

以下是 TypeScript 的常用数据类型和运算符的详细说明及示例：

---

### **一、常用数据类型**

#### **1. 原始类型**
- **`number`**: 数值（整数、浮点数等）
  ```typescript
  let age: number = 25;
  let price: number = 9.99;
  ```
  
- **`string`**: 字符串
  ```typescript
  let name: string = "Alice";
  let message: string = `Hello, ${name}!`; // 模板字符串
  ```

- **`boolean`**: 布尔值
  ```typescript
  let isActive: boolean = true;
  let isEmpty: boolean = false;
  ```

---

#### **2. 数组**
[[数组知识点]]
- **语法**：`元素类型[]` 或 `Array<元素类型>`
  ```typescript
  let numbers: number[] = [1, 2, 3];
  let fruits: Array<string> = ["Apple", "Banana"];
  ```

---

#### **3. 元组（Tuple）**
[[元组]]
- 固定长度和类型的数组：
  ```typescript
  let person: [string, number] = ["Alice", 25]; // 正确
  let error: [string, number] = [30, "Bob"];     // 报错：类型不匹配
  ```

---

#### **4. 枚举（Enum）**
- 为一组常量命名：
  ```typescript
  enum Direction {
    Up = "UP",
    Down = "DOWN",
  }
  let dir: Direction = Direction.Up;
  ```

---

#### **5. `any`**
- 任意类型（慎用）：
  ```typescript
  let data: any = "Hello";
  data = 100; // 合法，但失去类型检查
  ```

---

#### **6. `void`**
- 表示无返回值（常用于函数）：
  ```typescript
  function logMessage(): void {
    console.log("This is a message");
  }
  ```

---

#### **7. `null` 和 `undefined`**
- 需要明确指定：
  ```typescript
  let u: undefined = undefined;
  let n: null = null;
  ```

---

#### **8. `never`**
- 表示永远不会返回（如抛出错误）：
  ```typescript
  function throwError(message: string): never {
    throw new Error(message);
  }
  ```

---

#### **9. `object`**
- 非原始类型的对象：
  ```typescript
  let user: object = { name: "Alice", age: 25 };
  ```

---

#### **10. 联合类型（Union Types）**
- 允许变量为多种类型：
  ```typescript
  let value: string | number;
  value = "Hello"; // 合法
  value = 100;     // 合法
  ```

---

#### **11. 字面量类型（Literal Types）**
- 固定值作为类型：
  ```typescript
  let status: "success" | "error";
  status = "success"; // 合法
  status = "pending"; // 报错
  ```

---

### **二、常用运算符**

#### **1. 算术运算符**
- `+`, `-`, `*`, `/`, `%`, `**`（幂）
  ```typescript
  let sum = 10 + 5;     // 15
  let remainder = 10 % 3; // 1
  let power = 2 ** 3;   // 8
  ```

---

#### **2. 比较运算符**
- `==`, `!=`, `===`, `!==`, `>`, `<`, `>=`, `<=`
  ```typescript
  let a = 5;
  let b = 10;
  console.log(a === 5);  // true
  console.log(b !== 10); // false
  ```

---

#### **3. 逻辑运算符**
- `&&`（与）, `||`（或）, `!`（非）
  ```typescript
  let isTrue = true && false; // false
  let isFalse = true || false; // true
  let notTrue = !true;        // false
  ```

---

#### **4. 赋值运算符**
- `=`, `+=`, `-=`, `*=`, `/=`
  ```typescript
  let x = 10;
  x += 5; // x = 15
  x *= 2; // x = 30
  ```

---

#### **5. 类型运算符**
- **`typeof`**: 获取变量类型
  ```typescript
  console.log(typeof "Hello"); // "string"
  ```

- **`instanceof`**: 检查对象是否为某类的实例
  ```typescript
  class Car {}
  let myCar = new Car();
  console.log(myCar instanceof Car); // true
  ```

---

#### **6. 可选链 `?.`**
- 安全访问深层属性（TypeScript 特有）：
  ```typescript
  type User = { address?: { city?: string } };
  let user: User = {};
  let city = user.address?.city; // 不会报错，city 为 undefined
  ```

---

#### **7. 空值合并 `??`**
- 左侧为 `null` 或 `undefined` 时返回右侧值：
  ```typescript
  let input = undefined;
  let value = input ?? "default"; // value = "default"
  ```

---

#### **8. 类型断言**
- 手动指定类型（两种写法）：
  ```typescript
  let someValue: any = "Hello";
  let strLength1: number = (<string>someValue).length; // 尖括号写法
  let strLength2: number = (someValue as string).length; // as 写法
  ```

---

### **总结**
- **数据类型**：TypeScript 在 JavaScript 基础上添加了静态类型（如 `enum`、`never`、联合类型等）。
- **运算符**：大部分运算符与 JavaScript 一致，但新增了类型相关操作（如 `as`、`?.`、`??`）。

通过合理使用类型和运算符，可以大幅提升代码的健壮性和可读性！ 🚀