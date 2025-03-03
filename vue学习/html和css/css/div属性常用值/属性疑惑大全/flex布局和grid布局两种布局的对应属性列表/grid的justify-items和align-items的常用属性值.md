

以下是 **Grid 布局**中 `justify-items` 和 `align-items` 的常用属性值及解释：

---

### **作用总结**  
- **`justify-items`**：控制 **水平方向（行轴）** 对齐（子元素在单元格内的水平位置）。  
- **`align-items`**：控制 **垂直方向（列轴）** 对齐（子元素在单元格内的垂直位置）。  
- **简写属性**：`place-items: <align-items> <justify-items>;`（如果只写一个值，表示两个方向相同）。

---

### **常用属性值**
| 属性值       | 效果                   | 示意图（以单元格为边界）                                             |
| --------- | -------------------- | -------------------------------------------------------- |
| `start`   | 对齐到单元格的 **起始边**（左/上） | ![start](https://via.placeholder.com/150x50?text=靠左/上对齐) |
| `end`     | 对齐到单元格的 **结束边**（右/下） | ![end](https://via.placeholder.com/150x50?text=靠右/下对齐)   |
| `center`  | 单元格内 **居中**          | ![center](https://via.placeholder.com/150x50?text=居中)    |
| `stretch` | **拉伸填满** 单元格（默认值）    | ![stretch](https://via.placeholder.com/150x50?text=拉伸铺满) |

---

### **示例代码**
```html
<div class="grid-container">
  <div class="item">A</div>
  <div class="item">B</div>
  <div class="item">C</div>
  <div class="item">D</div>
</div>
```

```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: repeat(2, 100px);
  gap: 10px;
  
  /* 水平对齐 */
  justify-items: center; /* start | end | center | stretch */
  
  /* 垂直对齐 */
  align-items: center; /* start | end | center | stretch */
  
  /* 简写 */
  place-items: center center; /* 或 place-items: center; */
}

.item {
  background: lightblue;
  padding: 10px;
}
```

---

### **效果对比**
| 场景 | `justify-items` | `align-items` | 效果 |
|------|-----------------|---------------|------|
| 默认 | `stretch`       | `stretch`     | 子元素拉伸填满单元格 |
| 居中 | `center`        | `center`      | 子元素在单元格内水平和垂直居中 |
| 右上对齐 | `end`          | `start`       | 子元素在单元格右下角对齐 |

---

### **记忆口诀**  
- `justify-items` → **横着动**（左右对齐）。  
- `align-items` → **竖着动**（上下对齐）。  
- `stretch` 是默认值，不想拉伸就改掉！  

---

### **注意事项**
1. **必须设置父元素为 Grid 容器**：`display: grid`。  
2. **子元素尺寸**：如果子元素有固定宽度/高度，可能无法拉伸。  
3. **单元格对齐 ≠ 整体对齐**：`justify-content` 和 `align-content` 用于控制整个网格在容器内的位置。