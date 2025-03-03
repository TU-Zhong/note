

在 **Flex 布局中**，并没有 `justify-items` 这个属性！这是 **Grid 布局**的属性。  
Flex 布局的对齐属性分为 **主轴对齐（`justify-content`）** 和 **交叉轴对齐（`align-items`）**，以下是它们的常用属性值：

---

### **1. 主轴对齐：`justify-content`**
控制 **主轴方向** 的对齐方式（默认主轴是水平方向，可通过 `flex-direction` 修改）。  
常用值：

| 值              | 效果                                | 示例图                                                                 |
|------------------|-----------------------------------|----------------------------------------------------------------------|
| `flex-start`     | 元素向主轴起点对齐（默认）                   | ![flex-start](https://img-blog.csdnimg.cn/direct/ae2c2efc5eb544da8a5a4e5d3a8b7a1d.png) |
| `flex-end`       | 元素向主轴终点对齐                        | ![flex-end](https://img-blog.csdnimg.cn/direct/1c20de4b9c6f4d4f9d8d1e5d2a9a1c8c.png)    |
| `center`         | 主轴居中                            | ![center](https://img-blog.csdnimg.cn/direct/3a0a8b8f1c7c4d7d8b9a2c9a6a4b4e4d.png)      |
| `space-between`  | 元素均匀分布，首尾贴边                     | ![space-between](https://img-blog.csdnimg.cn/direct/4b4b4b4b4b4b4b4b4b4b4b4b4b4b4b.png) |
| `space-around`   | 元素均匀分布，周围留相等空间（两侧空间是中间的一半） | ![space-around](https://img-blog.csdnimg.cn/direct/4b4b4b4b4b4b4b4b4b4b4b4b4b4b4b.png)  |
| `space-evenly`   | 元素均匀分布，所有间距相等                  | ![space-evenly](https://img-blog.csdnimg.cn/direct/4b4b4b4b4b4b4b4b4b4b4b4b4b4b4b.png)  |

---

### **2. 交叉轴对齐：`align-items`**
控制 **交叉轴方向** 的对齐方式（默认交叉轴是垂直方向）。  
常用值：

| 值             | 效果                                | 示例图                                                                 |
|-----------------|-----------------------------------|----------------------------------------------------------------------|
| `flex-start`    | 元素向交叉轴起点对齐（顶部对齐）               | ![flex-start](https://img-blog.csdnimg.cn/direct/ae2c2efc5eb544da8a5a4e5d3a8b7a1d.png) |
| `flex-end`      | 元素向交叉轴终点对齐（底部对齐）               | ![flex-end](https://img-blog.csdnimg.cn/direct/1c20de4b9c6f4d4f9d8d1e5d2a9a1c8c.png)    |
| `center`        | 交叉轴居中                          | ![center](https://img-blog.csdnimg.cn/direct/3a0a8b8f1c7c4d7d8b9a2c9a6a4b4e4d.png)      |
| `baseline`      | 元素按基线对齐（文字底部对齐）                | ![baseline](https://img-blog.csdnimg.cn/direct/4b4b4b4b4b4b4b4b4b4b4b4b4b4b4b.png)      |
| `stretch`       | 元素拉伸填满容器高度（默认值，需子元素未设置高度）    | ![stretch](https://img-blog.csdnimg.cn/direct/4b4b4b4b4b4b4b4b4b4b4b4b4b4b4b.png)       |

---

### **示例代码**
```html
<div class="container" style="display: flex; justify-content: center; align-items: center; height: 200px; border: 2px solid red;">
  <div style="width: 50px; height: 50px; background: yellow;"></div>
  <div style="width: 50px; height: 50px; background: lightblue;"></div>
</div>
```

**效果：**  
<div style="display: flex; justify-content: center; align-items: center; height: 200px; border: 2px solid red; gap: 10px;">
  <div style="width: 50px; height: 50px; background: yellow;"></div>
  <div style="width: 50px; height: 50px; background: lightblue;"></div>
</div>

---

### **总结**
- **Flex 主轴对齐用 `justify-content`**  
- **Flex 交叉轴对齐用 `align-items`**  
- `justify-items` **是 Grid 的属性，别用错了！**