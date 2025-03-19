

用快递包裹的比喻解释四种 POST 数据传递方式：

---

### **一、POST Text Body（纯文本快递）**
**场景**：寄送一封信（纯文本）或一份标准格式合同（JSON/XML）  
**技术细节**：
- **Content-Type**：`text/plain`（纯文本）或 `application/json`（JSON）
- **后端接收方式**：`@RequestBody`（Spring Boot）
- **Postman 设置**：  
  ```text
  Body → raw → 选择 JSON/text
  输入内容：{"name":"张三","age":25}
  ```

---

### **二、POST Parameters Body（表单快递单）**
**场景**：填写快递单上的标准信息（键值对）  
**技术细节**：
- **Content-Type**：`application/x-www-form-urlencoded`
- **数据格式**：`key1=value1&key2=value2`
- **后端接收方式**：`@RequestParam`（Spring Boot）
- **Postman 设置**：  
  ```text
  Body → x-www-form-urlencoded
  输入键值对：
  name=张三
  age=25
  ```

---

### **三、POST Form with Text Field（带文字的表单包裹）**
**场景**：在快递包裹里附带一张手写便签（文本字段）  
**技术细节**：
- **Content-Type**：`multipart/form-data`
- **适用场景**：混合文本和文件（但此处仅文本）
- **后端接收方式**：`@RequestPart` 或 `@RequestParam`
- **Postman 设置**：  
  ```text
  Body → form-data
  添加文本字段：
  KEY：remark，VALUE：请轻拿轻放
  ```

---

### **四、POST Form with File Field（带文件的表单包裹）**
**场景**：快递包裹里放一本书（文件）  
**技术细节**：
- **Content-Type**：`multipart/form-data`
- **后端接收方式**：`@RequestPart MultipartFile file`
- **Postman 设置**：  
  ```text
  Body → form-data
  添加文件字段：
  KEY：file，VALUE：选择文件 → 类型选File
  ```

---

### **对比总结表**
| **类型**               | **Content-Type**             | **典型用途**         | **后端接收方法**               | **请求示例**                     |
|------------------------|------------------------------|----------------------|--------------------------------|----------------------------------|
| POST Text Body         | application/json             | API 接口传复杂数据   | @RequestBody User user         | {"name":"张三"}                  |
| POST Parameters Body   | x-www-form-urlencoded        | 传统表单提交         | @RequestParam String name      | name=张三&age=25                |
| POST Form Text Field   | multipart/form-data          | 带说明的文件上传表单 | @RequestParam String remark    | KEY:remark → 请轻拿轻放         |
| POST Form File Field   | multipart/form-data          | 文件上传             | @RequestPart MultipartFile file| KEY:file → 选择文件             |

---

### **常见错误避坑指南**
1. **Content-Type 不匹配**  
   - 用 JSON 数据但没设置 `Content-Type: application/json` → 后端无法解析
   - 用 `multipart/form-data` 但后端用 `@RequestBody` → 报错 415

2. **文件上传限制**  
   Spring Boot 默认限制文件大小（1MB），需在 `application.properties` 调整：
   ```properties
   spring.servlet.multipart.max-file-size=10MB
   spring.servlet.multipart.max-request-size=10MB
   ```

3. **中文乱码问题**  
   对于 `x-www-form-urlencoded`，需确保后端编码正确：
   ```java
   @PostMapping(produces = "application/json;charset=UTF-8")
   ```

---

**最终口诀**：  
- 传对象 → JSON（Text Body）  
- 传简单参数 → x-www-form-urlencoded  
- 传文件 → multipart/form-data