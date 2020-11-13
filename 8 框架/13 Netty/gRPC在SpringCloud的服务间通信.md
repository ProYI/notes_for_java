### gRPC在微服务框架SpringCloud中应用

在微服务开发中，服务间调用一般有两种方式：

1、以OpenFeign为代表是封装HTTP协议进行通信

2、底层使用TCP协议的RPC`远程服务调用`的方式，例如Dubbo、gRPC等

这两种方式没有绝对的优劣势，本质上还是继承了协议不同的优劣势。比如服务间发送HTTP请求时简单、易于理解，测试验证方便，但是通信消息内容冗长，消息连接资源开销大；RPC通信相较于HTTP通信，消息内容小，资源消耗少，因此在对低延迟通信部分有广泛的应用

gRPC就是服务间通信中应用比较广泛的RPC框架

<h4>gRPC工具包</h4>

开源项目grpc-spring-boot-starter是一个高度封装的开源项目，便于在Spring Cloud项目中开发应用

```xml
<dependency>
    <groupId>net.devh</groupId>
    <artifactId>grpc-server-spring-boot-starter</artifactId>
    <version>x.x.x.RELEASE</version>
</dependency>
```



<h4>练习Demo</h4>

<h5>定义gRPC接口</h5>

基于protobuf来声明数据模型和RPC接口服务，创建helloworld.proto文件，便于gRPC服务端和客户端使用

```protobuf
syntax = "proto3";

option java_multiple_files = true;
option java_package = "org.springboot.grpc.lib";
option java_outer_classname = "HelloWorldProto";

service Simple {
    rpc SayHello (HelloRequest) returns (HelloReply) {
    }
}

message HelloRequest {
    string name = 1;
}

message HelloReply {
    string message = 1;
}
```

根据proto的命令可以转换成对应的语言的代码，生成java代码，也可以借助maven插件，在编译时自动生成

通过mavent插件，可以在pom.xml中增加如下依赖：

```xml
<properties>
		<os.plugin.version>1.5.0.Final</os.plugin.version>
		<protobuf.plugin.version>0.5.0</protobuf.plugin.version>
		<protoc.version>3.3.0</protoc.version>
	</properties>
<build>
		<extensions>
			<extension>
				<groupId>kr.motd.maven</groupId>
				<artifactId>os-maven-plugin</artifactId>
				<version>${os.plugin.version}</version>
			</extension>
		</extensions>
		<plugins>
			<plugin>
				<groupId>org.xolstice.maven.plugins</groupId>
				<artifactId>protobuf-maven-plugin</artifactId>
				<version>${protobuf.plugin.version}</version>
				<configuration>
					<protocArtifact>com.google.protobuf:protoc:${protoc.version}:exe:${os.detected.classifier}</protocArtifact>
					<pluginId>grpc-java</pluginId>
					<pluginArtifact>io.grpc:protoc-gen-grpc-java:${grpc.version}:exe:${os.detected.classifier}</pluginArtifact>
				</configuration>
				<executions>
					<execution>
						<goals>
							<goal>compile</goal>
							<goal>compile-custom</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
```

注意：如果IDEA的maven插件直接去找`Plugins` -> `compiler`是有可能只生成实体，不生成service代码的，所以可以在maven项目中使用`Lifecycle` -> `compile`即可正确生成

或者直接在`Terminal`进入项目后输入`mvn general`编译过后即可在`target` -> `generated-sources` -> `protobuf`文件夹下找到生成文件

<h5>gRPC服务端</h5>

实现 gRPC server 的业务逻辑，使用注解**@GrpcService**定义gRPC服务端

```java
@GrpcService
public class GrpcServerService extends SimpleGrpc.SimpleImplBase {
    @Override
    public void sayHello(HelloRequest request, StreamObserver<HelloReply> responseObserver) {
        System.out.println("GrpcServerService...");
        HelloReply reply = HelloReply.newBuilder().setMessage("Hello ==> " + request.getName()).build();
        responseObserver.onNext(reply);
        responseObserver.onCompleted();
    }
}
```

gRPC 的 host 跟 port ，默认的监听的 host 是 0.0.0.0，默认的 port 是 9090，配置为0将会自动分配未使用的端口

```yaml
grpc:
  server:
    port: 0
```



<h5>gRPC客户端</h5>

客户端通过使用注解**@GrpcClient**来调用服务端接口

```java
@Service
public class GrpcClientService {

    @GrpcClient("spring-boot-grpc-server")
    private SimpleGrpc.SimpleBlockingStub simpleBlockingStub;

    public String sendMessage(String name) {
        try {
            HelloReply response = simpleBlockingStub.sayHello(HelloRequest.newBuilder().setName(name).build());
            return response.getMessage();
        } catch (final StatusRuntimeException e) {
            return "FAILED with " + e.getStatus().getCode();
        }
    }
}
```

spring-boot-grpc-server，即：服务端应用名，结合注册中心，通过服务名将会找到服务端的ip，进行通信，实际上是netty通信。

配置文件加入

```yaml
grpc:
  client:
    spring-boot-grpc-server:
      enableKeepAlive: true
      keepAliveWithoutCalls: true
      negotiationType: plaintext
```

感谢作者【xcbeyond】提供了完整的demo代码，参考地址![https://github.com/xcbeyond/spring-boot-grpc.git]