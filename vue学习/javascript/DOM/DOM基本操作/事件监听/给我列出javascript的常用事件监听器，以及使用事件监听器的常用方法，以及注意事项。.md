

以下是 JavaScript 中常用的事件监听器及使用方法的详细总结，附带注意事项和代码示例：

---

### **一、常用事件类型**
#### **1. 鼠标事件**
| 事件名称       | 触发场景                     | 示例代码                     |
|----------------|------------------------------|------------------------------|
| `click`        | 鼠标点击                     | `element.addEventListener('click', handler)` |
| `dblclick`     | 鼠标双击                     |                              |
| `mouseenter`   | 鼠标移入元素                 |                              |
| `mouseleave`   | 鼠标移出元素                 |                              |
| `mousemove`    | 鼠标在元素内移动             |                              |
| `mousedown`    | 鼠标按下（左键/右键）        |                              |
| `mouseup`      | 鼠标松开                     |                              |
| `contextmenu`  | 右键菜单事件（常用于阻止默认右键菜单） | |

#### **2. 键盘事件**
| 事件名称       | 触发场景                     |
|----------------|------------------------------|
| `keydown`      | 键盘按下时触发               |
| `keyup`        | 键盘松开时触发               |
| `keypress`     | 按下字符键时触发（已弃用，推荐用 `keydown`） |

#### **3. 表单事件**
| 事件名称       | 触发场景                     |
|----------------|------------------------------|
| `submit`       | 表单提交时触发               |
| `input`        | 输入框内容变化时触发         |
| `change`       | 表单元素值改变且失去焦点时触发（如下拉框、文件上传） |
| `focus`        | 元素获得焦点时触发           |
| `blur`         | 元素失去焦点时触发           |

#### **4. 窗口/文档事件**
| 事件名称       | 触发场景                     |
|----------------|------------------------------|
| `load`         | 页面或资源加载完成时触发     |
| `DOMContentLoaded` | HTML 解析完成时触发（无需等待图片） |
| `resize`       | 窗口大小改变时触发           |
| `scroll`       | 元素或窗口滚动时触发         |
| `beforeunload` | 页面关闭或刷新前触发         |

#### **5. 其他事件**
| 事件名称       | 触发场景                     |
|----------------|------------------------------|
| `touchstart`   | 触摸开始（移动端）           |
| `touchmove`    | 触摸移动                     |
| `touchend`     | 触摸结束                     |
| `transitionend`| CSS 过渡动画结束时触发       |

---

### **二、事件监听方法**
#### **1. `addEventListener`（推荐）**
```javascript
element.addEventListener('事件类型', 回调函数, 可选配置);
```
- **特点**：可添加多个监听器，支持事件捕获和冒泡阶段。
- **示例**：
  ```javascript
  const button = document.querySelector('button');
  button.addEventListener('click', function() {
    console.log('按钮被点击！');
  });

  // 添加第二个点击事件（不会覆盖）
  button.addEventListener('click', anotherHandler);
  ```

#### **2. `on事件` 属性（传统方式）**
```javascript
element.onclick = function() { ... };
```
- **特点**：只能绑定一个处理函数，后续赋值会覆盖之前的。
- **示例**：
  ```javascript
  button.onclick = function() { console.log('点击1'); };
  button.onclick = function() { console.log('点击2'); }; // 覆盖前一处理函数
  ```

#### **3. 移除事件监听**
```javascript
element.removeEventListener('事件类型', 回调函数);
```
- **要求**：必须与 `addEventListener` 绑定的回调函数是同一引用。
- **示例**：
  ```javascript
  function handler() { console.log('触发'); }
  button.addEventListener('click', handler);
  button.removeEventListener('click', handler); // 正确移除
  ```

---

### **三、注意事项**
#### **1. 事件冒泡与捕获**
- **冒泡**：事件从目标元素向上传播到根元素（默认阶段）。
- **捕获**：事件从根元素向下传播到目标元素。

  ```javascript
  element.addEventListener('click', handler, { 
    capture: true, // 捕获阶段触发
    once: true,    // 只触发一次
    passive: true  // 提示浏览器不阻止默认行为（优化滚动性能）
  });
  ```

#### **2. 事件委托（事件代理）**
- **场景**：动态添加的元素或大量子元素需要监听事件。
- **原理**：将事件绑定到父元素，通过 `event.target` 判断触发源。
- **示例**：
  ```javascript
  document.querySelector('.list').addEventListener('click', function(event) {
    if (event.target.classList.contains('item')) {
      console.log('点击了子元素', event.target);
    }
  });
  ```

#### **3. `this` 的指向**
- **在 `addEventListener` 的回调函数中**，`this` 默认指向绑定事件的元素。
- **箭头函数会改变 `this` 指向**，需注意：
  ```javascript
  button.addEventListener('click', () => {
    console.log(this); // 指向 Window 或外层作用域的 this
  });
  ```

#### **4. 性能优化**
- **防抖（Debounce）**：高频事件（如 `scroll`、`resize`）延迟触发。
  ```javascript
  function debounce(func, delay=300) {
    let timer;
    return function(...args) {
      clearTimeout(timer);
      timer = setTimeout(() => func.apply(this, args), delay);
    };
  }
  window.addEventListener('resize', debounce(handleResize));
  ```
- **节流（Throttle）**：确保事件在一定时间内只触发一次。
  ```javascript
  function throttle(func, delay=300) {
    let lastTime = 0;
    return function(...args) {
      const now = Date.now();
      if (now - lastTime >= delay) {
        func.apply(this, args);
        lastTime = now;
      }
    };
  }
  window.addEventListener('scroll', throttle(handleScroll));
  ```

#### **5. 内存泄漏**
- **动态移除元素时**，必须解绑事件监听。
- **单页应用（SPA）**：在组件销毁时移除事件监听。

---

### **四、完整代码示例**
```html
<button id="btn">点击我</button>
<ul class="list">
  <li class="item">Item 1</li>
  <li class="item">Item 2</li>
</ul>

<script>
  // 标准事件监听
  const btn = document.getElementById('btn');
  btn.addEventListener('click', function() {
    console.log('按钮被点击，this 指向按钮元素:', this);
  });

  // 事件委托
  document.querySelector('.list').addEventListener('click', function(event) {
    if (event.target.matches('.item')) {
      console.log('点击了子元素:', event.target.textContent);
    }
  });

  // 防抖示例
  function handleResize() {
    console.log('窗口大小调整完成:', window.innerWidth);
  }
  window.addEventListener('resize', debounce(handleResize));
</script>
```

---

### **五、总结**
- **优先使用 `addEventListener`**：支持多监听器和更精细的配置。
- **事件委托优化性能**：减少事件监听器数量，适合动态内容。
- **注意内存管理**：及时移除无用事件监听。
- **合理使用防抖节流**：提升高频事件性能。

掌握这些方法后，你可以高效处理用户交互，构建流畅的 Web 应用！ 🚀