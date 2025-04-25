[[Buffer代码解释|Buffer代码解释]]
```java
package NIO;

import java.nio.ByteBuffer;
import java.nio.CharBuffer;

public class NIO_Test {
    public static void main(String[] args) {
        // 演示ByteBuffer的基本操作
        byteBufferDemo();
        
        // 演示CharBuffer的基本操作
        charBufferDemo();
    }

    public static void byteBufferDemo() {
        System.out.println("=== ByteBuffer Demo ===");
        
        // 创建一个容量为8的ByteBuffer
        ByteBuffer buffer = ByteBuffer.allocate(8);
        
        // 写入数据
        System.out.println("写入数据前 - Position: " + buffer.position() + ", Limit: " + buffer.limit());
        buffer.put((byte) 1);
        buffer.put((byte) 2);
        buffer.put((byte) 3);
        System.out.println("写入数据后 - Position: " + buffer.position() + ", Limit: " + buffer.limit());

        // 切换到读模式
        buffer.flip();
        System.out.println("flip()后 - Position: " + buffer.position() + ", Limit: " + buffer.limit());

        // 读取数据
        while (buffer.hasRemaining()) {
            System.out.println("读取到: " + buffer.get());
        }
        
        // 清空buffer
        buffer.clear();
        System.out.println("clear()后 - Position: " + buffer.position() + ", Limit: " + buffer.limit());
    }

    public static void charBufferDemo() {
        System.out.println("\n=== CharBuffer Demo ===");
        
        // 创建一个CharBuffer
        CharBuffer buffer = CharBuffer.allocate(6);
        
        // 写入字符串
        String text = "Hello";
        buffer.put(text);
        System.out.println("写入数据后 - Position: " + buffer.position() + ", Limit: " + buffer.limit());

        // 切换到读模式
        buffer.flip();
        
        // 读取所有字符
        System.out.print("读取的字符: ");
        while (buffer.hasRemaining()) {
            System.out.print(buffer.get());
        }
        System.out.println();

        // 重绕buffer，重新读取
        buffer.rewind();
        
        // 批量读取到字符数组
        char[] chars = new char[buffer.remaining()];
        buffer.get(chars);
        System.out.println("重新读取: " + new String(chars));
        
        // 压缩未读的数据
        buffer.compact();
        
        // 标记和重置示例
        buffer.clear();
        buffer.put("ABCDEF");
        buffer.flip();
        buffer.get();  // 读取'A'
        buffer.mark();  // 标记当前位置
        buffer.get();  // 读取'B'
        buffer.get();  // 读取'C'
        buffer.reset(); // 重置到标记位置
        System.out.println("重置后读取: " + buffer.get()); // 应该再次读取'B'
    }
}
```
这个示例展示了 NIO Buffer 的主要特性：

1. Buffer 的基本属性：
    - position: 当前位置
    - limit: 可读写的限制
    - capacity: 容量
2. 主要操作：
    - `allocate()`: 分配一个新的 Buffer
    - `put()`: 写入数据
    - `get()`: 读取数据
    - `flip()`: 从写模式切换到读模式
    - `clear()`: 清空整个buffer
    - `rewind()`: 重置position到开始位置
    - `mark()`: 标记当前位置
    - `reset()`: 重置到标记位置
    - `compact()`: 压缩buffer
3. 两种类型的Buffer示例：
    - ByteBuffer: 处理字节数据
    - CharBuffer: 处理字符数据

运行这个程序会输出类似这样的结果：
```java
=== ByteBuffer Demo ===
写入数据前 - Position: 0, Limit: 8
写入数据后 - Position: 3, Limit: 8
flip()后 - Position: 0, Limit: 3
读取到: 1
读取到: 2
读取到: 3
clear()后 - Position: 0, Limit: 8

=== CharBuffer Demo ===
写入数据后 - Position: 5, Limit: 6
读取的字符: Hello
重新读取: Hello
重置后读取: B
```