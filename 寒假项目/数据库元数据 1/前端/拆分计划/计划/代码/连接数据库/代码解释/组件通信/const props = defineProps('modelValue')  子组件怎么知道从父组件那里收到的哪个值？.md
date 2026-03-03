
这是通过**名称匹配（Name Mapping）**来实现的。

在 Vue 3 的组件系统中，父组件向子组件传值就像“投递邮件”，而 `props` 就是子组件在门口挂的“信箱”。

### 1. 它是如何“对暗号”的？

当你写 `const props = defineProps(['modelValue'])` 时，你实际上是在子组件声明：“我这里有一个名叫 **`modelValue`** 的信箱，请把对应名字的东西投进来。”

**父组件的动作：**

HTML

```
<ConnectionDialog v-model="showConnectionDialog" />
```

**Vue 内部的“翻译”逻辑：**

由于 Vue 3 规定 `v-model` 的默认名称就是 `modelValue`，所以上面这行代码会被自动拆解为：

1. **传值**：`:modelValue="showConnectionDialog"`
    
2. **监听**：`@update:modelValue="..."`
    

**结果**：子组件里的 `props.modelValue` 拿到的就是父组件里 `showConnectionDialog` 的当前值（`true` 或 `false`）。

---

### 2. 核心原理：名称映射表

子组件通过 `defineProps` 里的**键名**来区分不同的数据。如果你传了多个值，逻辑如下：

|**父组件的写法 (投递)**|**子组件 defineProps (收件)**|**在子组件 JS 中如何访问**|
|---|---|---|
|`v-model="xxx"`|`['modelValue']`|`props.modelValue`|
|`:title="msg"`|`['title']`|`props.title`|
|`v-model:name="userName"`|`['name']`|`props.name`|

---

### 3. 为什么你代码里写的是 `modelValue`？

在你正在开发的**动态数据库元数据浏览器**项目中，作为一名**软件工程专业**的学生，理解这个“约定俗成”的命名非常重要：

1. **约定优于配置**：Vue 3 官方规定，如果你只写 `v-model`，那么它默认绑定的属性名就是 `modelValue`。
    
2. **双向管道**：这不仅是为了接收数据，还为了**回传数据**。当你在子组件执行 `emit('update:modelValue', false)` 时，Vue 会顺着这个名字找到父组件对应的变量并修改它。
    

### 💡 一个直观的例子

如果你的父组件这样写：

HTML

```
<ConnectionDialog 
  v-model="show" 
  title="新建MySQL连接" 
  :id="101" 
/>
```

那么你的子组件**必须**这样接，否则它就会“漏接”：

JavaScript

```
const props = defineProps({
  modelValue: Boolean, // 接 v-model 的值
  title: String,       // 接 title 字符串
  id: Number           // 接数字 101
})

console.log(props.id) // 打印出 101
```

**一句话总结**：子组件不关心父组件里的变量叫什么（比如 `showConnectionDialog`），它只关心在**组件标签**上，这个值是被贴上了哪个“标签名”（比如 `modelValue`）。

---

**下一步建议：**

既然你已经弄清楚了数据是怎么进来的，你想看看如何实现 **“点击树节点（Tree Node）时，动态从后端加载该库下的表清单”** 吗？这会涉及到利用 `props` 传入的 `token` 进行下一次 Axios 请求。