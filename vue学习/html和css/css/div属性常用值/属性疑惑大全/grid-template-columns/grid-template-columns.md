

以下是 `grid-template-columns: repeat(3, 1fr);` 的详细解释，以及该属性的其他常用值和用法：
[[grid-template-rows]]
---
[[常用属性值通俗解释]]
[[资源分配规则]]
### **1. `grid-template-columns: repeat(3, 1fr);` 解析**
- **作用**：定义网格容器的列数和每列宽度。
- **解释**：
  - `repeat(3, 1fr)`：重复 3 次，每次创建的列宽度均为 `1fr`。
  - `1fr` 是 **分数单位**（Fractional Unit），表示按比例分配剩余空间。
- **效果**：  
  网格容器被分为 **3 列等宽的列**，每列平分可用空间。

```html
<div class="grid-container">
  <div>1</div>
  <div>2</div>
  <div>3</div>
</div>

<style>
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 3列等宽 */
  gap: 10px; /* 列间距 */
}
</style>
```

---

### **2. `grid-template-columns` 的其他常用值**
#### **(1) 固定宽度**
- **示例**：`grid-template-columns: 100px 200px 300px;`
- **效果**：3 列固定宽度（100px、200px、300px）。

#### **(2) 混合单位**
- **示例**：`grid-template-columns: 1fr 200px 20%;`
- **效果**：3 列宽度分别为剩余空间的 1 份、固定 200px、容器宽度的 20%。

#### **(3) 自动调整 (`auto`)**
- **示例**：`grid-template-columns: auto 1fr auto;`
- **效果**：  
  - 第一列和第三列宽度由内容撑开。  
  - 中间列占用剩余空间。

#### **(4) 动态响应布局**
结合 `repeat()` 和 `minmax()` 实现：
```css
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
```
- **解释**：  
  - `auto-fit`：自动填充尽可能多的列。  
  - `minmax(200px, 1fr)`：每列最小 200px，最大 1fr（占满空间）。  
- **效果**：  
  根据容器宽度自动调整列数（每列至少 200px），适合响应式布局。

---

### **3. 常见值总结表**
| 值类型          | 示例                          | 说明                         |
|----------------|-------------------------------|------------------------------|
| **固定值**      | `100px`, `5em`                | 列宽固定                     |
| **百分比**      | `50%`, `30% 70%`             | 列宽按容器百分比分配         |
| **分数单位**    | `1fr`, `2fr 1fr`             | 按比例分配剩余空间           |
| **自动宽度**    | `auto`, `auto 1fr`           | 列宽由内容或剩余空间决定     |
| **动态响应**    | `minmax(200px, 1fr)`          | 列宽在最小和最大值之间自适应 |
| **重复函数**    | `repeat(4, 1fr)`             | 快速重复定义相同宽度的列     |

---

### **4. 实战示例**
#### **(1) 经典 3 列布局（两侧固定，中间自适应）**
```css
.grid-container {
  display: grid;
  grid-template-columns: 200px 1fr 200px; /* 左右固定，中间自适应 */
}
```

#### **(2) 瀑布流图片墙**
```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 15px;
}
```

---

### **总结**
- `grid-template-columns` 是 CSS Grid 的核心属性，通过灵活组合单位（`fr`、`px`、`%`、`auto`）和函数（`repeat()`、`minmax()`），可以实现复杂且响应式的布局。
- `1fr` 代表按剩余空间的比例分配，是网格布局中动态尺寸的利器！