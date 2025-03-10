

在 HTML 和 CSS 中，`position` 属性用于控制元素的定位方式，是布局中非常重要的核心属性。以下是 `position` 的 **5 种常用值**及其详细说明和示例：

---

### 1. `static`（默认值）
- **特点**：
  - 元素按正常文档流排列。
  - 忽略 `top`、`right`、`bottom`、`left` 和 `z-index` 属性。
- **用途**：取消任何已应用的定位（如覆盖继承的定位属性）。
- **代码示例**：
  ```html
  <div style="position: static;">默认定位的 div</div>
  ```

---

### 2. `relative`（相对定位）
[[相对定位和绝对定位的区别]]
- **特点**：
  - 这个定位是相对于这个元素原本的位置的偏移
  - 元素**仍占据文档流的原始位置**。<mark style="background: #FFF3A3A6;">（只是视觉位置不一样了）</mark>，不一定一定要设定top等属性
  - 可通过 `top`、`right`、`bottom`、`left` **相对于自身原始位置偏移**。<mark style="background: #FFF3A3A6;">比如top: 70px;意思是该元素顶部相对于原来的位置有70px;</mark>
  - 其他元素不会填补原始位置的空间。
- **用途**：微调元素位置，或作为 `absolute` 子元素的定位基准。
- **代码示例**：
  ```html
  <div style="position: relative; top: 20px; left: 30px;">
    相对定位的 div（向右移动30px，向下移动20px）
  </div>
  ```

---

### 3. `absolute`（绝对定位）
[[相对定位和绝对定位的区别]]
- **特点**：
  - 元素**脱离文档流**，不占据原始空间。
  - 相对于**最近的非 `static` 定位的祖先元素**定位。，比如relative的元素。
  - 若没有非 `static` 祖先，则相对于 `<body>` 定位。
- **用途**：创建模态框、下拉菜单、悬浮提示等。
- **代码示例**：
  ```html
  <div style="position: relative; width: 300px; height: 200px; background: #eee;">
    父元素（relative定位）
    <div style="position: absolute; top: 10px; right: 10px;">
      绝对定位的子元素（相对于父容器右上角）
    </div>
  </div>
  ```

---

### 4. `fixed`（固定定位）
- **特点**：
  - 元素**脱离文档流**，相对于**浏览器视口（viewport）**定位。
  - 即使页面滚动，元素位置仍固定不变。
  - 常用于固定导航栏或悬浮按钮。
- **代码示例**：
  ```html
  <div style="position: fixed; bottom: 20px; right: 20px; background: #f00; color: white;">
    固定定位的悬浮按钮
  </div>
  ```

---

### 5. `sticky`（粘性定位）
- **特点**：
  - 元素在滚动到特定阈值前表现为 `relative`，超过阈值后表现为 `fixed`。
  - 需配合 `top`、`left` 等属性定义触发阈值。
- **用途**：实现吸顶导航栏、固定表头等。
- **代码示例**：
  ```html
  <div style="position: sticky; top: 0; background: #333; color: white;">
    滚动到顶部时固定的导航栏
  </div>
  ```

---

### 核心注意事项：
1. **`z-index`**：仅对定位元素（非 `static`）生效，控制层叠顺序。
2. **绝对定位的基准**：`absolute` 定位的元素需要父级设置 `relative`/`absolute`/`fixed`/`sticky` 才会相对于父级定位。
3. **性能问题**：过度使用 `fixed` 或 `sticky` 可能影响页面滚动性能。
4. **兼容性**：`sticky` 在旧版浏览器（如 IE）中不支持。

---

### 综合示例：各类定位组合使用
```html
<!DOCTYPE html>
<html>
<head>
<style>
.container {
  position: relative;
  height: 2000px;
  border: 2px solid #000;
}

.relative-box {
  position: relative;
  top: 50px;
  left: 50px;
  width: 200px;
  height: 100px;
  background: lightblue;
}

.absolute-box {
  position: absolute;
  bottom: 20px;
  right: 20px;
  width: 100px;
  height: 50px;
  background: coral;
}

.fixed-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background: #4CAF50;
  color: white;
  padding: 10px;
}

.sticky-header {
  position: sticky;
  top: 0;
  background: #333;
  color: white;
  padding: 10px;
}
</style>
</head>
<body>
  <div class="sticky-header">吸顶导航栏（滚动时固定）</div>
  
  <div class="container">
    <div class="relative-box">
      相对定位的盒子
      <div class="absolute-box">绝对定位的子盒子</div>
    </div>
  </div>

  <div class="fixed-button">固定按钮</div>
</body>
</html>
```

---

通过灵活组合这些定位方式，可以实现复杂的页面布局和交互效果。建议通过实际案例练习，理解不同定位的层级关系和视觉表现。