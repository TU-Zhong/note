

**代码插入消毒（Input Sanitization）** 是一种安全措施，用于防止恶意用户通过输入内容向网页注入可执行代码（如 JavaScript、HTML 或 CSS），从而避免 **XSS（跨站脚本攻击）** 等安全漏洞。以下是通俗易懂的解释和示例：

---

### **一、为什么需要消毒？**
#### **攻击场景示例**
假设用户在一个评论区输入以下内容：
```html
<script>alert('你的账户已被盗！');</script>
<img src="x" onerror="stealCookies()">
```
- **未消毒时**：如果直接将这个内容通过 `innerHTML` 插入页面，`<script>` 标签和 `onerror` 事件会执行恶意代码。
- **消毒后**：内容会被处理为纯文本或安全的 HTML，如：
  ```html
  &lt;script&gt;alert('你的账户已被盗！');&lt;/script&gt;
  <img src="x"> <!-- 移除了 onerror 属性 -->
  ```

---

### **二、消毒的核心步骤**
#### 1. **移除危险标签**
   - 删除 `<script>`、`<iframe>`、`<style>` 等可能执行代码的标签。
#### 2. **过滤危险属性**
   - 移除 `onclick`、`onerror`、`href="javascript:..."` 等事件处理器和协议。
#### 3. **转义特殊字符**
   - 将 `<` 转义为 `&lt;`、`>` 转义为 `&gt;`，防止标签被解析。

---

### **三、消毒实现方式**
#### **1. 使用消毒库（推荐）**
如 **[DOMPurify](https://github.com/cure53/DOMPurify)**，它会自动过滤危险内容：
```javascript
import DOMPurify from 'dompurify';

const userInput = '<img src="x" onerror="alert(1)">';
const cleanHTML = DOMPurify.sanitize(userInput); // 输出: <img src="x">

element.innerHTML = cleanHTML;
```

#### **2. 手动转义（简单场景）**
对用户输入的纯文本进行转义：
```javascript
function escapeHTML(text) {
  return text.replace(/</g, '&lt;').replace(/>/g, '&gt;');
}

const userInput = '<script>alert(1)</script>';
element.textContent = escapeHTML(userInput); // 显示为文本，而非执行代码
```

---

### **四、消毒前后对比**
| **用户输入**                     | **未消毒直接显示**               | **消毒后显示**                   |
|----------------------------------|----------------------------------|----------------------------------|
| `<script>alert(1)</script>`      | 弹窗显示 "1"（代码执行）          | 显示为文本：`&lt;script&gt;...`   |
| `<img src="x" onerror="hack()">` | 触发 `hack()` 函数（加载失败时）  | 仅保留 `<img src="x">`            |
| `<a href="javascript:steal()">点我</a>` | 点击后执行 `steal()`          | 移除 `javascript:` 协议，变为 `<a>` 普通链接 |

---

### **五、消毒最佳实践**
1. **永远不要信任用户输入**：所有来自用户的内容（评论、表单、URL 参数）必须消毒。
2. **根据场景选择消毒强度**：
   - **纯文本显示**：用 `textContent` 或手动转义。
   - **富文本编辑**（如博客评论）：使用消毒库（如 DOMPurify）允许安全标签（如 `<b>`、`<a>`）。
3. **在服务器和客户端双重消毒**：
   - 前端消毒：防止即时攻击。
   - 后端消毒：防止绕过前端的恶意请求。

---

### **六、常见消毒工具**
| 工具                | 用途                     | 特点                          |
|---------------------|--------------------------|-------------------------------|
| **DOMPurify**       | HTML 消毒                | 轻量级，支持白名单配置          |
| **sanitize-html**   | Node.js 后端 HTML 消毒    | 可定制允许的标签和属性          |
| **validator.js**    | 输入格式验证+消毒         | 结合数据校验和简单转义          |

---

### **七、一句话总结**
**代码插入消毒 = 给用户输入“洗澡”**，洗掉可能导致代码执行的“脏东西”（如恶意标签和脚本），确保页面安全！ 🛡️