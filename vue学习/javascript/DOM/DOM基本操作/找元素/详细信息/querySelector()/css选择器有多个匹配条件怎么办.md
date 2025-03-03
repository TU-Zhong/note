

在 CSS 选择器中，如果需要在 `[]` 内组合多个条件（即多个属性匹配），可以通过 **连续书写多个属性选择器** 或 **使用特殊语法** 来实现。以下是详细分类和示例：

---

### **一、组合多个属性的写法**
#### 1. **"与" 条件（同时满足）**
**写法**：连续书写多个 `[属性]`  
**作用**：匹配 **同时满足所有属性条件** 的元素  
```css
/* 匹配同时有 id="user" 和 data-role="admin" 的元素 */
div[id="user"][data-role="admin"] {
  color: red;
}
```

#### 2. **"或" 条件（满足其一）**
**写法**：用逗号分隔多个选择器  
**作用**：匹配 **满足任意一个条件** 的元素  
```css
/* 匹配有 data-type 或 data-id 属性的元素 */
div[data-type], div[data-id] {
  border: 1px solid blue;
}
```

---

### **二、单个属性多值匹配**
#### 1. **匹配多个值中的任意一个**
**写法**：`[属性~="值"]`  
**作用**：匹配属性值以空格分隔的列表中包含指定值的元素  
```html
<div class="box warning"></div>
<div class="box error"></div>
```
```css
/* 匹配 class 包含 "warning" 或 "error" 的元素 */
div[class~="warning"], div[class~="error"] {
  background-color: yellow;
}
```

#### 2. **匹配属性值的开头或结尾**
**写法**：
- `[属性^="开头"]` → 匹配以指定值开头的属性
- `[属性$="结尾"]` → 匹配以指定值结尾的属性
- `[属性*="包含"]` → 匹配包含指定子串的属性
```css
/* 匹配 href 以 https 开头的链接 */
a[href^="https"] {
  color: green;
}

/* 匹配 src 以 .png 结尾的图片 */
img[src$=".png"] {
  border: 2px solid red;
}

/* 匹配 title 包含 "重要" 的元素 */
div[title*="重要"] {
  font-weight: bold;
}
```

---

### **三、高级组合技巧**
#### 1. **属性与伪类组合**
```css
/* 匹配被勾选且 disabled 的复选框 */
input[type="checkbox"]:checked[disabled] {
  opacity: 0.5;
}
```

#### 2. **属性与子元素组合**
```css
/* 匹配有 data-active 属性的父元素下的子元素 */
div[data-active] > .child {
  display: block;
}
```

#### 3. **同时匹配属性和标签**
```css
/* 匹配所有有 aria-label 属性的按钮 */
button[aria-label] {
  cursor: help;
}
```

---

### **四、代码示例汇总**
#### HTML 结构
```html
<div id="user" data-role="admin" class="profile vip">管理员</div>
<input type="checkbox" checked disabled>
<a href="https://example.com">安全链接</a>
<img src="logo.png">
```

#### CSS 选择器
```css
/* 同时有 id 和 data-role 属性 */
div[id="user"][data-role="admin"] {
  background: gold;
}

/* 禁用且被勾选的复选框 */
input[type="checkbox"][disabled]:checked {
  outline: 2px solid red;
}

/* 以 https 开头 或 以 .png 结尾的属性 */
a[href^="https"], img[src$=".png"] {
  border: 1px dashed blue;
}

/* 类名包含 vip 的元素 */
div[class~="vip"] {
  font-size: 24px;
}
```

---

### **五、注意事项**
| 错误写法                | 正确写法                      | 问题分析                   |
|-------------------------|-----------------------------|---------------------------|
| `div[class="vip profile"]` | `div[class~="vip"]`         | 前者要求严格顺序和完整匹配  |
| `input[checked=checked]` | `input[checked]`            | 布尔属性直接写属性名即可    |
| `[data-role=admin]`      | `[data-role="admin"]`       | 属性值建议用引号包裹        |

---

### **总结**
| 目标                | 写法                          | 示例                        |
|---------------------|-------------------------------|----------------------------|
| 同时满足多个属性    | `[attr1][attr2]`              | `div[id][data-role]`       |
| 匹配属性包含某值    | `[attr~="value"]`             | `[class~="error"]`         |
| 匹配属性值的部分    | `[attr^="start"]` / `[attr$="end"]` | `a[href^="https"]`         |
| 组合伪类和属性      | `:pseudo-class[attr]`         | `input:checked[disabled]`  |

通过灵活组合这些写法，可以精确筛选出你想要的 DOM 元素！ 🎯