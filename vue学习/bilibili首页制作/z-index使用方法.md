

`z-index` 是 CSS 中用于控制元素层叠顺序的属性，它能决定哪些元素在前、哪些元素在后显示。以下是 **核心知识点** 和 **实战用法**：

---

### 一、基础规则
1. **生效条件**：
   - 仅对 **定位元素** 生效（`position` 值为 `relative`/`absolute`/`fixed`/`sticky`）。
   - `static` 定位的元素会忽略 `z-index`。

2. **默认值**：
   - `auto`（浏览器自动计算层叠顺序，通常按 HTML 书写顺序排列，后写的元素覆盖前写的）。

3. **数值特性**：
   - 值越大，元素越靠前（可正可负）。
   - 支持负值（如 `z-index: -1`）。

---

### 二、核心概念：层叠上下文
`z-index` 的实际效果受 **层叠上下文（Stacking Context）** 影响。层叠上下文类似一个封闭的层级作用域，内部的 `z-index` 只在当前上下文内比较。

#### 形成层叠上下文的条件：
1. 根元素 `<html>`。
2. `position` 非 `static` 且 `z-index` 不为 `auto`。
3. `opacity` < 1。
4. `transform`、`filter`、`will-change` 等属性被设置。

---

### 三、实战示例
#### 示例 1：兄弟元素间的层叠
```html
<div class="box box1" style="z-index: 10;">z-index: 10</div>
<div class="box box2" style="z-index: 5;">z-index: 5</div>
```
- **效果**：`box1` 覆盖 `box2`（z-index 大的在上）。

#### 示例 2：父子元素的层叠上下文
```html
<div class="parent" style="position: relative; z-index: 1;">
  <div class="child" style="z-index: 100;"></div>
</div>

<div class="parent" style="position: relative; z-index: 2;">
  <div class="child" style="z-index: 0;"></div>
</div>
```
- **效果**：父元素的 `z-index` 决定了子元素的层级范围，第二个父容器的子元素 `z-index: 0` 仍会覆盖第一个父容器的子元素 `z-index: 100`。

---

### 四、常见错误与解决
#### 错误 1：`z-index` 不生效
- **原因**：元素未设置 `position` 或值为 `static`。
- **修复**：为元素设置 `position: relative` 等非 `static` 值。

#### 错误 2：子元素无法覆盖其他父容器的元素
- **原因**：父容器的 `z-index` 较低，导致子元素的层级被限制在当前层叠上下文中。
- **修复**：提升父容器的 `z-index`。

---

### 五、最佳实践
1. **避免滥用高值**：
   ```css
   /* ❌ 不要随意设置超大值 */
   .element {
     z-index: 9999;
   }

   /* ✅ 使用有逻辑的层级（如 10 的倍数） */
   :root {
     --z-modal: 100;
     --z-dropdown: 50;
     --z-tooltip: 30;
   }
   ```

2. **控制层叠上下文的生成**：
   - 仅在需要时设置 `z-index`，避免不必要的层叠上下文。

3. **调试技巧**：
   - 使用浏览器开发者工具的 **Layers** 面板（Chrome）可视化层级关系。

---

### 六、综合示例
```html
<style>
.modal {
  position: fixed;
  z-index: 100;       /* 模态框在最上层 */
  background: white;
}

.tooltip {
  position: absolute;
  z-index: 200;       /* 看似更大，但因父容器是 modal，实际无法覆盖 modal 外的元素 */
}

.sidebar {
  position: fixed;
  z-index: 50;        /* 会被 modal 覆盖 */
}
</style>

<div class="sidebar">侧边栏（z-index: 50）</div>
<div class="modal">
  <div class="tooltip">提示（z-index: 200，但受限于 modal 的层叠上下文）</div>
</div>
```

---

### 总结表
| 场景                          | 解决方案                          |
|------------------------------|---------------------------------|
| 元素覆盖顺序调整              | 设置合理 `z-index` 值             |
| 父容器限制子元素层级          | 提升父容器的 `z-index`            |
| 跨层叠上下文的覆盖            | 确保目标元素处于更高层叠上下文中   |

掌握 `z-index` 的关键是理解 **层叠上下文**，而非单纯比较数值大小。