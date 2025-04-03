

以下是 **Vue3 中 v-bind 指令用于属性绑定的核心知识点和示例**，重点覆盖样式绑定场景：

---
[[属性绑定和文本渲染的区别]]
### 一、基础用法
#### **1. 绑定普通属性**
[[vue学习/vue/Vue3/基础语法/属性绑定命令/基础语法|基础语法]]
```html
<img :src="imageUrl"> <!-- 动态绑定src -->
<a :href="link">跳转</a>
```

---

### 二、样式绑定专题
#### **1. 绑定 class**
**对象语法**（根据条件切换类名）：
```html
<div :class="{ active: isActive, 'text-danger': hasError }"></div>
```
当 `isActive` 为 true 时添加 `active` 类，`hasError` 为 true 时添加 `text-danger` 类

**数组语法**（应用多个类）：
```html
<div :class="[activeClass, errorClass]"></div>
```
```javascript
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

---

#### **2. 绑定内联样式**
**对象语法**（推荐）：
```html
<div :style="{ color: textColor, fontSize: size + 'px' }"></div>
```

**数组语法**（合并多个样式对象）：
```html
<div :style="[baseStyles, overridingStyles]"></div>
```

---

### 三、条件绑定技巧
#### **1. 三目运算符**
```html
<!-- 根据条件切换类 -->
<div :class="isActive ? 'active' : 'inactive'"></div>

<!-- 根据条件切换样式 -->
<div :style="{ color: isValid ? 'green' : 'red' }"></div>
```

#### **2. 动态属性名**
```html
<div :class="{[dynamicKey]: true}"></div>
```
当需要根据变量决定类名时使用

---

### 四、综合示例

#### **示例1：导航菜单激活状态**
```html
<ul>
  <li 
    v-for="item in menus"
    :class="{ active: item.id === currentMenu }"
    @click="currentMenu = item.id"
  >{{ item.name }}</li>
</ul>
```

#### **示例2：动态进度条**
```html
<div class="progress-bar">
  <div 
    :style="{ 
      width: progress + '%',
      backgroundColor: progress >= 100 ? '#4CAF50' : '#2196F3' 
    }"
  ></div>
</div>
```

#### **示例3：主题切换**
```html
<div :class="theme">
  <button @click="theme = 'light'">浅色</button>
  <button @click="theme = 'dark'">深色</button>
</div>
```
```css
.light { background: #fff; color: #333; }
.dark { background: #333; color: #fff; }
```

---

### 五、注意事项
1. **样式对象属性命名**  
   - 使用**驼峰式**或**引号包裹**（如 `fontSize` 或 `'font-size'`）
   
2. **安全警告**  
   - 避免直接绑定用户输入的样式（防止XSS攻击）

3. **优先级**  
   - 动态绑定的类/样式会与静态class共存
   - 动态绑定的样式会覆盖内联样式

4. **性能优化**  
   - 避免在模板中写复杂表达式（建议使用计算属性）

---

### 六、总结表格
| 场景                     | 语法示例                                      | 适用场景                  |
|-------------------------|---------------------------------------------|-------------------------|
| 条件类绑定                | `:class="{active: isActive}"`               | 根据状态切换单个/多个类        |
| 动态类列表                | `:class="[classA, classB]"`                 | 同时应用多个动态类           |
| 内联样式对象              | `:style="{color: textColor}"`               | 精细控制单个元素的样式         |
| 样式数组                 | `:style="[styleObj1, styleObj2]"`           | 合并多个来源的样式规则         |
| 三目条件样式              | `:style="{display: isShow ? 'block' : 'none'}"` | 简单的二元条件切换           |

建议将这些代码示例复制到Vue项目中进行实际操作，通过修改数据观察不同效果，可以更直观地理解各种绑定方式！ 😊