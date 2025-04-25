

在 Java 中，提到 **IO（Input/Output）**，通常会联想到以下核心概念和组件：

---

### **一、核心概念**
1. **数据流动**：程序与外部的数据交换（文件、网络、键盘输入、屏幕输出等）
2. **流（Stream）**：比喻为数据传输的管道，分为：
   - **字节流**（`InputStream`/`OutputStream`）：处理**所有类型**数据（如图片、视频）
   - **字符流**（`Reader`/`Writer`）：专门处理**文本数据**（自动处理编码，如 UTF-8）
3. **阻塞 vs 非阻塞**：传统 IO 是阻塞的，NIO 支持非阻塞操作（提高并发性能）。

---

### **二、必知类与接口**
#### **1. 基础类（`java.io` 包）**
| 类型       | 类/接口                | 用途                         | 示例场景               |
|------------|------------------------|------------------------------|------------------------|
| **字节流** | `FileInputStream`      | 从文件读取字节               | 复制图片、视频         |
|            | `FileOutputStream`     | 向文件写入字节               | 保存二进制数据         |
| **字符流** | `FileReader`/`FileWriter` | 读写文本文件（自动处理编码） | 读取配置文件、日志文件 |
| **缓冲流** | `BufferedInputStream`  | 加快读取速度（批量加载数据） | 处理大文件             |
|            | `BufferedReader`       | 逐行读取文本                 | 解析 CSV 文件          |
| **对象流** | `ObjectInputStream`    | 序列化对象                   | 保存用户会话信息       |
|            | `ObjectOutputStream`   | 反序列化对象                 | 恢复程序状态           |

#### **2. NIO 类（`java.nio` 包，Java 1.4+）**
| 类/接口          | 用途                               | 优势                     |
|------------------|------------------------------------|--------------------------|
| `Channel`        | 双向数据传输（替代传统流）         | 支持非阻塞 IO            |
| `ByteBuffer`     | 内存缓冲区管理                     | 高效操作数据块           |
| `Selector`       | 多路复用，监控多个 Channel 的事件  | 高并发服务器开发         |
| `Files`/`Paths`  | 简化文件操作（Java 7+）            | 替代 `File` 类，更易用   |

---

### **三、典型使用场景**
1. **文件操作**：
   ```java
   // 读取文本文件
   try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
       String line;
       while ((line = reader.readLine()) != null) {
           System.out.println(line);
       }
   }
   ```
2. **网络通信**：
   ```java
   // 使用 Socket 传输数据
   Socket socket = new Socket("example.com", 80);
   OutputStream out = socket.getOutputStream();
   out.write("GET / HTTP/1.1\r\n".getBytes());
   ```
3. **数据持久化**：
   ```java
   // 序列化对象到文件
   try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("user.dat"))) {
       oos.writeObject(new User("Alice", 25)); 
   }
   ```

---

### **四、关键注意事项**
1. **资源关闭**：必须关闭流（用 `try-with-resources` 自动管理）：
   ```java
   // 正确写法（自动关流）
   try (InputStream in = new FileInputStream("a.txt")) {
       // 操作流
   } // 自动调用 in.close()
   ```
2. **性能优化**：
   - 优先使用**缓冲流**（如 `BufferedInputStream`），减少磁盘 IO 次数。
   - 大文件处理避免一次性加载到内存。
3. **编码问题**：
   ```java
   // 指定编码读取文本
   new InputStreamReader(new FileInputStream("data.txt"), StandardCharsets.UTF_8);
   ```
4. **NIO 的优势**：
   - `Files.copy()` 比传统 IO 复制文件更快（底层使用系统级优化）。
   - `ByteBuffer` 支持内存映射文件（MappedByteBuffer），适合超大文件处理。

---

### **五、常见面试问题**
1. **IO vs NIO 的区别**？
   - IO 是阻塞的，流式传输；NIO 是非阻塞的，基于缓冲区 + 通道。
2. **什么是序列化？如何实现**？
   - 序列化是将对象转为字节流，需实现 `Serializable` 接口。
3. **字节流和字符流的区别**？
   - 字节流直接操作字节（8-bit），字符流操作 Unicode 字符（16-bit）。

---

**总结**：Java IO 是数据交互的桥梁，理解其分层设计（字节/字符流、缓冲、NIO）是开发高效可靠应用的关键。