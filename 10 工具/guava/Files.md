### Files的使用
```java
package vip.proyi.guava.io;

import com.google.common.base.Charsets;
import com.google.common.base.Joiner;
import com.google.common.hash.HashCode;
import com.google.common.hash.Hashing;
import com.google.common.io.FileWriteMode;
import com.google.common.io.Files;
import com.google.common.io.LineProcessor;
import org.junit.After;
import org.junit.Test;

import java.io.File;
import java.io.IOException;
import java.nio.file.Paths;
import java.nio.file.StandardCopyOption;
import java.util.ArrayList;
import java.util.List;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.equalTo;

public class FilesTest {
    private final String BASE = "/src/test/java/vip/proyi/guava/io";
    private final String SOURCE_FILE = BASE + "/source.txt";
    private final String TARGET_FILE = BASE + "/target.txt";

    @Test
    public void testCopyFileWithGuava() throws IOException {
        File targetFile = new File(TARGET_FILE);

        Files.copy(new File(SOURCE_FILE), targetFile);
        assertThat(targetFile.exists(), equalTo(true));

        HashCode sourceHashCode = Files.asByteSource(new File(SOURCE_FILE)).hash(Hashing.sha256());
        HashCode targetHashCode = Files.asByteSource(targetFile).hash(Hashing.sha256());
        assertThat(sourceHashCode.toString(), equalTo(targetHashCode.toString()));
    }

    @Test
    public void testCopyFileWithJDKNio2() throws IOException {
        java.nio.file.Files.copy(
                Paths.get(BASE, "source.txt"),
                Paths.get(BASE, "target.txt"),
                StandardCopyOption.REPLACE_EXISTING
        );
        assertThat(new File(TARGET_FILE).exists(), equalTo(true));
    }

    @Test
    public void testMoveFile() throws IOException {
        try {
            Files.move(new File(SOURCE_FILE), new File(TARGET_FILE));
            assertThat(new File(TARGET_FILE).exists(), equalTo(true));
            assertThat(new File(SOURCE_FILE).exists(), equalTo(false));
        } finally {
            Files.move(new File(TARGET_FILE), new File(SOURCE_FILE));
        }
    }

    @Test
    public void testToString() throws IOException {
        final String expectedString = "这是一个测试用的文本文件\n" +
                "测试Guava的文件流处理功能";
        List<String> strings = Files.readLines(new File(SOURCE_FILE), Charsets.UTF_8);
        String result = Joiner.on("\n").join(strings);
        assertThat(result, equalTo(expectedString));

    }

    @Test
    public void testToProcessString() throws IOException {
//        Files.readLines(new File(SOURCE_FILE), Charsets.UTF_8);
        LineProcessor<List<Integer>> lineProcessor = new LineProcessor<List<Integer>>() {
            private final List<Integer> lengthList = new ArrayList<>();

            @Override
            public boolean processLine(String line) throws IOException {
                lengthList.add(line.length());
                return true;
            }

            @Override
            public List<Integer> getResult() {
                return lengthList;
            }
        };
        List<Integer> result = Files.asCharSource(new File(SOURCE_FILE), Charsets.UTF_8).readLines(lineProcessor);

        System.out.println(result);
    }

    @Test
    public void testFileSha() throws IOException {
        File file = new File(SOURCE_FILE);
        HashCode hashCode = Files.asByteSource(file).hash(Hashing.sha256());
        System.out.println(hashCode);
    }

    @Test
    public void testFileWrite() throws IOException {
        // 覆盖原有内容
        String testPath = BASE + "/test.txt";
        File testFile = new File(testPath);
        testFile.deleteOnExit();
        String content1 = "content 1";
        Files.asCharSink(testFile, Charsets.UTF_8).write(content1);
        String actully = Files.asCharSource(testFile, Charsets.UTF_8).read();
        assertThat(actully, equalTo(content1));


        // 如果存在内容，则累加模式 APPEND模式
        String content2 = "content 2";
        Files.asCharSink(testFile, Charsets.UTF_8, FileWriteMode.APPEND).write(content2);
        actully = Files.asCharSource(testFile, Charsets.UTF_8).read();
        assertThat(actully, equalTo(content1 + content2));
    }

    @Test
    public void testTouchFile() throws IOException {
        File touchFile = new File(BASE + "/touch.txt");
        touchFile.deleteOnExit();
        Files.touch(touchFile);
        assertThat(touchFile.exists(), equalTo(true));
    }

    // 递归
    @Test
    public void testRecursive() {
        List<File> list = new ArrayList<>();
        this.recursiveList(new File(BASE), list);
        list.forEach(System.out::println);
    }

    private void recursiveList(File root, List<File> fileList) {
        if (root.isHidden()) {
            return;
        }
        fileList.add(root);
        if (!root.isFile()) {
            File[] files = root.listFiles();
            for (File f : files) {
                recursiveList(f, fileList);
            }
        }
    }

    // 遍历时深度优先 先输出文件夹 再遍历文件夹下文件
    @Test
    public void testTreeFiles() {
        File root = new File("/Volumes/HDD/code/git/java/mytest/guava_test/src/test/java");
        Iterable<File> files = Files.fileTraverser().depthFirstPreOrder(root);
        files.forEach(System.out::println);

        System.out.println("----------------------------");
        Iterable<File> files2 = Files.fileTraverser().depthFirstPreOrder(root);
        files2.forEach(file -> {
            if (file.isFile()) {
                System.out.println(file);
            }
        });
    }

    // 遍历时深度优先 先输出文件 再输出文件夹
    @Test
    public void testTreeFilesDepthFirstPostOrder() {
        File root = new File("/Volumes/HDD/code/git/java/mytest/guava_test/src/test/java");
        Iterable<File> files = Files.fileTraverser().depthFirstPostOrder(root);
        files.forEach(System.out::println);
    }

    // 遍历时广度优先 先输出所有文件夹
    @Test
    public void testTreeFilesBreadthFirst() {
        File root = new File("/Volumes/HDD/code/git/java/mytest/guava_test/src/test/java");
        Iterable<File> files = Files.fileTraverser().breadthFirst(root);
        files.forEach(System.out::println);
    }


    @After
    public void tearDown() throws IOException {
        File targetFile = new File(TARGET_FILE);
        if(targetFile.exists()) {
            targetFile.delete();
        }
    }
}
```

