你遇到的这个错误是一个很经典且“tricky”（有陷阱）的问题！

**错误原因**不是 JavaScript 语法本身，而是 **HTML 解析器**的工作方式。

### 🐞 为什么会报错？

你的代码中包含了这一行：

JavaScript

```
maliciousHtml: '这是一个<script>alert("XSS 攻击！");</script>尝试'
```

当浏览器（或 Vue 的编译工具）解析你的 `.vue` 文件时，它会读取 `<script setup lang="ts">` 标签。然后，它会**逐字扫描**这个标签内部的内容，**寻找第一个 `</script>`** 作为“脚本块结束”的标记。

在你的代码中，它找到了你字符串 _内部_ 的 `</script>`：

JavaScript

```
// ...
maliciousHtml: '这是一个<script>alert("XSS 攻击！");</script>  <-- 解析器在这里停止了！
// ...
```

解析器**误以为**你的整个 `<script setup>` 块在这里就已经结束了。但此时，你的 JavaScript 字符串 `maliciousHtml` 还没有遇到闭合的单引号 `'`。

因此，JavaScript 引擎（或 TypeScript 编译器）会报怨：“你开始了一个字符串（`'这是一个...`），但我没找到它的结尾！”—— 这就是 **"Unclosed string literal"**（未闭合的字符串字面量）错误的真正来源。

---

### 🚀 如何修复

你有两种简单的方法来“欺骗”HTML 解析器，让它找不到那个 `</script>` 字符串：

#### 方案一：转义斜杠（推荐）

在 JavaScript 字符串中，你可以对斜杠 `/` 进行转义，写成 `\/`。这对 JavaScript 来说含义不变，但 HTML 解析器就不会把它识别为标签的结尾。

Code snippet

```
<script setup lang="ts">
const data = {
  basicHtml: '<i>这是一段斜体文本</i>',
  // 注意这里的 <\/script>
  maliciousHtml: '这是一个<script>alert("XSS 攻击！");<\/script>尝试'
}
</script>
```

#### 方案二：字符串拼接

你也可以把这个敏感的字符串拆开，用 `+` 号拼起来。

Code snippet

```
<script setup lang="ts">
const data = {
  basicHtml: '<i>这是一段斜体文本</i>',
  // 将 '</script>' 拆开
  maliciousHtml: '这是一个<script>alert("XSS 攻击！");</' + 'script>尝试'
}
</script>
```

这两种方法都能确保 HTML 解析器正确地读完你的整个 `<script setup>` 块，从而修复这个错误。