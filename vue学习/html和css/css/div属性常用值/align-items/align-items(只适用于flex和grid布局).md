

用「摆书架」和「窗户高度」的比喻来解释这两个属性，保证傻子也能听懂：

---
[[align-items的常用值]]
### **1. `height: 100vh;` 是干什么的？**
<mark style="background: #FFF3A3A6;">想象你的网页是一扇窗户，`100vh` 就是让元素（比如一个书架）的高度 **撑满整个窗户的高度**，无论窗户开多大。</mark>

- <mark style="background: #FFF3A3A6;">**`vh`**：表示“视口高度”（Viewport Height），`100vh` = 100% 的窗户高度。</mark>
- <mark style="background: #FFF3A3A6;">**效果**：书架会一直和窗户一样高，用户滚动页面时，书架高度不变。</mark>

**代码示例**：
```html
<div class="书架">我是书架，和窗户一样高！</div>

<style>
.书架 {
  height: 100vh;   /* 撑满整个视口高度 */
  background: lightblue;
}
</style>
```

---

### **2. `align-items: center;` 是干什么的？**
想象你有一个横着摆书的书架（Flex 容器），`align-items: center` 是让所有书（子元素）**垂直方向居中摆放**（上下居中）。

- **适用场景**：父元素是 Flex 或 Grid 容器。
- **核心作用**：控制子元素在交叉轴（默认是垂直方向）的对齐方式。

**代码示例**：
```html
<div class="书架">
  <div class="书">📕</div>
  <div class="书">📗</div>
  <div class="书">📘</div>
</div>

<style>
.书架 {
  display: flex;           /* 变成 Flex 书架 */
  align-items: center;     /* 书垂直居中 */
  height: 100vh;           /* 书架高度撑满窗户 */
  background: #f0f0f0;
}

.书 {
  width: 100px;
  height: 100px;
}
</style>
```

**效果**：  
书在书架中 **上下居中排列**，但水平方向默认靠左排列（若需水平居中，需加 `justify-content: center`）。

---

### **总结**
| 属性                | 作用                   | 类比              |
|---------------------|-----------------------|-------------------|
| `height: 100vh`     | 元素高度撑满整个浏览器窗口 | 书架和窗户一样高   |
| `align-items: center` | 子元素在交叉轴方向居中 | 书上下居中摆放在书架 |

---

### **常见问题**
#### Q：为什么设置了 `align-items: center` 没效果？
- **检查父容器**：必须设置 `display: flex` 或 `display: grid`。  
- **检查父容器高度**：如果父容器没有固定高度（如 `height: 100vh`），子元素可能没有空间展示居中效果。

---

### **完整垂直水平居中示例**
```html
<div class="容器">
  <div class="内容">我在正中间！</div>
</div>

<style>
.容器 {
  display: flex;
  align-items: center;      /* 垂直居中 */
  justify-content: center;  /* 水平居中 */
  height: 100vh;
  background: lightblue;
}

.内容 {
  width: 200px;
  height: 200px;
  background: white;
}
</style>
```

**效果**：白色方块在蓝色背景中 **完全居中**。

---

学会这两个属性，让元素乖乖听话居中！ 🎯