### Preconditions的使用

guava中进行断言的工具，下面是常用的测试方法

```java
package vip.proyi.guava.util;

import com.google.common.base.Preconditions;
import com.google.common.collect.ImmutableList;
import org.junit.Test;

import java.util.List;
import java.util.Objects;

import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.CoreMatchers.instanceOf;
import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.isA;


public class PreconditionsTest {

    @Test(expected = NullPointerException.class)
    public void testCheckNotNull() {
        checkNotNull(null);
    }

    private void checkNotNull(final List<String> list) {
        Preconditions.checkNotNull(list);
    }

    @Test
    public void testCheckNotNullWithMessage() {
        try {
            checkNotNullWithMessage(null);
        } catch (Exception e) {
            assertThat(e, is(instanceOf(NullPointerException.class)));
            assertThat(e, isA(NullPointerException.class));
            /*junit 4.13依赖的hamcrest-core 1.3 isA()方法存在bug*/
            assertThat(e.getMessage(), equalTo("This list should not be null"));
        }
    }

    private void checkNotNullWithMessage(final List<String> list) {
        Preconditions.checkNotNull(list, "This list should not be null");
    }

    @Test
    public void testCheckNotNullWithFormatMessage() {
        try {
            checkNotNullWithFormatMessage(null);
        } catch (Exception e) {
            assertThat(e, isA(NullPointerException.class));
            assertThat(e.getMessage(), equalTo("This list should not be null and the size must be 2"));
        }
    }

    private void checkNotNullWithFormatMessage(final List<String> list) {
        Preconditions.checkNotNull(list, "This list should not be null and the size must be %s", 2);
    }

    @Test
    public void testCheckArguments() {
        final String type = "A";
        try {
            Preconditions.checkArgument(type.equals("B"));
        } catch (Exception e) {
            assertThat(e, isA(IllegalArgumentException.class));
        }
    }

    @Test
    public void testCheckState() {
        try {
            final String state = "A";
            Preconditions.checkState(state.equals("B"), "The state is illegal");
        } catch (Exception e) {
            assertThat(e, isA(IllegalStateException.class));
        }
    }

    @Test
    public void testCheckIndex() {
        try {
            List<String> list = ImmutableList.of();
            Preconditions.checkElementIndex(10, list.size());
        } catch (Exception e) {
            assertThat(e, isA(IndexOutOfBoundsException.class));
        }
    }

    /**
     * java8中方法
     */
    @Test(expected = NullPointerException.class)
    public void testByObject() {
        Objects.requireNonNull(null);
    }

    @Test(expected = AssertionError.class)
    public void testAssert() {
        List<String> list = null;
        assert list != null;
    }

    @Test
    public void testAssertWithMessage() {
        try {
            List<String> list = null;
            assert list != null : "The list should not be null";
        } catch (Error e) {
            assertThat(e, isA(AssertionError.class));
            assertThat(e.getMessage(), equalTo("The list should not be null"));
        }
    }
}
```



