

JavaScript 和 TypeScript 在基础语法上非常相似，因为 TypeScript 是 JavaScript 的超集（所有合法的 JS 代码都是合法的 TS 代码）。但 TypeScript 添加了**静态类型系统**和其他增强功能，以下是它们基础语法的核心区别：

TypeScript 的核心是==**静态类型系统**==，它在代码编写阶段就能发现潜在错误，而 JavaScript 的灵活性更高，但调试难度更大。两者基础语法相似，但 TypeScript 通过类型注解和其他特性大幅增强了代码的可维护性。

---

### **1. 类型注解（Type Annotations）**
#### **TypeScript**
```typescript
// 变量类型注解
let age: number = 25;
let name: string = "Alice";

// 函数参数和返回值类型
function add(a: number, b: number): number {
  return a + b;
}
```

#### **JavaScript**
```javascript
// 无类型注解
let age = 25;
let name = "Alice";

function add(a, b) {
  return a + b;
}
```

---

### **2. 接口（Interfaces）**
#### **TypeScript**
```typescript
// 定义接口
interface Person {
  name: string;
  age: number;
}

// 使用接口约束对象
const user: Person = {
  name: "Bob",
  age: 30,
};
```

#### **JavaScript**
JavaScript 没有接口，只能通过注释或运行时检查。
```javascript
// 仅通过注释描述对象结构
const user = {
  name: "Bob",
  age: 30,
};
```

---

### **3. 联合类型与字面量类型**
#### **TypeScript**
```typescript
let value: string | number; // 可以是字符串或数字
value = "hello";
value = 100;

// 字面量类型（固定值的类型）
let status: "success" | "error";
status = "success"; // 正确
status = "pending"; // 错误：只能是 "success" 或 "error"
```

#### **JavaScript**
JavaScript 无法限制变量值的类型或范围。
```javascript
let value = "hello";
value = 100; // 完全合法

let status = "success";
status = "pending"; // 合法
```

---

### **4. 函数参数类型与默认值**
#### **TypeScript**
```typescript
// 参数类型强制约束
function greet(name: string, age: number = 20): string {
  return `Hello ${name}, you are ${age} years old.`;
}
greet("Alice"); // 合法（age 默认 20）
greet("Bob", "30"); // 错误：第二个参数必须是数字
```

#### **JavaScript**
```javascript
// 无类型约束，参数默认值语法相同
function greet(name, age = 20) {
  return `Hello ${name}, you are ${age} years old.`;
}
greet("Alice"); // 合法
greet("Bob", "30"); // 合法（但没有类型检查）
```

---

### **5. 类（Classes）**
#### **TypeScript**
支持访问修饰符（`public`/`private`/`protected`）和类型注解：
```typescript
class User {
  private id: number;
  public name: string;

  constructor(id: number, name: string) {
    this.id = id;
    this.name = name;
  }
}
```

#### **JavaScript**
无访问修饰符，类型自由：
```javascript
class User {
  constructor(id, name) {
    this.id = id; // 默认公开
    this.name = name;
  }
}
```

---

### **6. 泛型（Generics）**
#### **TypeScript**
```typescript
// 泛型函数
function identity<T>(arg: T): T {
  return arg;
}

// 使用泛型
let output = identity<string>("hello"); // output 类型为 string
```

#### **JavaScript**
无泛型支持：
```javascript
function identity(arg) {
  return arg;
}
```

---

### **7. 枚举（Enums）**
#### **TypeScript**
```typescript
enum Direction {
  Up = "UP",
  Down = "DOWN",
}
let dir: Direction = Direction.Up;
```

#### **JavaScript**
需手动模拟枚举：
```javascript
const Direction = {
  Up: "UP",
  Down: "DOWN",
};
let dir = Direction.Up;
```

---

### **总结**
| 特性               | TypeScript                          | JavaScript                |
|--------------------|-------------------------------------|---------------------------|
| 类型注解           | 强制静态类型（编译时检查）         | 动态类型（运行时检查）   |
| 接口               | 支持 `interface` 约束对象结构       | 无                        |
| 类和访问修饰符     | 支持 `public`/`private`/`protected` | 无                        |
| 泛型               | 支持 `T` 类型参数                   | 无                        |
| 枚举               | 支持 `enum` 关键字                  | 需手动模拟                |
| 联合类型           | 支持 `string | number` 语法         | 无                        |
| 类型推断           | 自动推断变量类型（如 `let x = 5`）  | 动态类型，无静态推断      |

TypeScript 的核心是<mark style="background: #FFF3A3A6;">**静态类型系统**</mark>，它在代码编写阶段就能发现潜在错误，而 JavaScript 的灵活性更高，但调试难度更大。两者基础语法相似，但 TypeScript 通过类型注解和其他特性大幅增强了代码的可维护性。