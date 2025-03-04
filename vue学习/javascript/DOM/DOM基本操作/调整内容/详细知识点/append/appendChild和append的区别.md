

以下是 **`appendChild`** 和 **`append`** 的详细知识点和注意事项，用最简洁易懂的方式总结：

---

| 区别点         | appendChild                  | append                       |
|--------------|-----------------------------|-----------------------------|
| **接受参数**   | 只能一个节点                  | 多个参数（节点或字符串）         |
| **处理字符串** | 直接报错                     | 自动转成文本节点               |
| **移动节点**   | ✔️ 会移动                   | ✔️ 同样会移动                |
| **复制节点**   | 永远不复制                   | 只有传新节点或字符串时才会复制   |
### **一、appendChild**
#### **基本规则**
1. **功能**：将 **一个节点对象** 添加到父节点的子节点列表末尾。
   ```javascript
   parent.appendChild(childNode);
   ```

2. **返回值**：返回被添加的子节点（即参数本身）。

3. **唯一性**：如果子节点已存在于文档中，会先 **从原位置移除**，再添加到新位置（相当于移动节点）。

---

#### **注意事项**
- **参数必须是节点对象**  
  如果传入字符串、数字等非节点参数，会直接报错：
  ```javascript
  document.body.appendChild('text'); // ❌ 报错 TypeError
  ```

- **避免循环引用**  
  禁止将父节点添加到自己的子节点中：
  ```javascript
  parent.appendChild(parent); // ❌ 死循环警告
  ```

- **性能陷阱**  
  高频操作时，建议使用 `DocumentFragment` 减少重绘：
  ```javascript
  const fragment = document.createDocumentFragment();
  // 先批量操作 fragment
  parent.appendChild(fragment);
  ```

---

### **二、append**
#### **基本规则**
1. **功能**：将 **多个节点或字符串** 添加到父节点的子节点列表末尾。
   ```javascript
   parent.append(node1, "text", node2, ...);
   ```

2. **无返回值**：方法返回 `undefined`。

3. **允许多类型参数**：可以混合传入节点对象或字符串（字符串会转换为文本节点）。

---


#### **注意事项**

| 方法        | 当参数是 **已存在的节点**                 | 当参数是 **字符串或新节点**                 |
|------------|------------------------------------------|--------------------------------------------|
| **`append()`** | **移动节点**（和 `appendChild` 行为一致） | **创建新节点**（如字符串转文本节点）         |
| **`appendChild()`** | **移动节点**（同上）                     | ❌ 不接受字符串参数（会报错）                |

---

### **回到你的代码问题**  
你的代码中两次传入的是 **同一个已存在的节点对象 `c`**，所以无论用 `append()` 还是 `appendChild()`，都是 **移动同一个节点**，导致视觉上只有一个。

---

### **什么情况下 `append()` 会创建副本？**
只有当参数是 **字符串** 或 **新建的节点对象** 时，才会创建新节点：
```javascript
const a = document.getElementById("in");
const c = document.createElement("p");
c.textContent = "我是你爹";

// 第一次添加：创建并插入节点 c
a.append(c); 

// 第二次添加：传入字符串，自动创建新文本节点
a.append("新文本"); // ✅ 新增一个文本节点

// 第三次添加：传入新建的节点
const c2 = document.createElement("p");
c2.textContent = "另一个段落";
a.append(c2); // ✅ 新增节点 c2
```

---

### **如何强制复制已有节点？**
使用 `cloneNode(true)` 创建副本：
```javascript
const a = document.getElementById("in");
const c = document.createElement("p");
c.textContent = "我是你爹";

// 第一次添加原始节点
a.append(c); 

// 第二次添加副本（深拷贝）
const cClone = c.cloneNode(true); // true 表示深拷贝（包括子节点）
a.append(cClone); // ✅ 显示两个段落
```
