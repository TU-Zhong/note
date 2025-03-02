

是的！**`grid-template-rows`** 的用法和 **`grid-template-columns`** 基本一致，唯一的区别是方向不同：一个是控制 **行的尺寸**，一个是控制 **列的尺寸**。用「横竖切蛋糕」来比喻：

---

### **核心对比**
| 属性                    | 作用方向       | 类比                 |
|-------------------------|---------------|----------------------|
| `grid-template-columns` | 控制 **列宽**  | **竖着切蛋糕** 🍰🔪  |
| `grid-template-rows`    | 控制 **行高**  | **横着切蛋糕** 🍰➖   |

---

### **用法完全一致**
#### 1. **固定值**
```css
grid-template-rows: 100px 200px;  /* 两行，第一行高 100px，第二行高 200px */
```

#### 2. **分数单位 `fr`**
```css
grid-template-rows: 1fr 2fr;  /* 两行高度比例 1:2 */
```

#### 3. **混合单位**
```css
grid-template-rows: 50px 1fr auto;  /* 第一行固定 50px，第二行占剩余空间，第三行由内容撑开 */
```

#### 4. **动态响应**
```css
grid-template-rows: repeat(auto-fit, minmax(100px, 1fr));  /* 自动生成行，每行最小 100px */
```

---

### **唯一区别：方向**
- **`grid-template-columns`** 定义的是 **垂直方向的分割线**（竖着切蛋糕）。  
- **`grid-template-rows`** 定义的是 **水平方向的分割线**（横着切蛋糕）。

---

### **示例：完整网格布局**
```html
<div class="grid-container">
  <div>1</div>
  <div>2</div>
  <div>3</div>
  <div>4</div>
</div>

<style>
.grid-container {
  display: grid;
  grid-template-columns: 1fr 1fr;  /* 竖着切两列 */
  grid-template-rows: 100px 200px;  /* 横着切两行 */
  gap: 10px;
}
</style>
```


---

### **注意事项**
1. **隐式行**：如果网格项超出定义的 `grid-template-rows` 行数，浏览器会自动生成新的行（默认高度为 `auto`）。  
   - 可通过 `grid-auto-rows` 设置这些隐式行的高度。  
   ```css
   grid-auto-rows: 50px;  /* 所有新增的行高为 50px */
   ```

2. **行高不足**：如果定义的行高度不够，内容可能会溢出。  
   - 使用 `minmax()` 防止内容被压扁：
   ```css
   grid-template-rows: minmax(100px, auto);  /* 最小 100px，高度自动增加 */
   ```

---

### **总结**
- `grid-template-rows` 和 `grid-template-columns` **语法完全一致**，只是方向不同。  
- 记住：**竖着切用 `columns`，横着切用 `rows`**，其他规则一模一样！ 🚀