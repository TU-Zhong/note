

以下是关于 DOM 中 `textContent` 属性的 **详细知识点和示例**，用通俗易懂的方式解释：

---

### **一、textContent 是什么？**
`textContent` 是 DOM 元素的属性，用于 <mark style="background: #FFF3A3A6;">**获取或</mark><mark style="background: #FFF3A3A6;">设置元素及其所有子元素的纯文本内容**</mark>。  
**特点**：
- 返回所有子节点的文本（包括隐藏元素）  
- 自动忽略 HTML 标签  
- 不会解析 HTML 实体（如 `&amp;` 保持原样）  
- 高性能（不会触发页面重排）

---

### **二、基础用法**
#### 1. **获取文本内容**
```html
<div id="demo">
  Hello <span style="display:none">隐藏文本</span> World!
</div>
```
```javascript
const div = document.getElementById("demo");
console.log(div.textContent); // 输出："Hello 隐藏文本 World!"
```

#### 2. **设置文本内容**
[[会清空所有的子元素]]
```javascript
div.textContent = "新文本 <strong>不会解析标签</strong>";
console.log(div.innerHTML); // 输出："新文本 &lt;strong&gt;不会解析标签&lt;/strong&gt;"
```

---

### **三、对比 innerText**
| **特性**             | `textContent`              | `innerText`                  |
|----------------------|---------------------------|------------------------------|
| **获取隐藏文本**      | ✅ 包含隐藏元素内容         | ❌ 忽略隐藏元素内容            |
| **保留格式空白符**    | ✅ 保留换行、缩进等         | ❌ 合并空白符                  |
| **性能**             | 高（纯文本操作无重排）      | 低（需计算样式，可能触发重排）  |
| **XSS 安全**         | ✅ 自动转义 HTML 标签       | ✅ 自动转义 HTML 标签          |

---

### **四、常见应用场景**
#### 1. **动态更新纯文本**
```javascript
const button = document.querySelector("button");
button.textContent = "点击次数：0";
let count = 0;
button.addEventListener("click", () => {
  button.textContent = `点击次数：${++count}`;
});
```

#### 2. **清空元素内容**
```javascript
const list = document.getElementById("myList");
list.textContent = ""; // 移除所有子节点
```

#### 3. **解决空白符问题**
```html
<div>  
    前方       有    多余   空格  
</div>
```
```javascript
const one=document.querySelector("div");  
console.log(one.textContent);  
console.log(one.innerText);
```
首先，控制台输出信息在开发者工具中控制台可以看到
其次html<mark style="background: #FFF3A3A6;">文件中的多的空格浏览器渲染的时候会被默认折叠</mark>，意思是即使你的textContent确实有很多空格，但<mark style="background: #FFF3A3A6;">最后到浏览器页面上也就是没有多余空格的样式</mark>。
```
折叠规则：
- 连续的空白符（空格、换行、制表符）会被合并为一个空格
- 行首/行尾的空白符会被移除
- 换行符会被视为空格
```
textContent<mark style="background: #FFF3A3A6;">确实有多余空格</mark>（<mark style="background: #FFF3A3A6;">保留了文本全部原貌</mark>），但是由于默认渲染规则，在浏览器上也就没有对于空格了。
这些可以在开发者工具的控制台查看输出。

如果<mark style="background: #FFF3A3A6;">要保留空格渲染</mark>，那么当给元素添加 `white-space: pre` 或 `pre-wrap` 后，无论是 `textContent` 还是 `innerText`，浏览器都会保留所有空格和换行。这是因为 **CSS 的 `white-space` 属性优先级高于默认的空白处理规则**，覆盖了 `textContent/innerText` 的默认行为

例子：
```html
<div id="whitespace">
  前方  有多余    空格
</div>
```
```javascript
const div = document.getElementById("whitespace");
console.log(div.textContent); // 输出："前方  有多余    空格"
console.log(div.innerText);   // 输出："前方 有多余 空格"（合并空格）
```

#### 4. **防止 XSS 攻击**
```javascript
const userInput = '<img src="x" onerror="alert(1)">';
// ✅ 安全做法（自动转义）
element.textContent = userInput; 
// 输出：&lt;img src=&quot;x&quot; onerror=&quot;alert(1)&quot;&gt;

// ❌ 危险做法（执行脚本）
element.innerHTML = userInput;
```

---

### **五、注意事项**
1. **不适用于表单元素**  
   对于 `<input>`、`<textarea>`，应使用 `value` 属性：
   ```javascript
   const input = document.querySelector("input");
   input.value = "新值"; // ✅ 正确
   input.textContent = "新值"; // ❌ 无效
   ```

2. **SVG 支持**  
   `textContent` 同样适用于 SVG 文本元素：
   ```javascript
   const svgText = document.querySelector("svg text");
   svgText.textContent = "SVG 文本";
   ```

3. **性能优化**  
   批量修改时优先使用 `textContent`：
   ```javascript
   // ✅ 高效（一次性操作）
   let html = "";
   for (let i = 0; i < 1000; i++) {
     html += `<div>Item ${i}</div>`;
   }
   container.textContent = html;

   // ❌ 低效（多次重排）
   for (let i = 0; i < 1000; i++) {
     const div = document.createElement("div");
     div.textContent = `Item ${i}`;
     container.appendChild(div);
   }
   ```

---

### **六、总结**
| **场景**                     | **推荐方法**              |
|------------------------------|-------------------------|
| 需要获取所有文本（含隐藏内容） | `textContent`           |
| 安全显示用户输入             | `textContent`           |
| 保留原始空白格式             | `textContent`           |
| 需要样式感知的文本           | `innerText`             |

**一句话总结**：  
`textContent` = 获取/设置纯文本的瑞士军刀，安全高效，无视隐藏！ 🔐