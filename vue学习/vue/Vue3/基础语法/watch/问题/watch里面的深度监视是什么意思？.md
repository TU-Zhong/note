

在 Vue 的 `watch` 中，**深度监视（Deep Watch）** 是一种用于监听对象或数组内部嵌套值变化的机制。下面通过概念解析、使用场景和代码示例来详细说明：

---

### 一、核心概念解析

#### 1. 默认行为（浅层监听）
```javascript
watch(obj, (newVal) => {
  console.log('对象变化', newVal)
})
```
- **只监听对象引用变化**：当整个对象被重新赋值时触发
- **不监听内部属性变化**：修改 `obj.a.b = 1` 不会触发回调

#### 2. 深度监听（Deep Watch）
```javascript
watch(obj, (newVal) => {
  console.log('深度变化检测', newVal)
}, { deep: true })
```
- **监听所有嵌套属性变化**：修改 `obj.a.b = 1` 会触发回调
- **递归追踪依赖**：Vue 会遍历对象的所有层级属性

---

### 二、工作原理图解

```bash
        ┌───────────┐
        │  监听对象   │
        │  {        │
        │    a: {   │
        │      b: 1 │ ← 修改此处
        │    }      │
        │  }        │
        └─────┬─────┘
              │
      ┌───────▼───────┐
      │  Vue 响应式系统 │
      │  递归追踪所有属性 │
      └───────┬───────┘
              │
        ┌─────▼─────┐
        │ 触发回调函数 │
        └───────────┘
```

---

### 三、使用场景示例

#### 场景 1：监听表单对象
```javascript
const form = reactive({
  user: {
    name: 'Alice',
    age: 25
  },
  settings: {
    darkMode: false
  }
})

// 需要监听所有表单字段变化
watch(form, (newForm) => {
  console.log('表单内容变化', newForm)
}, { deep: true })

// 以下操作都会触发回调：
form.user.name = 'Bob'      // 嵌套属性修改
form.settings.darkMode = true
form.user = { name: 'Cara' } // 整体替换
```

#### 场景 2：监控复杂数据结构
```javascript
const treeData = ref({
  nodes: [
    {
      id: 1,
      children: [
        { id: 2, expanded: false }
      ]
    }
  ]
})

watch(treeData, (newTree) => {
  console.log('树结构变化', newTree)
}, { deep: true })

// 操作示例：
treeData.value.nodes[0].children[0].expanded = true // 触发
```

---

### 四、与 `watchEffect` 的区别

| 特性               | watch + deep                | watchEffect                |
|--------------------|-----------------------------|----------------------------|
| 监听方式          | 显式指定监听源              | 自动收集依赖               |
| 深度监听          | 需要手动启用                | 不支持深度监听             |
| 初始执行          | 需要配置 `immediate: true` | 默认立即执行               |
| 性能消耗          | 较高（需递归追踪）          | 中等（自动依赖追踪）        |

---

### 五、性能优化建议

#### 1. 限制监听深度
```javascript
// 只监听特定层级（Vue 3.4+）
watch(() => obj.a.b, (newVal) => {
  console.log('仅监听 a.b 的变化', newVal)
}, { deep: true })
```

#### 2. 避免滥用深度监听
```javascript
// 不推荐 ❌
watch(largeObject, callback, { deep: true })

// 推荐 ✅
watch(() => largeObject.importantKey, callback)
```

#### 3. 配合 `immediate` 使用
```javascript
// 初始立即执行 + 深度监听
watch(obj, callback, { 
  deep: true,
  immediate: true 
})
```

---

### 六、Vue 2 vs Vue 3 实现差异

#### Vue 2 实现
```javascript
watch: {
  obj: {
    handler(newVal) {
      console.log('变化', newVal)
    },
    deep: true
  }
}
```

#### Vue 3 组合式 API
```javascript
// 选项式写法
watch(
  () => state.obj,
  (newVal) => {
    console.log('变化', newVal)
  },
  { deep: true }
)

// 直接监听 reactive 对象
const obj = reactive({ a: 1 })
watch(obj, (newVal) => {
  console.log('变化', newVal)
})
// 注意：reactive 对象默认需要 deep: true
```

---

### 七、常见问题解答

#### Q1：为什么修改数组元素需要深度监听？
```javascript
const list = ref([1, 2, { a: 3 }])

watch(list, (newList) => {
  console.log('数组变化')
}, { deep: true })

// 以下操作会触发：
list.value[2].a = 4  // 需要深度监听
list.value.push(5)    // 不需要深度监听
```

#### Q2：深度监听能否检测新增属性？
```javascript
const obj = reactive({ a: 1 })

watch(obj, (newVal) => {
  console.log('变化')
}, { deep: true })

obj.b = 2 // 新增属性 ✅ 会被检测到
```

#### Q3：如何避免深度监听的性能问题？
```javascript
// 方案 1：使用性能更好的数据结构
const optimizedData = shallowRef({ /* 大数据对象 */ })

// 方案 2：手动触发更新
const trigger = ref(0)
watch(trigger, () => {
  // 手动处理数据检查
})
const checkChanges = () => {
  trigger.value++
}
```

---

### 总结
**深度监视** 是 Vue 响应式系统的重要扩展能力，合理使用可以：
1. 简化对复杂数据结构的监控
2. 确保嵌套属性的变化能被准确捕获
3. 提升开发效率（减少手动处理层级变化的代码）

但需谨记：
- 大型数据结构的深度监听会带来性能损耗
- 优先考虑精准监听特定属性
- 必要时结合 `shallowRef`/`markRaw` 优化性能