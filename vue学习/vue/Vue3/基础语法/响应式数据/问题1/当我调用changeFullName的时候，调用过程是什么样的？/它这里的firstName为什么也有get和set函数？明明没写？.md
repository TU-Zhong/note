

在 Vue3 的响应式系统中，`firstName` 之所以具备 **getter/setter** 函数，是因为你使用了 `ref()` 或 `reactive()` 创建响应式数据。这些函数在底层自动为数据添加了 **响应式拦截器**。以下是完整的技术解析：

---

### 一、响应式数据创建过程
当你使用 `ref()` 创建数据时：
```javascript
const firstName = ref('zhang')
```
Vue3 实际上会生成这样一个对象：
```javascript
{
  _value: 'zhang',
  get value() {         // 触发依赖收集
    track(this, 'value')
    return this._value
  },
  set value(newVal) {   // 触发更新派发
    this._value = newVal
    trigger(this, 'value')
  }
}
```

---

### 二、关键机制说明
#### 1. 自动生成的 getter/setter
| 方法      | 功能说明                                                                 |
|-----------|--------------------------------------------------------------------------|
| **getter** | 读取 `.value` 时触发，自动追踪当前依赖（如计算属性、模板等）             |
| **setter** | 修改 `.value` 时触发，自动通知所有依赖项更新                             |

#### 2. 响应式流程图解
```bash
   修改 firstName.value
       ↓
触发 setter 函数 → 通知依赖更新
       ↓
所有依赖项（如 fullName 计算属性）重新计算
       ↓
视图自动更新
```

---

### 三、你的代码执行分析
```javascript
function changeFullName() {
  fullName.value = 'li-si' // 触发 fullName 的 setter
}

// fullName 的 setter 内部：
set(val) {
  const [str1, str2] = val.split('-')
  firstName.value = str1 // 触发 firstName 的 setter
  lastName.value = str2  // 触发 lastName 的 setter
}
```

具体执行流程：
1. **修改 `fullName.value`**  
   计算属性的 setter 被调用

2. **分解字符串并赋值**  
   `firstName.value` 和 `lastName.value` 被修改

3. **触发响应式更新**  
   每个响应式变量（firstName/lastName）的 setter 执行：
   - 更新内部存储值
   - 通知所有依赖它们的计算属性/组件重新计算

4. **连锁更新**  
   `fullName` 的 getter 因依赖变化被重新调用，生成新值

---

### 四、为什么看不到 getter/setter 定义？
因为 Vue3 使用 **Proxy** 或 **Object.defineProperty** 自动注入了这些方法：

#### 实现原理对比
|              | Vue2 (Object.defineProperty)         | Vue3 (Proxy)                       |
|--------------|---------------------------------------|-------------------------------------|
| 实现方式      | 递归遍历对象属性添加 getter/setter    | 通过代理层拦截整个对象的访问         |
| 数组支持      | 需要特殊处理                         | 原生支持                            |
| 深层响应      | 初始化时递归处理                     | 按需代理（惰性处理）                |

---

### 五、验证实验
#### 实验 1：直接观察 ref 对象
```javascript
import { ref } from 'vue'

const test = ref('hello')
console.log(test)
```
控制台输出：
```
RefImpl {
  _rawValue: "hello",
  _value: "hello",
  dep: undefined,
  __v_isRef: true,
  value: (...)
  // 可以看到 getter/setter 的提示
}
```

#### 实验 2：性能测试
```javascript
// 测试 10 万次赋值操作
let count = ref(0)
console.time('ref setter')
for(let i=0; i<100000; i++) {
  count.value = i
}
console.timeEnd('ref setter') // ≈5ms (包含响应式系统的开销)
```

---

### 六、最佳实践建议
1. **优先使用 `ref` 的基础类型**  
   ```javascript
   const num = ref(0) // String/Number/Boolean 等使用 ref
   ```

2. **复杂对象使用 `reactive`**  
   ```javascript
   const user = reactive({
     name: 'Alice',
     age: 25
   })
   ```

3. **解构时保持响应性**  
   ```javascript
   const { name } = toRefs(user) // 正确的解构方式
   ```

---

### 七、常见问题解答
#### Q1：能否手动添加 getter/setter？
```javascript
// 可以，但会破坏响应式系统
const obj = reactive({
  _age: 25,
  get age() { return this._age },
  set age(v) { this._age = v }
})
```
（不推荐，可能导致依赖追踪失效）

#### Q2：为什么修改普通对象不触发更新？
```javascript
const badObj = { name: 'test' }
badObj.name = 'new' // ❌ 不会触发视图更新
```
（未经过响应式包装，无 getter/setter 拦截）

#### Q3：如何强制刷新视图？
```javascript
import { triggerRef } from 'vue'

triggerRef(countRef) // 强制触发更新
```
（应优先使用响应式数据流，避免手动触发）

---

通过 Vue 的响应式系统设计，开发者无需手动管理数据监听，只需关注数据逻辑本身。这种自动化的 getter/setter 机制正是 Vue 响应式系统的核心魔法所在。