对于习惯了 Java 的你来说，可以将 `watch` 理解为 Java 中的 **`ChangeListener`（变化监听器）** 或者 **观察者模式（Observer Pattern）**。

在 Vue 中，`watch` 专门用来实现：“当某个数据变了，我就去执行一个特定的**副作用动作**”。

---

### 🔍 1. `watch` 到底是什么？

在你的代码中，`watch` 的逻辑链条是这样的：

1. **监听对象**：`filterText`（搜索框绑定的变量）。
    
2. **触发时机**：用户在搜索框里每打一个字，`filterText` 就会变。
    
3. **执行动作**：一旦变了，就去通知 `el-tree` 组件：“喂，数据变了，快用这个新词去过滤一下你的节点！”
    

---

### 🔍 2. `treeRef.value?.filter(val)` 是干嘛的？

这是一个**级联动作**：

- `treeRef.value`：拿到你在 HTML 里写的那个 `<el-tree>` 的“对象引用”（句柄）。
    
- `.filter(val)`：这是 Element Plus 组件自带的一个**内置方法**。
    
- **深层原理**：当你调用这个 `filter` 方法时，树组件内部会自动遍历所有的节点，并把当前的搜索词传给你定义的 `filterNode` 函数。
    

---

### 🔍 3. `filterNode`：Java 程序员的老朋友

这本质上是一个 **断言（Predicate）**。

JavaScript

```
const filterNode = (value, data) => {
  if (!value) return true; 
  return data.label.includes(value); 
};
```

你可以把它完全等同于 Java 8 的 **Stream Filter**：

Java

```
// Java 逻辑类比
list.stream()
    .filter(node -> node.getLabel().contains(searchText))
    .collect(Collectors.toList());
```

- 如果返回 `true`：这个节点保留，显示在屏幕上。
    
- 如果返回 `false`：这个节点隐藏。
    

---

### 🏗️ 总结：它们是怎么串联的？

为了让你看清全貌，这就是你代码运行时的“电路图”：

1. **用户输入**：在 `<el-input>` 输入了 "student"。
    
2. **数据变更**：`filterText` 变成 "student"。
    
3. **监视器报警**：`watch` 发现 `filterText` 变了。
    
4. **组件动作**：`watch` 内部调用了 `treeRef.value.filter("student")`。
    
5. **规则判定**：树组件对每个节点执行 `filterNode`。
    
6. **视觉反馈**：包含 "student" 的节点留下，其他的消失。
    

---

### 💡 为什么不直接在 HTML 里处理？

作为软件工程专业的学生，你需要理解**关注点分离**：

- **HTML (Template)**：只负责声明“这里有个树，它的过滤规则叫 `filterNode`”。
    
- **JS (Logic)**：负责在合适的时机（`watch`）启动这个过滤。
    

> **注意一个 Java 程序员常犯的错**：
> 
> 在 `<script>` 逻辑里，`treeRef` 必须加 **`.value`**。因为 `treeRef` 是一个包装过的引用，只有 `.value` 才是真正的组件实例。这里的 `?` 是可选链，防止组件还没加载完时调用导致空指针异常。

---

**既然搜索联动已经理顺了，下一步我们要不要把 `handleNodeDblClick` 写了？这样你双击左边的“学生表”，右边才能真正弹出标签页。**