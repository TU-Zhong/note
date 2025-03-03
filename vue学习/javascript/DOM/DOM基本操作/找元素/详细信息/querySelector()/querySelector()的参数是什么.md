

`querySelector()` 的 **参数是一个字符串格式的 CSS 选择器**，它的作用是告诉浏览器“用哪种规则找元素”。以下是详细解释和示例：

---

### **参数规则**
| 选择器类型        | 写法示例                     | 说明                          |
|-------------------|----------------------------|-------------------------------|
| **类选择器**      | `".class-name"`            | 查找第一个匹配该类的元素       |
| **ID选择器**      | `"#element-id"`            | 查找指定 ID 的元素             |
| **标签选择器**    | `"div"`                    | 查找第一个匹配的标签           |
| **属性选择器**    | `"[data-type='button']"`   | 查找有特定属性的元素           |
| **组合选择器**    | `"div.content > p"`        | 通过层级或条件组合查找         |
| **伪类选择器**    | `"input:checked"`          | 查找特定状态的元素             |

---

### **核心示例**
#### 1. 基础选择器
```javascript
// 查找第一个类名为 "btn" 的元素
const btn = document.querySelector(".btn");

// 查找 ID 为 "header" 的元素
const header = document.querySelector("#header");

// 查找第一个 <img> 标签
const img = document.querySelector("img");
```

#### 2. 组合选择器
[[属性选择器]]
[[css选择器有多个匹配条件怎么办]]
标签名+[匹配条件]
```javascript
// 查找第一个包含 data-id 属性的元素
const dataItem = document.querySelector("[data-id]");

// 查找类名为 "active" 的 <li> 元素
const activeLi = document.querySelector("li.active");

// 查找 <div class="container"> 下的第一个 <p> 子元素
const paragraph = document.querySelector("div.container > p");
```

#### 3. 伪类选择器
[[什么叫伪类选择器]]
### **核心逻辑**

1. **`input[type='checkbox']`**  
    → 先选出所有 **"复选框"**（相当于选出一批特定类型的温度计）
    
2. **`:checked`**  
    → 再从这些复选框中筛选出 **"当前被勾选"** 的（相当于从温度计里挑出显示发烧的）
```javascript
// 查找第一个被选中的复选框
const checkedBox = document.querySelector("input[type='checkbox']:checked");

// 查找表格的第一行
const firstRow = document.querySelector("tr:first-child");

// 查找无效的表单输入
const invalidInput = document.querySelector("input:invalid");
```

#### 4. 复杂层级
<mark style="background: #FFF3A3A6;">^= 匹配前缀；$=匹配后缀；*= 包含后面的这个字符串的字符串</mark>
[[匹配条件分析]]
<mark style="background: #FFF3A3A6;">nth-child(2)查找父元素的第二个子元素且要是li,否则不会匹配；nth-of-type(2)查找父元素的第二个li元素。</mark>

"ul.list li:nth-child(2)"<mark style="background: #FFF3A3A6;">条件辨析，这个必须要弄明白是怎么个事
</mark>
[[条件辨析]]
```javascript
// 查找第二个列表项
const secondItem = document.querySelector("ul.list li:nth-child(2)");

// 查找有 title 属性且以 "重要" 开头的元素
const importantItem = document.querySelector("[title^='重要']");

// 查找类名以 "-btn" 结尾的按钮
const specialBtn = document.querySelector("button[class$='-btn']");
```

---

### **注意事项**
1. **返回第一个匹配项**  
   `querySelector()` 始终返回 **第一个匹配的元素**，没有匹配时返回 `null`。

2. **转义特殊字符**  
   <mark style="background: #FFF3A3A6;">如果 ID 或类名包含特殊字符（如 `.`、`#`），需用反斜杠转义：</mark>
   ```javascript
   // 查找 ID 为 "user.name" 的元素
   const element = document.querySelector("#user\\.name");
   ```

3. <mark style="background: #FFF3A3A6;">**作用域限制**  
   可在特定元素内查找（缩小范围）：</mark>
   ```javascript
   const container = document.getElementById("app");
   // 只在 container 内查找
   const innerElement = container.querySelector(".child");
   ```

---

### **错误排查**
- **返回 `null` 的常见原因**：
  1. 选择器书写错误（如忘记 `.` 或 `#`）
  2. 元素尚未加载（脚本执行过早）
  3. 动态生成的内容未更新

- **验证选择器有效性**：  
  在浏览器开发者工具的 **Elements** 面板按 `Ctrl + F`（Windows）或 `Cmd + F`（Mac），输入选择器测试是否能匹配到元素。

---

掌握 `querySelector()` 的选择器语法，你就拥有了一把精准操作 DOM 的万能钥匙！ 🔑