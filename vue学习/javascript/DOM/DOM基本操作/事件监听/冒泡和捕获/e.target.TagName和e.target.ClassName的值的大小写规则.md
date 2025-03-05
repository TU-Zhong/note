

关于 `e.target.tagName` 和 `e.target.className` 的返回值规则，以下是关键点：

---

### 一、**`e.target.tagName` 的值**
- **返回值全为大写字母**  
  无论 HTML 中标签的书写方式如何，`tagName` 始终返回**全大写**的标签名。  
  ```html
  <!-- HTML -->
  <div id="test"></div>
  <SPAN class="demo"></SPAN>
  ```
  ```javascript
  document.querySelector('#test').addEventListener('click', (e) => {
    console.log(e.target.tagName); // 输出 "DIV"
  });
  
  document.querySelector('.demo').addEventListener('click', (e) => {
    console.log(e.target.tagName); // 输出 "SPAN"
  });
  ```

- **用途**  
  常用于判断元素类型（如 `if (e.target.tagName === 'BUTTON')`）。  
  **注意**：必须用大写字符串比较（如 `'DIV'` 而非 `'div'`）。

---

### 二、**`e.target.className` 的值**
- **返回值与 HTML 中的 `class` 属性完全一致**  
  1. **保留大小写**：HTML 中如何书写 `class`，`className` 就如何返回。  
  2. **多类名返回字符串**：多个类名以空格分隔。  

  ```html
  <!-- HTML -->
  <div class="myClass"></div>
  <span class="Red-Box active"></span>
  ```
  ```javascript
  // 点击第一个 div
  console.log(e.target.className); // 输出 "myClass"（保留原大小写）

  // 点击第二个 span
  console.log(e.target.className); // 输出 "Red-Box active"（空格分隔）
  ```

- **注意事项**  
  - 若元素没有 `class` 属性，返回空字符串 `""`。  
  - 直接修改 `className` 会覆盖原有类名（推荐用 `classList` API 增删）。

---

### 三、**对比总结表**

| 属性               | 大小写规则                     | 典型用途                     | 示例返回值              |
|--------------------|------------------------------|----------------------------|-------------------------|
| **`tagName`**      | 全大写（如 `DIV`, `INPUT`）   | 判断元素类型               | `"BUTTON"`, `"INPUT"`    |
| **`className`**    | 保留 HTML 中的原始大小写      | 获取类名或动态修改样式     | `"menu-item active"`     |

---

### 四、**事件委托中的正确用法**
#### 1. **判断标签类型（必须大写）**
```javascript
document.getElementById('list').addEventListener('click', (e) => {
  if (e.target.tagName === 'LI') { // 必须用全大写
    console.log('点击了列表项');
  }
});
```

#### 2. **判断类名（注意大小写敏感）**
```javascript
document.body.addEventListener('click', (e) => {
  // 方法1：直接匹配 className（需完全一致）
  if (e.target.className === 'red-button') {
    console.log('点击了红色按钮');
  }

  // 方法2：更健壮的类名判断（推荐）
  if (e.target.classList.contains('red-button')) {
    console.log('点击了红色按钮');
  }
});
```

---

### 五、**常见问题**
#### 问题1：为什么 `tagName` 要返回大写？
- **答案**：这是 DOM 规范的历史设计（早期 XML 严格区分大小写），保持一致性。

#### 问题2：如何安全地获取类名？
- **推荐使用 `classList`**：  
  ```javascript
  // 检查类名（不依赖大小写，但需注意类名本身的大小写）
  if (e.target.classList.contains('active')) { 
    // 逻辑
  }
  
  // 添加/移除类名（不破坏其他类）
  e.target.classList.add('highlight');
  ```

---

掌握这些规则后，你可以更精准地操作 DOM 元素！ 🚀