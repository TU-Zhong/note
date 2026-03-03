`:rules="rules"` 是 Element Plus 表单组件的 **“法律法规手册”**。

它的作用是：**告诉表单组件，用户填写的每一个格子必须遵守什么规则，否则就报错飘红。**

我们用一个形象的比喻和代码拆解来看懂它：

### 1. 形象比喻：安检员的手册

- **`<el-form>`**：是机场安检口。
    
- **`:rules="rules"`**：是你发给安检员的**一本操作手册**。
    
- **手册里写着**：
    
    - “用户名”这一栏：**必须要填**，如果不填，就对他喊“请输入用户名”！
        
    - “密码”这一栏：**必须要填**，而且**长度不能少于 6 位**，否则就拦截！
        

### 2. 代码拆解

你在 JS 里定义的 `rules` 对象，通常长这样：

JavaScript

```
const rules = {
  // 对应 <el-form-item prop="username">
  username: [
    // 规则 1：必须填 (required: true)，没填就显示 message，鼠标离开时检查 (trigger: 'blur')
    { required: true, message: '请输入用户名', trigger: 'blur' } 
  ],
  // 对应 <el-form-item prop="password">
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, message: '密码长度不能少于6位', trigger: 'blur' }
  ]
}
```

### 3. 三个关键属性的含义

在每一条规则里，有三个核心单词：

1. **`required: true`** —— **【必填项】**
    
    - 如果用户留空，就会触发错误。同时表单项前面会自动出现一个红色的星号 `*`。
        
2. **`message: '...'`** —— **【报错话术】**
    
    - 当校验失败时，输入框下方会显示这行红色的小字。
        
3. **`trigger: 'blur'`** —— **【什么时候查？】**
    
    - **`'blur'`** (失去焦点)：当用户的鼠标点进输入框，然后**点到外面**（离开输入框）时，立即检查。
        
    - **`'change'`** (改变值)：用户每敲一个键盘字母，就检查一次（适合下拉框或实时性要求高的）。
        

### 4. ⚠️ 必须要配合 `prop` 使用！

这是新手最容易踩的坑。**规则要想生效，必须要把 `prop` 属性对上号！**

- **JS 里的规则名**：`username: [...]`
    
- **HTML 里的标签**：`<el-form-item prop="username">`
    

**如果你的 HTML 里没写 `prop="username"`，或者写错成了 `prop="name"`，那么 Element Plus 就会因为找不到对应的规则而导致校验失效（不飘红）。**

### 总结

`:rules="rules"` 就是你给表单设置的**自动化纠错机制**。有了它，你就不需要在代码里写一堆 `if (username === '') alert('没填')`，Element Plus 会自动帮你处理得漂漂亮亮。