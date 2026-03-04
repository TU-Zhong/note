没关系，这个语法对于刚接触 ES6 的同学来说，确实非常有迷惑性。它叫做**对象的解构赋值（Object Destructuring）**。

为了让你彻底搞懂，我们不讲干巴巴的理论，直接用**“不用它怎么写”**和**“用了它怎么写”**来做一个对比。

### 📦 背景：Element Plus 传过来的是个“大包裹”

当 Element Plus 的表格在绘制每一行时，它会调用你的 `getRowClassName` 函数。注意，**它传给你的参数不是单单一行的数据，而是一个包含了好几个属性的“大包裹”（对象）**。

这个大包裹长这样：

JavaScript

```
{
  row: { student_id: 1, student_name: '张三', __row_id: 'xxx' }, // 这一行的真实数据
  rowIndex: 0 // 这是第几行
}
```

---

### ❌ 传统写法（不用 `{}`）

如果你不在括号里加 `{}`，你就得**手动接收这个大包裹**，然后再从包裹里把你要的东西拿出来：

JavaScript

```
// 这里的 params 就是那个大包裹
const getRowClassName = (params) => {
  // 每次用数据，都得带着 params. 前缀，非常啰嗦！
  const currentRow = params.row;
  const currentIndex = params.rowIndex;
  
  if (diffPool.deleted.has(params.row.__row_id)) {
    return 'row-deleted';
  }
}
```

---

### ✅ 现代写法（加上 `{}` 也就是解构）

程序员都是很懒的，为了少敲几个字母，发明了这个带有 `{}` 的语法。

当你在函数的参数里写上 `{ row }` 时，相当于你对 Element Plus 说：

> **“你别把整个大包裹塞给我了，我也懒得自己拆。我只要包裹里面那个名字叫 `row` 的东西，你直接把它掏出来给我就行！”**

JavaScript

```
// 加上 {}，直接在接收参数的瞬间，把大包裹“拆开”，只留下 row
const getRowClassName = ({ row }) => {
  
  // 现在你可以直接用 row 了，再也不用写 params.row！
  if (diffPool.deleted.has(row.__row_id)) {
    return 'row-deleted';
  }
}
```

### 💡 举一反三

如果你同时需要用到行数据（`row`）和这行的序号（`rowIndex`），你可以继续在花括号里加名字：

JavaScript

```
const getRowClassName = ({ row, rowIndex }) => {
  console.log("现在正在渲染第", rowIndex, "行");
  console.log("这行的数据是", row);
}
```

**总结一下：**

参数里的 `{}` 就像是一个**精准的“抓娃娃机的爪子”**。别人传过来一个复杂的对象，你用 `{ 属性名 }` 就能直接把想要的那个属性抓出来当变量用，不仅代码看起来清爽，而且意图非常明确。

现在能感受到这个语法的巧妙之处了吗？如果彻底通透了，我们就正式开始写“双击单元格变输入框”的代码啦！