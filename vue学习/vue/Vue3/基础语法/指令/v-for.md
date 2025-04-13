

以下是 **Vue3 中 `v-for` 的常用知识点及示例**：

---

### 1. **基本用法：遍历数组**
渲染一个数组中的每一项，建议始终添加 `:key`。
```vue
<template>
  <ul>
    <li v-for="(item, index) in items" :key="item.id">
      {{ index + 1 }}. {{ item.name }}
    </li>
  </ul>
</template>

<script setup>
const items = [
  { id: 1, name: '苹果' },
  { id: 2, name: '香蕉' }
];
</script>
```

---

### 2. **遍历对象**
遍历对象的属性（顺序基于 `Object.keys()` 的结果）。
```vue
<template>
  <ul>
    <li v-for="(value, key, index) in user" :key="key">
      {{ index }}. {{ key }}: {{ value }}
    </li>
  </ul>
</template>

<script setup>
const user = {
  name: '张三',
  age: 30,
  city: '北京'
};
</script>
```

---

### 3. **使用解构**
在 `v-for` 中对数组项进行解构。
```vue
<template>
  <ul>
    <li v-for="({ name, price }, index) in products" :key="index">
      商品名：{{ name }}，价格：{{ price }}
    </li>
  </ul>
</template>

<script setup>
const products = [
  { name: '手机', price: 2999 },
  { name: '耳机', price: 499 }
];
</script>
```

---

### 4. **`v-for` 与 `:key` 的重要性**
- **作用**：帮助 Vue 高效更新 DOM，避免重复渲染问题。
- **规则**：`key` 必须是唯一标识符（如 `id`），不要用 `index`。
```vue
<!-- ✅ 正确写法 -->
<div v-for="user in users" :key="user.id">{{ user.name }}</div>

<!-- ❌ 避免使用索引作为 key -->
<div v-for="(user, index) in users" :key="index">{{ user.name }}</div>
```

---

### 5. **遍历范围**
生成指定次数的重复元素。
```vue
<template>
  <span v-for="n in 5" :key="n">{{ n }}</span>
</template>
<!-- 输出：1 2 3 4 5 -->
```

---

### 6. **结合响应式数组**
直接修改原数组（如 `push`/`pop`）会自动触发更新，但索引操作需注意。
```vue
<script setup>
import { ref } from 'vue';

const list = ref(['A', 'B', 'C']);

function addItem() {
  list.value.push('New Item'); // ✅ 自动触发更新
}

function updateByIndex() {
  list.value[1] = 'BB';        // ❌ 不会触发更新（需用 list.value.splice 或重新赋值）
}
</script>
```

---

### 7. **嵌套 `v-for`**
多层嵌套循环。
```vue
<template>
  <div v-for="group in groups" :key="group.id">
    <h3>{{ group.name }}</h3>
    <ul>
      <li v-for="member in group.members" :key="member.id">
        {{ member.name }}
      </li>
    </ul>
  </div>
</template>

<script setup>
const groups = [
  {
    id: 1,
    name: '开发组',
    members: [
      { id: 101, name: '张三' },
      { id: 102, name: '李四' }
    ]
  }
];
</script>
```

---

### 8. **在组件上使用 `v-for`**
为组件列表添加唯一 `key`，并通过 props 传递数据。
```vue
<template>
  <UserItem
    v-for="user in users"
    :key="user.id"
    :user="user"
    @delete="handleDelete"
  />
</template>

<script setup>
import UserItem from './UserItem.vue';

const users = [
  { id: 1, name: '王五' },
  { id: 2, name: '赵六' }
];

function handleDelete(userId) {
  // 处理删除逻辑
}
</script>
```

---

### 9. **过滤与排序**
使用计算属性处理数据后再遍历。
```vue
<template>
  <ul>
    <li v-for="item in filteredList" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
</template>

<script setup>
import { computed, ref } from 'vue';

const list = ref([
  { id: 1, name: '苹果', stock: 5 },
  { id: 2, name: '香蕉', stock: 0 }
]);

// 过滤有库存的商品
const filteredList = computed(() => {
  return list.value.filter(item => item.stock > 0);
});
</script>
```

---

### 10. **`v-for` 与 `v-if` 的优先级**
- **Vue3 中 `v-if` 优先级高于 `v-for`**，不建议在同一元素上同时使用。
- **推荐写法**：在外层包裹 `<template>` 分开处理。
```vue
<!-- ✅ 正确写法 -->
<template v-for="item in items" :key="item.id">
  <div v-if="item.isActive">
    {{ item.name }}
  </div>
</template>
```

---

### 总结
- **优先使用 `:key`**：确保唯一性（避免索引）。
- **响应式数据修改**：使用数组变更方法（`push`/`pop`/`splice`）或替换引用。
- **复杂场景**：结合计算属性优化渲染。
- **组件列表**：为每个子组件传递独立 `key` 和 props。