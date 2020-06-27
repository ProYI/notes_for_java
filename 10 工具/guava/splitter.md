### splitter的使用

joiner是guava中操作字符分割的工具类，下面是常用的测试方法

```java
package vip.proyi.guava.util;

import com.google.common.base.Splitter;
import org.junit.Test;

import java.util.List;
import java.util.Map;
import java.util.regex.Pattern;

import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.CoreMatchers.notNullValue;
import static org.hamcrest.MatcherAssert.assertThat;

public class SplitterTest {
    @Test
    public void testSplitOnSplit() {
        List<String> result = Splitter.on("|").splitToList("hello|world");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(2));
        assertThat(result.get(0), equalTo("hello"));
        assertThat(result.get(1), equalTo("world"));
    }

    @Test
    public void testSplit_On_Split_OmitEmpty() {
        List<String> result = Splitter.on("|").splitToList("hello|world|||");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(5));
        result = Splitter.on("|").omitEmptyStrings().splitToList("hello|world|||");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(2));
    }

    @Test
    public void testSplit_On_Split_OmitEmpty_TrimResult() {
        List<String> result = Splitter.on("|").omitEmptyStrings().splitToList("hello | world|||");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(2));
        assertThat(result.get(0), equalTo("hello "));
        assertThat(result.get(1), equalTo(" world"));

        result = Splitter.on("|").trimResults().omitEmptyStrings().splitToList("hello | world|||");
        assertThat(result.get(0), equalTo("hello"));
        assertThat(result.get(1), equalTo("world"));
    }

    /**
     * aaaabbbbccccdddd 每4个字符进行分割
     */
    @Test
    public void testSplitFixLength() {
        List<String> result = Splitter.fixedLength(4).splitToList("aaaabbbbccccdddd");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(4));
        assertThat(result.get(0), equalTo("aaaa"));
        assertThat(result.get(3), equalTo("dddd"));
    }

    @Test
    public void testSplitOnSplitLimit() {
        List<String> result = Splitter.on("#").limit(3).splitToList("hello#world#java#google#guava");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(3));
        assertThat(result.get(0), equalTo("hello"));
        assertThat(result.get(1), equalTo("world"));
        assertThat(result.get(2), equalTo("java#google#guava"));
    }

    /**
     * 正则字符串
     */
    @Test
    public void testSplitOnPatternString() {
        List<String> result = Splitter.onPattern("\\|").trimResults().omitEmptyStrings().splitToList("hello | world|||");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(2));
        assertThat(result.get(0), equalTo("hello"));
        assertThat(result.get(1), equalTo("world"));
    }
    /**
     * 正则表达式
     */
    @Test
    public void testSplitOnPattern() {
        List<String> result = Splitter.on(Pattern.compile("\\|")).trimResults().omitEmptyStrings().splitToList("hello | world|||");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(2));
        assertThat(result.get(0), equalTo("hello"));
        assertThat(result.get(1), equalTo("world"));
    }

    @Test
    public void testSplitOnSplitToMap() {
        Map<String, String> result = Splitter.on(Pattern.compile("\\|")).trimResults().omitEmptyStrings()
                .withKeyValueSeparator("=").split("hello=HELLO | world=WORLD|||");
        assertThat(result, notNullValue());
        assertThat(result.size(), equalTo(2));
        assertThat(result.get("hello"), equalTo("HELLO"));
        assertThat(result.get("world"), equalTo("WORLD"));
    }
}
```

