### Strings

```java
package vip.proyi.guava.util;

import com.google.common.base.CharMatcher;
import com.google.common.base.Charsets;
import com.google.common.base.Strings;
import org.junit.Test;

import java.nio.charset.Charset;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.equalTo;
import static org.hamcrest.Matchers.nullValue;

public class StringsTest {
    @Test
    public void testStringsMethod() {
        assertThat(Strings.emptyToNull(""), nullValue());
        assertThat(Strings.nullToEmpty(null), equalTo(""));
        assertThat(Strings.nullToEmpty("hello"), equalTo("hello"));
        // 共同前缀
        assertThat(Strings.commonPrefix("Hello", "Hit"), equalTo("H"));
        assertThat(Strings.commonPrefix("Hello", "Xit"), equalTo(""));
        // 共同后缀
        assertThat(Strings.commonSuffix("Hello", "Echo"), equalTo("o"));
        assertThat(Strings.repeat("Hello", 3), equalTo("HelloHelloHello"));


        assertThat(Strings.isNullOrEmpty(null), equalTo(true));
        assertThat(Strings.isNullOrEmpty(""), equalTo(true));

        assertThat(Strings.padStart("hello", 3, 'H'), equalTo("hello"));
        // 自动从头部填充
        assertThat(Strings.padStart("hello", 9, 'H'), equalTo("HHHHhello"));
        // 自动从尾部填充
        assertThat(Strings.padEnd("hello", 9, 'H'), equalTo("helloHHHH"));
    }

    @Test
    public void testCharsets() {
        Charset charset = Charset.forName("UTF-8");
        // Charsets.UTF_8 相当于 Charset.forName("UTF-8")
        assertThat(Charsets.UTF_8, equalTo(charset));
    }

    @Test
    public void testCharMatcher() {
        // 匹配数字
        assertThat(CharMatcher.javaDigit().matches('5'), equalTo(true));
        assertThat(Character.isDigit('5'), equalTo(true));
        assertThat(Character.isDigit('x'), equalTo(false));

        // 匹配字符个数
        assertThat(CharMatcher.is('G').countIn("Google guava is a java class libray"), equalTo(1));
        assertThat(CharMatcher.is('a').countIn("Google guava is a java class libray"), equalTo(7));

        // 将空格替换， replacement不可为''
        assertThat(CharMatcher.breakingWhitespace().collapseFrom("     hello world      ", ' '), equalTo(" hello world "));
        assertThat(CharMatcher.breakingWhitespace().collapseFrom("     hello world      ", '*'), equalTo("*hello*world*"));

        // 将空格和数字从字符串中移除
        assertThat(CharMatcher.javaDigit().or(CharMatcher.whitespace()).removeFrom("hello 1234 5678 world"), equalTo("helloworld"));
        // 保留空格和数字
        assertThat(CharMatcher.javaDigit().or(CharMatcher.whitespace()).retainFrom("hello 1234 5678 world"), equalTo(" 1234 5678 "));
    }
}
```

