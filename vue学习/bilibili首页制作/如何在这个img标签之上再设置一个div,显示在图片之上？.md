

可以通过 **绝对定位（absolute）** 将新添加的 `div` 覆盖在图片上。以下是具体实现步骤：
[[z-index使用方法]]
这个东西决定了显示层级，但是父级容器的这个值决定了子级元素的这个值，自己元素不会超过父级元素。

---

### 步骤 1：修改 HTML 结构
在 `<img>` 标签**同级**添加一个 `<div>`，并为其添加类名（如 `overlay`）：
```html
<div class="container2">
    <div class="container">
        <img src="../static/img/顶部图片.avif" alt="图片">
        <!-- 新增的覆盖层 div -->
        <div class="overlay">这里显示覆盖内容</div>
    </div>
</div>
```

---

### 步骤 2：编写 CSS 样式
#### 关键点：
1. **父容器设置 `position: relative`**：为 `.container` 建立定位上下文。
2. **覆盖层设置 `position: absolute`**：使 `div` 相对于父容器定位。
3. **使用 `top: 0; left: 0;` 和宽高 100%**：让覆盖层完全覆盖父容器区域。

```css
.container {
    position: relative;  /* 关键：建立定位基准 */
    /* 可选：设置容器尺寸（如果图片未撑开容器） */
    width: 100%;
    height: auto;
}

.overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;         /* 宽度与父容器一致 */
    height: 100%;        /* 高度与父容器一致 */
    background: rgba(0, 0, 0, 0.3); /* 半透明背景（示例） */
    z-index: 1;          /* 确保覆盖层在图片上方 */
}

/* 确保图片不脱离文档流（默认已满足） */
.container img {
    display: block;      /* 消除图片底部间隙 */
    width: 100%;         /* 图片宽度撑满容器 */
    height: auto;
}
```

---

### 步骤 3：验证效果
- 覆盖层 `.overlay` 会完全覆盖在图片上方。
- 可通过修改 `background` 属性实现蒙版效果，或在 `.overlay` 内部添加文字、图标等内容。

---

### 扩展场景
#### 1. 覆盖层仅在悬停时显示
```css
.overlay {
    opacity: 0;  /* 默认隐藏 */
    transition: opacity 0.3s;
}

.container:hover .overlay {
    opacity: 1;  /* 悬停时显示 */
}
```

#### 2. 覆盖层内容居中
```css
.overlay {
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    font-size: 20px;
}
```

#### 3. 覆盖层局部覆盖（如右下角图标）
```css
.overlay {
    /* 调整为局部覆盖 */
    width: 50px;
    height: 50px;
    top: auto;       /* 取消默认的 top: 0 */
    left: auto;      /* 取消默认的 left: 0 */
    bottom: 10px;    /* 距离父容器底部 10px */
    right: 10px;     /* 距离父容器右侧 10px */
}
```

---

### 最终完整代码示例
```html
<!DOCTYPE html>
<html>
<head>
<style>
.container {
    position: relative;
    width: 500px; /* 假设容器宽度固定 */
    margin: 0 auto;
}

.container img {
    display: block;
    width: 100%;
    height: auto;
}

.overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 24px;
    z-index: 1;
}
</style>
</head>
<body>
<div class="container2">
    <div class="container">
        <img src="../static/img/顶部图片.avif" alt="图片">
        <div class="overlay">图片蒙版文字</div>
    </div>
</div>
</body>
</html>
```

---

通过这种方式，你可以灵活地在图片上方添加文字、半透明蒙版、操作按钮等交互元素。