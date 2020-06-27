### joiner的使用

joiner是guava中操作字符拼接的工具类，下面是常用的测试方法

```java
package vip.proyi.guava.util;

import com.google.common.base.Joiner;
import com.google.common.io.Files;
import org.junit.Test;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

import static com.google.common.collect.ImmutableMap.of;
import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.CoreMatchers.sameInstance;
import static org.hamcrest.MatcherAssert.assertThat;
import static org.junit.Assert.fail;


public class JoinerTest {
     private final List<String> stringList = Arrays.asList(
             "google", "guava", "java", "scala", "kafka"
     );
    private final List<String> stringListWithNullValue = Arrays.asList(
            "google", "guava", "java", "scala", null
    );
    private final Map<String, String> stringMap = of("google", "guava", "java", "scala");

    @Test
    public void testJoinOnJoin() {
        String result = Joiner.on("#").join(stringList);
        assertThat(result, equalTo("google#guava#java#scala#kafka"));
    }

    @Test(expected = NullPointerException.class)
    public void testJoinOnJoinWithNullValue() {
        String result = Joiner.on("#").join(stringListWithNullValue);
        assertThat(result, equalTo("google#guava#java#scala#null"));
    }

    @Test()
    public void testJoinOnJoinWithNullValueButSkip() {
        String result = Joiner.on("#").skipNulls().join(stringListWithNullValue);
        assertThat(result, equalTo("google#guava#java#scala"));
    }

    @Test()
    public void testJoin_On_Join_With_NullValue_UseDefaultValue() {
        String result = Joiner.on("#").useForNull("default").join(stringListWithNullValue);
        assertThat(result, equalTo("google#guava#java#scala#default"));
    }

    @Test()
    public void testJoin_On_Append_To_StringBuilder() {
        final StringBuilder builder = new StringBuilder();
        StringBuilder resultBuilder = Joiner.on("#").useForNull("default").appendTo(builder, stringListWithNullValue);
        assertThat(resultBuilder, sameInstance(builder));
        assertThat(resultBuilder.toString(), equalTo("google#guava#java#scala#default"));
    }

    private final String targetFileName = "/path/guava_joiner_list.txt";
    @Test()
    public void testJoin_On_Append_To_Writer() {
        try (FileWriter writer = new FileWriter(new File(targetFileName))){
            Joiner.on("#").useForNull("default").appendTo(writer, stringListWithNullValue);
            assertThat(Files.isFile().test(new File(targetFileName)), equalTo(true));
        } catch (IOException e) {
            fail("append to the writer occur fatal error.");
        }
    }

    @Test
    public void testJoiningByStream_SkipNullValues() {
        String result = stringListWithNullValue.stream().filter(item -> item!=null&&!item.isEmpty()).collect(Collectors.joining("#"));
        assertThat(result, equalTo("google#guava#java#scala"));
    }

    @Test
    public void testJoiningByStream_WithDeafultValue() {
        String result = stringListWithNullValue.stream().map(item -> item==null||item.isEmpty()?"default":item).collect(Collectors.joining("#"));
        assertThat(result, equalTo("google#guava#java#scala#default"));
    }

    private final String targetMapFileName = "/path/guava_joiner_map.txt";
    @Test
    public void testJoinOnWithMap() {
        assertThat(Joiner.on("#").withKeyValueSeparator("=").join(stringMap), equalTo("google=guava#java=scala"));
        try (FileWriter writer = new FileWriter(new File(targetMapFileName))){
            Joiner.on("#").withKeyValueSeparator("=").appendTo(writer, stringMap);
            assertThat(Files.isFile().test(new File(targetMapFileName)), equalTo(true));
        } catch (IOException e) {
            fail("append to the writer occur fatal error.");
        }
    }
}
```

