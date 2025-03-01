

以下是常用 HTML 标签及其最常关联的 **CSS 通用属性**列表（按标签分类）：

---

### **一、容器/布局类标签**
| HTML 标签     | 常用 CSS 属性                                                                             | 典型用途   |
| ----------- | ------------------------------------------------------------------------------------- | ------ |
| `<div>`     | `display`, `width`, `height`, `margin`, `padding`, `background`, `border`, `position` | 通用布局容器 |
| `<section>` | 同 `<div>` + 语义化                                                                       | 内容区块划分 |
| `<header>`  | 同 `<div>` + 通常设置 `height` 或固定定位                                                       | 页面头部   |
| `<footer>`  | 同 `<div>` + 通常设置 `margin-top`                                                         | 页面底部   |
| `<main>`    | 同 `<div>` + `max-width`                                                               | 主体内容区域 |
| `<article>` | 同 `<div>` + `line-height` 优化阅读                                                        | 独立文章内容 |

---

### **二、文本类标签**
| HTML 标签       | 常用 CSS 属性                                 | 典型用途                       |
|------------------|---------------------------------------------|------------------------------|
| `<p>`            | `font-size`, `line-height`, `color`, `text-align`, `margin` | 段落文本                      |
| `<h1>`-`<h6>`    | `font-size`, `font-weight`, `color`, `margin` | 标题层级                      |
| `<span>`         | `color`, `font-size`, `display`, `vertical-align` | 行内文本修饰                  |
| `<strong>`       | `font-weight: bold`（默认，可覆盖）           | 强调加粗                      |
| `<em>`           | `font-style: italic`（默认，可覆盖）          | 强调斜体                      |
| `<a>`            | `color`, `text-decoration`, `:hover`, `cursor` | 链接样式                      |

---

### **三、列表类标签**
| HTML 标签       | 常用 CSS 属性                                 | 典型用途                       |
|------------------|---------------------------------------------|------------------------------|
| `<ul>`/`<ol>`    | `list-style`, `padding-left`, `margin`      | 列表容器                      |
| `<li>`           | `margin`, `padding`, `list-style-type`      | 列表项样式                    |

---

### **四、媒体类标签**
| HTML 标签       | 常用 CSS 属性                                 | 典型用途                       |
|------------------|---------------------------------------------|------------------------------|
| `<img>`          | `width`, `height`, `object-fit`, `border-radius` | 图片尺寸与裁剪                |
| `<video>`        | `width`, `height`, `controls`（默认显示控件）  | 视频播放器                    |
| `<audio>`        | `width`, `display: block`                    | 音频播放器                    |

---

### **五、表格类标签**
| HTML 标签       | 常用 CSS 属性                                 | 典型用途                       |
|------------------|---------------------------------------------|------------------------------|
| `<table>`        | `border-collapse`, `width`, `margin`        | 表格整体布局                  |
| `<tr>`           | `:nth-child()` 伪类选择器                   | 交替行背景色                  |
| `<td>`/`<th>`    | `padding`, `text-align`, `border`           | 单元格样式                    |

---

### **六、表单类标签**
| HTML 标签       | 常用 CSS 属性                                 | 典型用途                       |
|------------------|---------------------------------------------|------------------------------|
| `<form>`         | `padding`, `background`, `border-radius`    | 表单容器                      |
| `<input>`        | `width`, `padding`, `border`, `:focus`      | 输入框样式                    |
| `<textarea>`     | `resize`, `min-height`, `font-family`       | 多行文本框                    |
| `<button>`       | `background`, `color`, `border`, `:hover`   | 按钮交互样式                  |
| `<select>`       | `appearance: none`, `padding`, `border`     | 下拉菜单自定义                |
| `<label>`        | `cursor: pointer`, `display: block`         | 标签关联表单元素              |

---

### **七、其他常用标签**
| HTML 标签       | 常用 CSS 属性                                 | 典型用途                       |
|------------------|---------------------------------------------|------------------------------|
| `<nav>`          | `display: flex`, `gap`, `background`        | 导航栏布局                    |
| `<iframe>`       | `width`, `height`, `border: none`           | 嵌入外部内容                  |
| `<hr>`           | `border`, `margin`                          | 分割线样式                    |

---

### **通用属性总结**
以下属性几乎适用于所有元素：

| 属性             | 说明                                      | 示例值                      |
|------------------|-----------------------------------------|----------------------------|
| `display`        | 显示模式（`block`, `inline`, `flex` 等） | `display: flex`            |
| `margin`/`padding` | 内外边距                                | `margin: 10px auto`        |
| `width`/`height` | 尺寸控制                                | `width: 100%`              |
| `color`          | 文字颜色                                | `color: #333`              |
| `background`     | 背景颜色或图片                          | `background: #f0f0f0`      |
| `border`         | 边框样式                                | `border: 1px solid #ddd`   |
| `position`       | 定位方式（`relative`, `absolute` 等）   | `position: fixed`          |

---

### **使用建议**
1. **语义化优先**：优先使用 HTML5 语义化标签（如 `<nav>`, `<article>`），再通过 CSS 控制样式。
2. **响应式设计**：结合 `@media` 媒体查询和 `flex`/`grid` 布局适应不同屏幕。
3. **浏览器默认样式重置**：
   ```css
   * { margin: 0; padding: 0; box-sizing: border-box; }
   ```

通过熟悉这些属性，可以更高效地为不同标签编写样式！