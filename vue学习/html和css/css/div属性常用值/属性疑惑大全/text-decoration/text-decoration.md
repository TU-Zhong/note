

以下是 `text-decoration` 属性的详细写法规则，用简单易懂的方式整理：

---

### **`text-decoration` 属性通用写法**
这是一个复合属性（简写属性），可以同时设置以下子属性的值，**顺序不限**：
```
text-decoration: <text-decoration-line> <text-decoration-color> <text-decoration-style> <text-decoration-thickness>;
```

---

### **具体子属性说明**
| 子属性                     | 可选值                                                                 | 默认值          |
|---------------------------|-----------------------------------------------------------------------|----------------|
| **`text-decoration-line`**   | `none`（无装饰）<br>`underline`（下划线）<br>`overline`（上划线）<br>`line-through`（删除线） | `none`         |
| **`text-decoration-color`**  | 颜色值（如 `#ff0000`, `red`, `rgba(255,0,0,0.5)`）                    | 当前文本颜色    |
| **`text-decoration-style`**  | `solid`（实线）<br>`double`（双线）<br>`dotted`（点线）<br>`dashed`（虚线）<br>`wavy`（波浪线） | `solid`        |
| **`text-decoration-thickness`** | 数值（如 `2px`）<br>或相对单位（如 `0.1em`）                           | 自动（浏览器默认）|

---

### **常用组合示例**
#### 1. **基础下划线**
```css
.text {
  text-decoration: underline; /* 只设置线型 */
}
```

#### 2. **红色波浪线下划线（完整写法）**
```css
.text {
  text-decoration: underline wavy red;
}
```

#### 3. **蓝色双线删除线**
```css
.text {
  text-decoration: line-through double blue;
}
```

#### 4. **自定义粗细的虚线**
```css
.text {
  text-decoration: underline dashed #333 2px;
}
```

---

### **注意事项**
1. **顺序自由**：子属性值的顺序不影响效果（浏览器会自动识别类型）。  
2. **省略值自动使用默认值**：若未指定某个子属性，则使用默认值。  
   ```css
   text-decoration: underline; /* 等同于：underline 当前颜色 solid auto */
   ```
3. **兼容性**：  
   - `text-decoration-thickness` 较新（部分旧浏览器需前缀或单独设置）。

---

### **单独控制子属性（精细调整）**
```css
.text {
  text-decoration-line: underline;
  text-decoration-color: red;
  text-decoration-style: wavy;
  text-decoration-thickness: 3px;
}
```

---

### **经典用途**
- 去除链接默认下划线：
  ```css
  a {
    text-decoration: none;
  }
  ```
- 自定义输入框错误提示线：
  ```css
  .error {
    text-decoration: underline wavy red;
  }
  ```

掌握这些规则后，可以轻松控制文本装饰效果！ 🎨