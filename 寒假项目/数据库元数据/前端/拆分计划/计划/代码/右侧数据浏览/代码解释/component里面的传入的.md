你当然搜不到，因为这两个属性**不是 Vue 自带的，也不是 HTML 自带的**。

**这是你自己定义的（或者说是我帮你定义的）“私有属性”。**

---

### 🔍 揭秘时刻

这就好比你在 Java 里写了一个方法：

Java

```
// Java 方法定义
public void showConsole(String tabId, Map params) { ... }
```

- **`tabId`** 和 **`params`** 是这个方法的**形参**。
    
- 你在调用这个方法时，必须把参数传进去。
    

在 Vue 组件里，道理是一模一样的：

#### 1. 定义方（子组件）

我们在 `SqlConsole.vue` 和 `TableDataViewer.vue` 里写了这行代码：

JavaScript

```
// 相当于声明：我接收这两个参数
defineProps(['tabId', 'params'])
```

这就等于告诉 Vue：“谁想用我这个组件，必须给我传 `tabId` 和 `params` 这两个数据。”

#### 2. 调用方（父组件 WorkArea.vue）

你在 `<component>` 标签上写的：

HTML

```
<component 
  :tab-id="item.id"     :params="item.params" />
```

这就是在**给上面定义的两个“形参”赋值**。

---

### 💡 这里的两个重要知识点

#### 1. 为什么是 `:tab-id` 而不是 `:tabId`？

这是一个 Vue 的**命名潜规则**（HTML 大小写不敏感）：

- 在 **JavaScript (`defineProps`)** 里，我们习惯用**小驼峰命名法** (`tabId`)。
    
- 在 **HTML 模板**里，为了兼容性，Vue 建议自动转成**短横线命名法** (`tab-id`)。
    
- **Vue 会自动帮你转换**：你写 `:tab-id`，子组件收到 `tabId`。
    

#### 2. 动态组件的“透传”特性

`<component>` 这个标签非常特殊。

它自己没有任何属性（除了 `is`）。

**你写在它身上的所有其他属性（比如 `tab-id`, `params`, `wang-xiao-er`...），都会被原封不动地“透传”给当前加载的那个子组件。**

所以，如果你把代码改成：

HTML

```
<component :super-hero="item.id" />
```

只要你的子组件里写了 `defineProps(['superHero'])`，它照样能收到！

**结论：你是这个属性的“创世神”，除了你的代码，百度是搜不到的。**