## Action获取表单数据  
之前web阶段，提交表单到servlet里面，在servlet里面使用request对象里面的方法获取，通过getParameter，getParameterMap等方法  
提交表单到action，但是action没有request对象，不能直接使用request对象  

action获取表单提交数据主要三种方式  
1. 使用ActionContext类  
2. 使用ServletActionContext类  
3. 使用接口注入方式（很少使用）

### 使用ActionContext类获取  
> Map<String, Object> getParameters() 返回一个包含所有HttpServletRequest参数信息  
> static ActionContext getContext() 获取当前线程的ActionContext对象  
```java
package me.test.action;

import java.util.Map;

import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.ActionSupport;

public class FormActionDemo1 extends ActionSupport {

	@Override
	public String execute() throws Exception {
		
		//第一种方式 使用ActionContext类获取
		//1.获取ActionContext对象  
		ActionContext context = ActionContext.getContext();
		
		//2.调用方法得到表单数据（返回map集合）
		//key是表单输入项name属性值，value是输入的值
		Map<String, Object> map = context.getParameters();
		for ( Map.Entry<String, Object> entry : map.entrySet()) {
			String key = entry.getKey();
			//value是以数组的形式存储的：因为输入项里面可能有复选框的情况
			Object value[] = (Object[]) entry.getValue(); 
		}
		return NONE;
	}
}
```
表单提交中文不会有乱码，在struts2中的常量帮我们封装了，表单提交方式是post，提交中文不会有问题  

### 使用ServletActionContext类获取
> static HttpServletRequest getRequest()：获取web应用的HttpServletRequest对象  
> static HttpServletResponse getResponse()：获取web应用的HttpServletResponse对象  
> static ServletContext getServletContext()：获取web应用的ServletContext对象  
> static PageContext getPageContext()：获取web应用的PageContext对象  
```java
package me.test.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.struts2.ServletActionContext;

import com.opensymphony.xwork2.ActionSupport;

public class FormActionDemo2 extends ActionSupport {

	@Override
	public String execute() throws Exception {
		
		//第二种方式 使用ServletActionContext类获取
		//1.获取request、response对象  
		HttpServletRequest request = ServletActionContext.getRequest();
		HttpServletResponse response = ServletActionContext.getResponse();
		
		//2.调用request方法得到结果
		String username = request.getParameter("username");
		
		return NONE;
	}
}
```
### 使用接口注入（了解）
action实现接口，得到request对象  
```java
package me.test.action;

import javax.servlet.http.HttpServletRequest;

import org.apache.struts2.interceptor.ServletRequestAware;

import com.opensymphony.xwork2.ActionSupport;

/*
 * 使用接口注入的方式
 * 实现ServletRequestAware接口
 */
public class FormActionDemo3 extends ActionSupport implements ServletRequestAware{

	private HttpServletRequest request;
	@Override
	public void setServletRequest(HttpServletRequest request) {
		this.request = request;		
	}
	
	public String execute() throws Exception {

		//调用request方法得到结果
		String username = request.getParameter("username");
		
		return NONE;
	}
}
```
### 在action操作域对象
我们做个列表功能，查出所有的list集合，要把list放到域对象中，页面中要取到，放到域对象里边，servlet中直接放，action里边用ServletActionContent类主要功能是操作域对象

1 request、session、servletContext域对象

2 使用ServletActionContext类操作
```java
package me.test.action;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.apache.struts2.ServletActionContext;

import com.opensymphony.xwork2.ActionSupport;

public class FormActionDemo4 extends ActionSupport {

	@Override
	public String execute() throws Exception {
		
		//操作三个域对象
		//1.request域
		HttpServletRequest request = ServletActionContext.getRequest();
		request.setAttribute("req", "reqValue");
		
		//2.session域
		HttpSession session = request.getSession();
		session.setAttribute("sess", "sessValue");
		
		//3.ServletContext域
		ServletContext context = ServletActionContext.getServletContext();
		context.setAttribute("contextName", "contextValue");
		
		return NONE;
	}
}
```
## Struts2封装获取表单数据方式
### 原始方式获取表单封装到实体类对象
1. action获取到表单数据
2. 实体类new对象
3. 通过set方法将数据封装到对象中  
### 属性封装（会用）
直接把表单提交属性封装到action的属性里面  

**实现步骤**  
1. 在action成员变量位置定义变量  
    - 变量名称和表单输入项的name属性值一样  
2. 生成变量的set方法（把set和get方法都写出来）  
3. 使用属性封装获取表单数据到属性里面，不能把数据直接封装到实体类对象里面  
如果使用时还是要new对象，通过set方法封装到对象中  
```java
package me.test.action;

import com.opensymphony.xwork2.ActionSupport;

public class DataActionDemo1 extends ActionSupport {

	//1. 定义成员变量
	//变量名称和表单输入项的name属性值一样
	private String username;
	private String password;
	
	//2.生成变量的set/get方法
	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}


	@Override
	public String execute() throws Exception {
		
		return NONE;
	}
}
```
### 模型驱动封装（重点）
使用模型驱动方式，可以直接把表单数据封装到实体类对象里面

**实现步骤**  
1. action实现接口 ModelDriven  
2. 实现接口里面的方法 getModel方法（把创建对象返回）  
3. 在action里面创建实体类对象  
```java
package me.test.action;

import com.opensymphony.xwork2.ActionSupport;
import com.opensymphony.xwork2.ModelDriven;

import me.test.domain.User;

public class DataActionDemo2 extends ActionSupport implements ModelDriven<User> {

	//手动创建对象
	//前提要求：表单输入项name属性值和实体类属性名称一样
	private User user = new User();
	public User getModel() {
		//返回创建User对象
		return user;
	}

	public String execute() throws Exception {
		
		return NONE;
	}
}
```
**使用模型驱动和属性封装注意问题**：  
1. 在一个action中，获取表单数据可以属性封装，使用模型驱动封装  
2. 不能同时使用属性封装和模型驱动封装获取同一个表单数据  
3. 如果同时使用，只会执行模型驱动  
### 表达式封装（会用）
使用表达式封装可以把表单数据封装到实体类对象里面  
**实现过程**  
1. 在action里面声明实体类  
2. 生成实体类变量的set和get方法  
3. 在表单输入项的name属性值里面写表达式形式  
```java
package me.test.action;

import com.opensymphony.xwork2.ActionSupport;

import me.test.domain.User;

public class DataActionDemo3 extends ActionSupport {

	//1. 声明实体类
	private User user;
	//2. 生成实体类变量的set/get方法
	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}

	public String execute() throws Exception {
		
		return NONE;
	}
}
```
提交表单
```jsp
<form action="${pageContext.request.contextPath}/data3.action" method="post">
    username:<input type="text" name="user.username"/><br/>
    password:<input type="password" name="user.password"/><br/>
</form>
```
### 比较表达式封装和模型驱动封装
相同点：使用表达式封装和模型驱动封装都可以把数据封装到实体类对象里面  
不同点：  
1. 使用模型驱动只能把数据封装到一个实体类对象里面
    - 在一个action里面不能使用模型驱动把数据封装到不同的实体类对象里面  
2.  使用表达式封装可以把数据封装到不同的实体类对象里面  
    action中声明两个对象，jsp表达式选择不同对象的不同字段进行封装就可以将数据封装到不同对象中了  
### 封装数据到集合里面（会用）
* 封装数据到List集合  
* 封装数据到Map集合  
不管封装到list还是map，方法都类似于 **表达式封装**，具体如何实现百度  