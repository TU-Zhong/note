`map` 是 JavaScript/TypeScript 中数组的一个常用方法，它的作用是 **遍历数组中的每个元素，并对每个元素执行指定的操作，最终返回一个新的数组**。

---

### **`map` 的核心特点**
1. **不改变原数组**：`map` 会返回一个新数组，而不会修改原数组。
2. **返回值**：新数组的长度与原数组相同，每个元素是原数组元素经过操作后的结果。
3. **函数式编程**：`map` 是函数式编程的典型方法，强调对数据的转换。

---

### **`map` 的语法**
```typescript
let newArray = array.map((currentValue, index, array) => {
  // 对每个元素执行操作
  return result; // 返回处理后的值
});
```

- **`currentValue`**：当前正在处理的元素。
- **`index`**（可选）：当前元素的索引。
- **`array`**（可选）：原数组。
- **`result`**：处理后的值，将作为新数组的元素。

---

### **示例**

#### **示例 1：将数组中的每个元素加倍**
```typescript
let numbers = [1, 2, 3];
let doubled = numbers.map((num) => num * 2);

console.log(doubled); // [2, 4, 6]
```

#### **示例 2：将字符串数组转换为大写**
```typescript
let fruits = ["apple", "banana", "cherry"];
let upperCaseFruits = fruits.map((fruit) => fruit.toUpperCase());

console.log(upperCaseFruits); // ["APPLE", "BANANA", "CHERRY"]
```

#### **示例 3：提取对象数组中的特定属性**
```typescript
let users = [
  { name: "Alice", age: 25 },
  { name: "Bob", age: 30 },
];
let names = users.map((user) => user.name);

console.log(names); // ["Alice", "Bob"]
```

---

### **`map` vs `forEach`**
| **特性**           | **`map`**                           | **`forEach`**                     |
|--------------------|-------------------------------------|------------------------------------|
| 返回值             | 返回新数组                          | 无返回值                          |
| 是否改变原数组     | 不改变原数组                        | 不改变原数组                      |
| 适用场景           | 需要对数组元素进行转换时            | 仅需遍历数组，无需返回值时        |

---

### **总结**
- **`map` 的核心功能**：对数组中的每个元素执行操作，并返回一个新数组。
- **适用场景**：数据转换（如格式化、提取、计算等）。
- **优势**：代码简洁，符合函数式编程思想。

通过 `map` 方法，可以高效地对数组进行操作，生成新的数据集合！ 🚀