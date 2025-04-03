

以下是 **Vue3应用配置对象详解**，以及每个核心属性的使用示例，适合新手逐步学习：

---

### 一、基本结构
```javascript
const { createApp } = Vue;

createApp({
  // 这里写配置选项
}).mount('#app');
```

---

### 二、配置对象核心内容

#### 1. **data**（数据源）
```javascript
data() {
  return {
    message: 'Hello Vue!',
    count: 0,
    user: { name: '小明', age: 18 },
    todos: ['学习Vue', '写代码']
  }
}
```

#### 2. **methods**（方法）
```javascript
methods: {
  // 普通方法
  sayHello() {
    alert('你好！')
  },
  
  // 带参数的方法
  addCount(num) {
    this.count += num
  },
  
  // 访问data数据
  showUser() {
    console.log(this.user.name)
  }
}
```

#### 3. **computed**（计算属性）
```javascript
computed: {
  // 基本计算
  doubleCount() {
    return this.count * 2
  },
  
  // 带缓存的复杂计算
  filteredTodos() {
    return this.todos.filter(todo => todo.includes('Vue'))
  }
}
```

#### 4. **watch**（侦听器）
```javascript
watch: {
  // 监听基本类型变化
  count(newVal, oldVal) {
    console.log(`计数从${oldVal}变为${newVal}`)
  },
  
  // 监听对象属性（深度监听）
  user: {
    handler(newUser) {
      console.log('用户信息更新:', newUser)
    },
    deep: true // 深度监听对象内部变化
  }
}
```

#### 5. **生命周期钩子**
```javascript
// 常用生命周期
created() {
  console.log('组件已创建，可以访问data但DOM未生成')
},

mounted() {
  console.log('组件已挂载到DOM，可以操作DOM元素')
},

updated() {
  console.log('数据更新导致DOM变化')
},

unmounted() {
  console.log('组件已移除')
}
```

#### 6. **components**（注册子组件）
```javascript
components: {
  // 局部组件注册
  'my-button': {
    template: `<button @click="$emit('click')">点击我</button>`
  }
}
```

---

### 三、完整示例（计数器应用）
```javascript
const { createApp } = Vue;

createApp({
  // 数据
  data() {
    return {
      count: 0
    }
  },
  
  // 方法
  methods: {
    increment() {
      this.count++
    },
    reset() {
      this.count = 0
    }
  },
  
  // 计算属性
  computed: {
    isEven() {
      return this.count % 2 === 0 ? '偶数' : '奇数'
    }
  },
  
  // 监听器
  watch: {
    count(newVal) {
      if(newVal > 10) {
        alert('超过10了！')
      }
    }
  },
  
  // 生命周期
  created() {
    console.log('计数器组件已创建')
  }
}).mount('#app')
```

**模板代码**：
```html
<div id="app">
  <p>当前计数：{{ count }}</p>
  <p>状态：{{ isEven }}</p>
  <button @click="increment">+1</button>
  <button @click="reset">重置</button>
</div>
```

---

### 四、配置项总结表
| 配置项       | 作用                      | 特点                          |
|-------------|-------------------------|-----------------------------|
| data        | 定义响应式数据              | 必须用函数返回对象                |
| methods     | 定义方法                  | 可被模板直接调用                  |
| computed    | 计算属性                  | 带缓存，依赖变化时重新计算            |
| watch       | 数据监听器                | 适合执行异步或复杂操作               |
| components  | 注册局部组件               | 只在当前组件可用                  |
| created     | 生命周期-组件创建完成         | 数据已初始化，DOM未生成             |
| mounted     | 生命周期-组件挂载到DOM后      | 可以操作DOM元素                 |

---

### 五、模板中访问配置内容的优先级
```
实例属性访问顺序：
data → methods → computed → props
（同名的属性，后定义的会覆盖前面的，要避免命名冲突）
```

建议实际运行这些代码，并通过浏览器控制台观察不同生命周期阶段的日志输出，可以更直观理解各配置项的作用！ 😊