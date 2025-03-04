

---

**错误原因：**  
在 JavaScript 中遇到 `Uncaught SyntaxError: Cannot use import statement outside a module` 错误，是因为你使用了 ES6 的模块导入语法（`import`），但当前环境**未启用模块支持**。以下是具体原因和解决方案：

---

### **一、浏览器环境**
#### **错误场景**
直接通过 `<script>` 标签加载含 `import` 的脚本：
```html
<script src="app.js"></script> <!-- 普通脚本 -->
```
而 `app.js` 中使用了 `import`：
```javascript
import { foo } from './module.js'; // 报错！
```

#### **解决方案**
在 `<script>` 标签中添加 `type="module"`，声明模块：
```html
<script type="module" src="app.js"></script> ✅
```

---

### **二、Node.js 环境**
#### **错误场景**
在 Node.js 中直接运行含 `import` 的代码（未配置模块类型）：
```bash
node app.js  # 报错！
```

#### **解决方案**
**方法1**：在 `package.json` 中添加 `"type": "module"`  
```json
{
  "name": "my-project",
  "type": "module", ✅
  "scripts": { ... }
}
```

**方法2**：将文件扩展名改为 `.mjs`  
```javascript
// 文件命名为 app.mjs
import { foo } from './module.mjs';
```
运行命令：
```bash
node app.mjs  ✅
```

---

### **三、常见问题**
#### **1. 混合使用 `require` 和 `import`**
- **错误代码**：
  ```javascript
  const fs = require('fs'); // CommonJS 语法
  import express from 'express'; // ES6 语法（报错）
  ```
- **解决方案**：统一使用一种模块系统，或通过工具（如 Babel）转换代码。

#### **2. 模块路径未正确设置**
- **错误代码**：
  ```javascript
  import { foo } from 'module'; // 未指定相对路径（如 './module.js'）
  ```
- **解决方案**：浏览器需使用相对路径（如 `'./module.js'`），Node.js 需安装模块或指定路径。

---

### **四、总结**
| **环境**      | **解决方案**                          | **注意事项**                     |
|---------------|---------------------------------------|---------------------------------|
| **浏览器**    | 添加 `<script type="module">`          | 本地测试需通过服务器（如 VSCode Live Server）运行 |
| **Node.js**   | 在 `package.json` 中设置 `"type": "module"` 或使用 `.mjs` 扩展名 | 确保依赖库支持 ES 模块           |
| **通用**      | 使用构建工具（Webpack、Vite、Babel）    | 适合复杂项目，支持混合模块语法     |

---

**示例对比：**

#### **浏览器正确用法**
```html
<!DOCTYPE html>
<html>
<body>
  <!-- 添加 type="module" -->
  <script type="module" src="app.js"></script> ✅
</body>
</html>
```

#### **Node.js 正确用法**
```javascript
// package.json
{
  "type": "module" ✅
}

// app.js
import fs from 'fs'; // 正常使用
```

---

通过正确配置模块系统，即可解决 `import` 语法报错问题！ 🚀