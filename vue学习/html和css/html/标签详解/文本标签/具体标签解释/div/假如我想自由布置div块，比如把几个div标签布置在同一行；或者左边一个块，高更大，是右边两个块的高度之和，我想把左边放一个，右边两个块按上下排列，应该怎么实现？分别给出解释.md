

以下是实现两种典型布局的详细方法：

---

### **一、多个 `<div>` 排列在同一行**
默认情况下，`<div>` 是块级元素（独占一行），但可以通过以下方法实现水平排列：

#### 方法 1：使用 `Flexbox`（推荐）
通过 `display: flex` 让父容器成为弹性盒子，子元素自动水平排列。

```html
<div class="flex-container">
  <div class="box">块1</div>
  <div class="box">块2</div>
  <div class="box">块3</div>
</div>

<style>
  .flex-container {
    display: flex;      /* 启用 Flexbox */
    gap: 10px;         /* 设置子元素间距 */
  }
  .box {
    background: lightblue;
    padding: 20px;
  }
</style>
```

#### 方法 2：使用 `inline-block`
通过 `display: inline-block` 将块级元素转为行内块。

```html
<div class="inline-container">
  <div class="box">块1</div>
  <div class="box">块2</div>
  <div class="box">块3</div>
</div>

<style>
  .inline-container {
    font-size: 0;      /* 去除行内块默认间隙 */
  }
  .box {
    display: inline-block;
    width: 30%;
    background: lightcoral;
    padding: 20px;
    font-size: 16px;   /* 恢复字体大小 */
  }
</style>
```

#### 方法 3：使用 `Grid` 布局
通过 `display: grid` 定义网格列。

```html
<div class="grid-container">
  <div class="box">块1</div>
  <div class="box">块2</div>
  <div class="box">块3</div>
</div>

<style>
  .grid-container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);  /* 3 列等宽 */
    gap: 10px;
  }
  .box {
    background: lightgreen;
    padding: 20px;
  }
</style>
```

---

### **二、左侧一个高块，右侧两个上下排列的块**
目标效果：  
![](https://s2.loli.net/2024/07/08/2x1vX9Kk6b7Z8qo.png)

#### 实现方法：Flexbox + 嵌套 Flex
通过两层 Flexbox 实现动态高度匹配。

```html
<div class="layout-container">
  <div class="left">左侧块（高度较大）</div>
  <div class="right">
    <div class="top">右侧上块</div>
    <div class="bottom">右侧下块</div>
  </div>
</div>

<style>
  .layout-container {
    display: flex;         /* 父容器启用 Flexbox */
    gap: 20px;            /* 左右间距 */
    height: 400px;        /* 父容器固定高度（可选） */
  }
  .left {
    flex: 1;              /* 左侧占 1 份空间 */
    background: lightblue;
    padding: 20px;
  }
  .right {
    flex: 1;              /* 右侧占 1 份空间 */
    display: flex;        /* 右侧内部启用嵌套 Flexbox */
    flex-direction: column;  /* 垂直排列 */
    gap: 20px;            /* 上下间距 */
  }
  .top, .bottom {
    flex: 1;              /* 上下块平分右侧高度 */
    background: lightcoral;
    padding: 20px;
  }
</style>
```

#### 关键点说明：
1. **父容器设置 `display: flex`**：  
   让左右两侧水平排列，默认高度相同（由父容器或较高一侧决定）。
2. **右侧嵌套 `display: flex`**：  
   右侧内部使用垂直方向 (`flex-direction: column`) 的 Flexbox。
3. **子块高度分配 `flex: 1`**：  
   左右两侧各占 `1fr` 空间，右侧的上下块各占 `1fr`，总高度等于左侧高度。

---

### 三、其他注意事项
1. **父容器高度**：  
   - 如果未设置父容器高度，高度由内容撑开（可能导致左右高度不一致）。
   - 若需严格等高，可设置 `height: 400px`（示例中已用此方法）。
2. **响应式适配**：  
   使用百分比或 `min-height` 让布局适应不同屏幕尺寸。

---

通过上述方法，可灵活实现复杂布局。推荐优先使用 **Flexbox** 或 **Grid**，避免传统的 `float` 和 `inline-block`（易引发布局问题）。