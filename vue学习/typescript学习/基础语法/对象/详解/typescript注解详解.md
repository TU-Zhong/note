TypeScript 的 **类型注解（Type Annotations）** 是一种显式声明变量、函数、参数等类型的语法。它的主要作用是：
1. **静态类型检查**：在编译时检查类型，避免运行时错误。
2. **代码可读性**：明确变量或函数的类型，提高代码的可维护性。
3. **开发工具支持**：提供更好的代码补全、错误提示和重构支持。

---

### **1. 基本类型注解**
为变量或常量指定类型。
```typescript
let name: string = "Alice";
let age: number = 25;
let isActive: boolean = true;
```

---

### **2. 数组类型注解**
指定数组元素的类型。
```typescript
let numbers: number[] = [1, 2, 3];
let fruits: Array<string> = ["Apple", "Banana"];
```

---

### **3. 元组类型注解**
指定元组中每个位置的类型。
```typescript
let person: [string, number] = ["Alice", 25];
```

---

### **4. 对象类型注解**
使用接口或类型别名定义对象类型。
```typescript
interface User {
  name: string;
  age: number;
  isAdmin?: boolean; // 可选属性
}

let user: User = {
  name: "Alice",
  age: 25,
};
```

---

### **5. 函数类型注解**
为函数参数和返回值指定类型。
```typescript
function add(a: number, b: number): number {
  return a + b;
}

const greet = (name: string): string => `Hello, ${name}!`;
```

---

### **6. 联合类型注解**
允许变量为多种类型。
```typescript
let value: string | number;
value = "Hello"; // ✅
value = 100;     // ✅
```

---

### **7. 字面量类型注解**
指定变量为固定的值。
```typescript
let status: "success" | "error";
status = "success"; // ✅
status = "pending"; // ❌
```

---

### **8. 类型别名**
使用 `type` 定义复杂类型。
```typescript
type Point = {
  x: number;
  y: number;
};

let p: Point = { x: 10, y: 20 };
```

---

### **9. 接口**
使用 `interface` 定义对象类型。
```typescript
interface Person {
  name: string;
  age: number;
}

let person: Person = {
  name: "Alice",
  age: 25,
};
```

---

### **10. 泛型**
为函数或类定义通用类型。
```typescript
function identity<T>(arg: T): T {
  return arg;
}

let output = identity<string>("Hello");
```

---

### **11. 类型断言**
手动指定类型。
```typescript
let someValue: any = "Hello";
let strLength1: number = (<string>someValue).length; // 尖括号语法
let strLength2: number = (someValue as string).length; // as 语法
```

---

### **12. 可选属性和只读属性**
- **可选属性**：使用 `?` 表示。
- **只读属性**：使用 `readonly` 表示。
```typescript
interface User {
  readonly id: number;
  name: string;
  age?: number; // 可选属性
}

let user: User = {
  id: 1,
  name: "Alice",
};
```

---

### **13. 索引签名**
允许对象具有动态属性名。
```typescript
interface Dictionary {
  [key: string]: number;
}

let scores: Dictionary = {
  math: 90,
  english: 85,
};
```

---

### **14. 函数重载**
为函数定义多个签名。
```typescript
function add(a: number, b: number): number;
function add(a: string, b: string): string;
function add(a: any, b: any): any {
  return a + b;
}

let result1 = add(1, 2); // 3
let result2 = add("Hello", "World"); // "HelloWorld"
```

---

### **15. 类型推断**
TypeScript 会根据上下文自动推断类型，无需显式注解。
```typescript
let num = 10; // 推断为 number
let str = "Hello"; // 推断为 string
```

---

### **总结**
| **特性**          | **说明**                                | **示例**                              |
|-------------------|----------------------------------------|---------------------------------------|
| 基本类型注解      | 为变量指定类型                         | `let age: number = 25;`               |
| 数组类型注解      | 指定数组元素的类型                     | `let numbers: number[] = [1, 2, 3];` |
| 对象类型注解      | 使用接口或类型别名定义对象类型         | `interface User { name: string; }`    |
| 函数类型注解      | 为参数和返回值指定类型                 | `function add(a: number, b: number): number;` |
| 联合类型注解      | 允许变量为多种类型                     | `let value: string | number;`          |
| 字面量类型注解    | 指定变量为固定值                       | `let status: "success" | "error";`   |
| 类型别名          | 使用 `type` 定义复杂类型               | `type Point = { x: number; y: number };` |
| 泛型              | 定义通用类型                           | `function identity<T>(arg: T): T;`    |
| 类型断言          | 手动指定类型                           | `let str = (value as string);`        |
| 可选属性          | 使用 `?` 定义可选属性                  | `age?: number;`                       |
| 只读属性          | 使用 `readonly` 定义只读属性           | `readonly id: number;`                |
| 索引签名          | 允许动态属性名                         | `[key: string]: number;`              |
| 函数重载          | 为函数定义多个签名                     | `function add(a: number, b: number): number;` |
| 类型推断          | TypeScript 自动推断类型                | `let num = 10;`                       |

通过类型注解，可以显著提高代码的健壮性和可维护性，同时减少潜在的错误！ 🚀