

以下是 `<div>` 元素中常用 CSS 属性的关键值及典型用法说明：

---
[[小例子]]
### **1. `display`**
控制元素显示模式。

| 值              | 说明                     | 示例                    |
|------------------|-------------------------|-------------------------|
| `block`          | 块级元素（默认值）       | `display: block;`       |
| `inline`         | 行内元素（无法设置宽高） | `display: inline;`      |
| `inline-block`   | 行内块（可设宽高且同行） | `display: inline-block;`|
| `flex`           | 弹性布局容器             | `display: flex;`        |
| `grid`           | 网格布局容器             | `display: grid;`        |
| `none`           | 隐藏元素（不占空间）     | `display: none;`        |
[[flex和grid的通俗解释]]
---

### **2. `width` / `height`**
设置元素的宽度和高度。

| 值              | 说明                     | 示例                    |
|------------------|-------------------------|-------------------------|
| `px`             | 固定像素值               | `width: 300px;`         |
| `%`              | 相对于父元素的比例       | `width: 50%;`           |
| `auto`           | 自动计算（默认）         | `width: auto;`          |
| `vw`/`vh`        | 视口单位（响应式）       | `width: 100vw;`         |
| `max-width`      | 最大宽度限制             | `max-width: 1200px;`    |

---

### **3. `margin`**
设置元素的外边距（与其他元素的间距）。

| 值              | 说明                     | 示例                    |
|------------------|-------------------------|-------------------------|
| `auto`           | 自动水平居中             | `margin: 0 auto;`       |
| 数值             | 单边/多边统一设置        | `margin: 10px;`（四边） |
| 多值简写         | 上右下左（顺时针）       | `margin: 10px 20px 15px 5px;` |
| 百分比           | 基于父元素宽度的比例     | `margin: 5%;`           |

---

### **4. `padding`**
设置元素的内边距（内容与边框的间距）。  
语法与 `margin` 完全一致，但作用于内部。
```css
padding: 10px;          /* 四边统一 */
padding: 10px 20px;     /* 上下 10px，左右 20px */
padding: 5% 10px 0;    /* 上 5%，右 10px，下 0，左 10px */
```

---

### **5. `background`**
设置背景样式，支持复合属性或单属性。

| 属性              | 值示例                  | 说明                     |
|-------------------|-------------------------|-------------------------|
| `background-color`| `#fff`, `rgba(0,0,0,0.5)` | 背景颜色               |
| `background-image`| `url("image.jpg")`      | 背景图片               |
| `background-size` | `cover`, `contain`, `100% 200px` | 图片尺寸         |
| `background-position` | `center`, `left top` | 图片位置           |
| 复合写法          | `background: #f0f0f0 url("bg.png") no-repeat center/cover;` | 简写属性 |

---

### **6. `border`**
设置边框样式，支持单边或复合属性。

| 属性              | 值示例                  | 说明                     |
|-------------------|-------------------------|-------------------------|
| `border-width`    | `1px`, `2px 3px`        | 边框粗细               |
| `border-style`    | `solid`, `dashed`, `dotted` | 边框样式           |
| `border-color`    | `#ccc`, `transparent`   | 边框颜色               |
| 复合写法          | `border: 2px solid red;` | 简写（宽+样式+颜色） |
| `border-radius`   | `5px`, `50%`            | 圆角边框               |

---

### **7. `position`**
设置元素的定位方式。

| 值              | 说明                     | 典型场景                 |
|------------------|-------------------------|-------------------------|
| `static`         | 默认定位（不脱离文档流） | 无特殊定位需求时       |
| `relative`       | 相对定位（相对于自身）   | 微调元素位置           |
| `absolute`       | 绝对定位（相对于最近定位祖先） | 悬浮菜单、图标       |
| `fixed`          | 固定定位（相对于视口）   | 固定导航栏、弹窗       |
| `sticky`         | 粘性定位（滚动时固定）   | 表头固定、侧边栏      |

配合 `top`, `left`, `right`, `bottom`, `z-index` 使用：
```css
div {
  position: absolute;
  top: 20px;
  left: 50%;
  z-index: 10;
}
```

---

### **组合使用示例**
```html
<div class="box">这是一个容器</div>

<style>
  .box {
    display: flex;           /* 弹性布局 */
    width: 80%;
    height: 200px;
    margin: 20px auto;       /* 上下 20px，水平居中 */
    padding: 15px;
    background: #f5f5f5 url("bg.jpg") no-repeat center;
    border: 2px solid #333;
    border-radius: 8px;
    position: relative;      /* 为子元素绝对定位提供参照 */
  }
</style>
```

掌握这些属性值后，可以灵活实现大多数布局需求！