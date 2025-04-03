```html
    <style>
        .active{
            background-color: blue;
        }
        .Erroroo{
            background-color: red;
        }
    </style>
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
</head>
<body>
    <div>
        <a :href="link1">跳转百度</a>
        <div>
            <div>
                <span :class="active: isActive,Erroroo:isError">
                    {{isActive?当前已激活:当前未激活}}
                    {{isError?当前已错误:当前未激活}}
                </span>
            </div>
            <button @click="isActive=!isActive">激活</button>
            <button @click="isError=!isError">进入错误状态</button>
        </div>
    </div>
</body>
<script>
  const {createApp} = Vue
  createApp({
    data(){
      return{
          link1: 'https://baidu.com',
          isActive: true,
          isError: false
      }
    }
  }).mount('#app')
</script>
</html>这一段错在哪儿？
```



你的代码存在多处语法错误，以下是逐项修正和解释：

---

### 错误列表及修正方案

#### 1. 类绑定语法错误（主要问题）
```html
<!-- 错误写法 -->
<span :class="active: isActive,Erroroo:isError">

<!-- 正确写法（添加大括号） -->
<span :class="{ active: isActive, Erroroo: isError }">
```

#### 2. 插值表达式字符串未加引号
```html
<!-- 错误写法 -->
{{isActive?当前已激活:当前未激活}}

<!-- 正确写法 -->
{{ isActive ? '当前已激活' : '当前未激活' }}
```

#### 3. 缺少挂载点标识
```html
<!-- 原写法（无id导致无法挂载） -->
<div>

<!-- 正确写法（添加#app标识） -->
<div id="app">
```

---

### 完整修正代码
```html
<!DOCTYPE html>
<html>
<head>
    <style>
        .active {
            background-color: blue;
            color: white;
        }
        .Erroroo {
            background-color: red;
            color: white;
        }
    </style>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
</head>
<body>
    <!-- 添加挂载点 -->
    <div id="app">
        <a :href="link1">跳转百度</a>
        <div>
            <div>
                <!-- 修正类绑定和插值 -->
                <span :class="{ active: isActive, Erroroo: isError }">
                    {{ isActive ? '当前已激活' : '当前未激活' }}
                    {{ isError ? '当前已错误' : '当前正常' }}
                </span>
            </div>
            <!-- 保持按钮逻辑 -->
            <button @click="isActive=!isActive">激活</button>
            <button @click="isError=!isError">进入错误状态</button>
        </div>
    </div>
</body>
<script>
  const { createApp } = Vue
  createApp({
    data() {
      return {
          link1: 'https://baidu.com',
          isActive: true,
          isError: false
      }
    }
  }).mount('#app') // 正确挂载到#app
</script>
</html>
```

---

### 错误对照表
| 错误类型         | 错误代码示例                           | 修正后代码                                |
|------------------|--------------------------------------|-----------------------------------------|
| 类绑定语法        | `:class="active: isActive"`         | `:class="{ active: isActive }"`          |
| 字符串未加引号    | `{{isActive?当前已激活...}}`         | `{{ isActive ? '当前已激活'... }}`        |
| 挂载点缺失        | `<div>`                            | `<div id="app">`                        |
| 状态描述逻辑      | 两个插值都使用相同条件                | 分别判断 isActive 和 isError             |

---

### 关键知识点说明
1. **类绑定对象语法结构**
   ```html
   :class="{ 类名1: 布尔值, 类名2: 布尔值 }"
   ```
   必须用大括号包裹，键值对用逗号分隔

2. **插值表达式规范**
   ```html
   {{ JavaScript表达式 }}
   ```
   字符串必须用引号包裹，保持合法JS语法

3. **挂载点必要性**
   ```javascript
   .mount('#app') // 必须对应页面中的元素id
   ```
   这是Vue实例与DOM连接的桥梁

---

### 运行效果验证
1. 初始状态：蓝底白字 "当前已激活 当前正常"
2. 点击"进入错误状态"：红底白字 "当前已激活 当前已错误"
3. 点击"激活"：变回未激活状态

---

### 附加改进建议
1. 添加过渡效果：
   ```css
   span {
     transition: all 0.3s;
     padding: 5px;
     display: inline-block;
   }
   ```
2. 增加状态提示样式：
   ```css
   button {
     margin: 5px;
     padding: 5px 10px;
   }
   ```

建议将修正后的代码复制到本地文件运行，观察不同状态下的样式变化。如果仍有问题，请检查浏览器控制台是否有其他错误提示。