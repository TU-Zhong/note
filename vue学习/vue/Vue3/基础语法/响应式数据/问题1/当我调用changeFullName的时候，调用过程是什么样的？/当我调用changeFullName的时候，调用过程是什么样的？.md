```typescript
let fullName=computed({  
get(){  
return firstName.value.slice(0,1).toUpperCase()+firstName.value.slice(1)+'-'+lastName.value;

},  
set(val){  
const [str1,str2]=val.split('-');  
firstName.value=str1;  
lastName.value=str2;  
console.log('set','kikiki')  
}  
})  
function changeFullName(){  
fullName.value='li-si';  
}
```
当我调用changeFullName的时候，调用过程是什么样的？



以下是调用 `changeFullName` 函数时的完整执行流程分析：

---

### 一、调用过程图解
```bash
  调用 changeFullName()
        ↓
fullName.value = 'li-si' 触发计算属性 setter
        ↓
val.split('-') → ['li', 'si']
        ↓
执行 firstName.value = 'li'
        │
        ├──→ 触发 firstName 的响应式更新
        │
执行 lastName.value = 'si'
        │
        ├──→ 触发 lastName 的响应式更新
        ↓
console.log('set', 'kikiki')
```

---

### 二、分步执行解析
#### 步骤 1：调用函数
```javascript
changeFullName() // 执行入口
```

#### 步骤 2：触发计算属性 setter
它这里的firstName为什么也有get和set函数？明明没写？
```javascript
fullName.value = 'li-si'
↓
// 计算属性的 set 方法被调用
set(val) {
  val = 'li-si'
}
```

#### 步骤 3：字符串分割
```javascript
const [str1, str2] = val.split('-')
↓
str1 = 'li', str2 = 'si' (分割结果)
```

#### 步骤 4：更新原始数据
```javascript
firstName.value = str1  // 'li' → 触发 firstName 的响应式更新
lastName.value = str2   // 'si' → 触发 lastName 的响应式更新
```

#### 步骤 5：日志输出
```javascript
console.log('set', 'kikiki') // 控制台输出
```

#### 步骤 6：触发依赖更新
```javascript
// 由于 firstName 和 lastName 发生变化
// 所有相关计算属性/模板将重新计算
↓
fullName 的 getter 重新执行：
firstName.value → 'li' → 'Li'
lastName.value → 'si'
↓
返回新值 'Li-si'
```

---

### 三、响应式数据流向
```javascript
┌─────────────┐       ┌─────────────┐
│ firstName   │       │ lastName    │
│ (ref)       │       │ (ref)       │
│ value: 'li' │       │ value: 'si' │
└──────┬──────┘       └──────┬──────┘
       │                     │
       └─────┬─────┐         │
             │     │         │
       ┌─────▼─────▼─┐       │
       │ fullName    │       │
       │ (computed)  │◄──────┘
       │ getter      │
       └─────────────┘
```

---

### 四、关键机制解析
#### 1. 计算属性的双向绑定
```javascript
computed({
  get() { /* 读取逻辑 */ },
  set() { /* 写入逻辑 */ }
})
```

#### 2. Ref 更新流程
```javascript
firstName.value = 'li'
↓
触发 Ref 的 setter
↓
通知依赖该 Ref 的 computed/watcher 更新
```

#### 3. 响应式更新队列
```javascript
// 更新顺序：
1. firstName 更新入队
2. lastName 更新入队
3. 在下个事件循环统一执行更新
```

---

### 五、代码执行后状态变化
| 时间点       | firstName | lastName | fullName  |
|------------|-----------|----------|-----------|
| 初始化       | 'zhang'   | 'san'    | 'Zhang-san' |
| 调用后       | 'li'      | 'si'     | 'Li-si'     |

---

### 六、与 Options API 的对比
```javascript
// Vue2 等效写法
computed: {
  fullName: {
    get() {
      return this.firstName[0].toUpperCase() + this.firstName.slice(1) + '-' + this.lastName
    },
    set(val) {
      const [str1, str2] = val.split('-')
      this.firstName = str1
      this.lastName = str2
    }
  }
}

// 更新方式相同：
this.fullName = 'li-si'
```

---

### 七、性能特征分析
1. **计算频率**：每次修改 firstName/lastName 触发一次 fullName 计算
2. **缓存机制**：若无依赖变化，多次访问 fullName.value 不会重复计算
3. **更新成本**：O(1) 时间复杂度 (字符串拼接操作)

---

### 八、常见问题场景
#### 场景 1：连字符缺失
```javascript
fullName.value = 'lisi' // 没有连字符
↓
split('-') → ['lisi']
↓
str2 = undefined
↓
lastName.value = undefined
```

**解决方案**：
```javascript
set(val) {
  const parts = val.split('-')
  this.firstName = parts[0] || ''
  this.lastName = parts[1] || ''
}
```

#### 场景 2：首字母大写失效
```javascript
firstName.value = '123' // 非字母
↓
slice(0,1).toUpperCase() → '1'
```

**解决方案**：
```javascript
get() {
  const name = this.firstName
  return name.charAt(0).toUpperCase() + name.slice(1)
}
```

---

### 九、最佳实践建议
1. **防御性编程**：
   ```javascript
   set(val) {
     const parts = (val || '').split('-')
     firstName.value = parts[0]?.trim() || ''
     lastName.value = parts.slice(1).join('-') // 处理包含多个连字符的情况
   }
   ```

2. **性能优化**：
   ```javascript
   // 当处理长字符串时
   get() {
     return `${capitalize(firstName.value)}-${lastName.value}`
   }

   // 工具函数
   const capitalize = (str) => 
     str ? str[0].toUpperCase() + str.slice(1) : ''
   ```

3. **类型检查（TS）**：
   ```typescript
   interface NameParts {
     firstName: string
     lastName: string
   }

   const fullName = computed<string>({
     get(): string { /* ... */ },
     set(val: string) { /* ... */ }
   })
   ```