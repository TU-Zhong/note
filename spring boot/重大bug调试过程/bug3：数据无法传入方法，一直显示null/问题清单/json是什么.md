

**JSON 就像快递单上的信息表**——用简单格式写明「什么东西，放在哪里」，让不同系统之间快速传递数据。

---

### **一、JSON 是什么？**
**1. 定义：**  
JSON（JavaScript Object Notation）是一种**轻量级的数据格式**，看起来像这样：
```json
{
  "name": "张三",
  "age": 25,
  "hobbies": ["打篮球", "看电影"],
  "address": {
    "city": "北京",
    "street": "海淀区中关村"
  }
}
```
- **结构**：由 `{}` 包裹对象（键值对），`[]` 包裹数组，数据用 `,` 分隔。
- **特点**：纯文本、易读、易解析、占用空间小。

**2. 对比其他格式（秒懂版）**  
| 格式   | 例子                                      | 特点               |
|--------|------------------------------------------|--------------------|
| JSON   | `{"name": "张三"}`                       | 简洁，适合网络传输 |
| XML    | `<name>张三</name>`                      | 冗余，适合复杂结构 |
| CSV    | `张三,25,北京`                           | 简单，适合表格数据 |

---

### **二、JSON 有什么用？**
**核心作用：让不同系统「说同一种语言」**  
- **场景一：前后端通信**  
  前端（网页/App）→ 发 JSON 给后端 → 后端解析后存数据库  
  后端 → 返回 JSON 给前端 → 前端渲染页面  

- **场景二：配置文件**  
  VS Code 的 `settings.json`、Spring Boot 的 `application.json` 都用 JSON 配置参数。

- **场景三：跨平台数据交换**  
  Java 程序生成 JSON → 发送给 Python 程序 → Python 解析后处理数据。

---

### **三、JSON 用在哪儿？（真实场景举例）**
**1. Web 开发（90%的网站都在用）**  
- **用户登录**：  
  前端发送：
  ```json
  {"username": "user123", "password": "abc123"}
  ```
  后端返回：
  ```json
  {"code": 200, "message": "登录成功"}
  ```

**2. App 与服务器交互**  
- 微信获取用户信息时，服务器返回 JSON：
  ```json
  {
    "nickname": "小明",
    "avatar": "https://xxx.jpg",
    "openid": "o6_bmASZ6X7a..."
  }
  ```

**3. 存储配置信息**  
- 游戏存档用 JSON 保存进度：
  ```json
  {
    "level": 5,
    "coins": 1000,
    "unlockedSkills": ["冲刺", "二段跳"]
  }
  ```

**4. 大数据传输**  
- 天气 API 返回 JSON 格式数据：
  ```json
  {
    "city": "上海",
    "temperature": 28.5,
    "humidity": 65%,
    "forecast": ["晴", "多云", "小雨"]
  }
  ```

---

### **四、为什么不用普通文本而用 JSON？**
- **结构化**：明确区分字段名和值（比如 `"age": 25` 比 `年龄25` 更易解析）
- **跨语言**：几乎所有编程语言（Java、Python、JavaScript 等）都有 JSON 解析库
- **可扩展**：嵌套对象和数组，轻松表示复杂数据

---

### **五、手把手：如何生成/解析 JSON？**
**1. Java 中操作 JSON：**  
使用 `Jackson` 或 `Fastjson` 库：
```java
// 对象转 JSON
User user = new User("张三", 25);
String json = new ObjectMapper().writeValueAsString(user); 
// 结果：{"name":"张三","age":25}

// JSON 转对象
User user = new ObjectMapper().readValue(json, User.class);
```

**2. JavaScript 中操作 JSON：**  
```javascript
// 对象转 JSON
let user = {name: '张三', age: 25};
let json = JSON.stringify(user);

// JSON 转对象
let user = JSON.parse('{"name":"张三","age":25}');
```

---

### **六、JSON 的局限**
- **不支持注释**：不能在 JSON 中添加注释说明字段含义
- **数据类型有限**：只有字符串、数字、布尔值、数组、对象和 `null`
- **不适合存二进制数据**（如图片）→ 用 Base64 编码或单独传输

---

**总结一句话：JSON 是互联网时代的「数据普通话」，只要涉及系统间传数据，大概率会用到它！**