

以下是关于 Flex 布局中 `place-items` 的解答：

---
[[flex的justify-content和align-items的常用属性值]]
[[grid的justify-items和align-items的常用属性值]]
### **直接答案**  
<mark style="background: #FFF3A3A6;">**Flex 布局中没有 `place-items` 属性！** 这是 Grid 布局专属的，别搞混啦！</mark>

---

### **对比 Grid 和 Flex 的对齐属性**  
| 布局方式       | 主轴对齐        | 侧轴对齐        | 简写属性        |
|--------------|---------------|---------------|---------------|
| **Grid**     | `justify-items` | `align-items`  | `place-items`  |
| **Flex**     | `justify-content` | `align-items` | ❌ 无简写属性 |
<mark style="background: #FFF3A3A6;">grid布局一定要使用grid-template-columns和grid-template-rows两种属性</mark>
[[grid-template-rows]]
[[grid-template-columns]]

---

### **Flex 如何实现类似效果？**  
想在 Flex 中同时控制主轴和侧轴的对齐，必须分开写两个属性：  
```css
.parent {
  display: flex;
  justify-content: center;  /* 主轴居中 */
  align-items: center;      /* 侧轴居中 */
}
```

---

### **示例代码**  
```html
<div style="display: flex; justify-content: center; align-items: center; height: 200px; border: 2px solid red;">
  <div style="width: 80px; height: 80px; background: yellow;"></div>
</div>
```

**效果：**  
<div style="display: flex; justify-content: center; align-items: center; height: 200px; border: 2px solid red;">
  <div style="width: 80px; height: 80px; background: yellow;"></div>
</div>

---

### **Grid vs. Flex 对齐口诀**  
- **Grid** ➜ 一行代码搞定：`place-items: center;`  
- **Flex** ➜ 两行代码分开写：`justify-content` + `align-items`  

---

### **注意**  
Flex 中有一个 `place-content` 属性，但它是 `justify-content` 和 `align-content` 的简写（用于多行 Flex 容器），和 `place-items` 不同！