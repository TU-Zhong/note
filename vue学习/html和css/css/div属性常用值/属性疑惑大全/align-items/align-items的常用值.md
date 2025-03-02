

以下是 `align-items` 属性的常用值及其效果说明，适用于 **Flexbox 布局**和 **Grid 布局**：

---

### **常用值及效果**
| 值              | 作用（以 Flexbox 默认垂直方向为例） | 示意图                            |
|------------------|-----------------------------------|---------------------------------|
| **`stretch`**    | **拉伸子项**至填满容器高度（默认值） | ![stretch](https://s2.loli.net/2024/07/08/xVrHp8yT12fCJX6.png) |
| **`flex-start`** | 子项在容器**顶部对齐**              | ![flex-start](https://s2.loli.net/2024/07/08/5ys4nE9L7S3jtYF.png) |
| **`flex-end`**   | 子项在容器**底部对齐**              | ![flex-end](https://s2.loli.net/2024/07/08/3c5A8WZ6YQlTgXM.png) |
| **`center`**     | 子项在容器**垂直居中**              | ![center](https://s2.loli.net/2024/07/08/NsWwZbHxJ6e7lYv.png) |
| **`baseline`**   | 子项的**文字基线对齐**              | ![baseline](https://s2.loli.net/2024/07/08/1Fw5oBfKbM9Xc8Q.png) |

---

### **代码示例**
#### Flexbox 垂直居中布局
```html
<div class="container">
  <div class="item">Item 1</div>
  <div class="item">Item 2</div>
</div>

<style>
.container {
  display: flex;
  align-items: center; /* 控制垂直方向居中 */
  height: 200px;
  background: #f0f0f0;
}

.item {
  width: 100px;
  padding: 10px;
  background: lightblue;
  margin: 0 10px;
}
</style>
```

**效果**：  
两个浅蓝色方块在灰色容器中垂直居中。

---

### **对比不同值的场景**
1. **`stretch`**：  
   - 适合让子项等高填充容器，如导航栏按钮统一高度。
2. **`flex-start`**：  
   - 子项顶部对齐，适合顶部对齐的列表。
3. **`center`**：  
   - 垂直居中，适合登录框居中布局。
4. **`baseline`**：  
   - 文字基线对齐，适合多行文本或不同字体大小的元素对齐。

---

### **注意事项**
- **父容器必须有高度**：如果父容器没有固定高度（如 `height: 100vh` 或 `height: 200px`），`align-items` 可能无法生效。
- **Grid 布局中的行为**：在 Grid 中，`align-items` 控制网格项在单元格内的垂直对齐方式（需设置 `display: grid`）。

---

### **总结**
`align-items` 是控制子项在交叉轴上对齐的核心属性，根据不同布局需求选择合适的值即可！ 🎯