好的，您这个问题问到了 Python 设计哲学的精髓！Python 通过一套统一的“特殊方法”（Special Methods，也因其前后都有双下划线而常被称为“Dunder Methods”）来实现操作符的重载（Operator Overloading）。这使得用户自定义的类（对象）可以像内置类型（如 `int`, `list`）一样，自然地使用 `+`, `[]`, `len()` 等操作。

下面，我将为您分类列出 Python 中最常用的一些统一操作符，以及它们在“幕后”调用的对应特殊方法。

---
[[这种操作符使用的多吗？]]
[[python内置类都实现了统一操作符吗]]
### Python 操作符与特殊方法对应表

#### 1. 算术操作符 (Arithmetic Operators)

这些操作符用于执行数学运算。

|操作符 (Operator)|示例 (Example)|对应的特殊方法 (Special Method)|说明|
|:--|:--|:--|:--|
|`+`|`a + b`|`__add__(self, other)`|加法|
|`-`|`a - b`|`__sub__(self, other)`|减法|
|`*`|`a * b`|`__mul__(self, other)`|乘法|
|`/`|`a / b`|`__truediv__(self, other)`|真除法 (结果为浮点数)|
|`//`|`a // b`|`__floordiv__(self, other)`|地板除 (结果向下取整)|
|`%`|`a % b`|`__mod__(self, other)`|取模 (求余数)|
|`**`|`a ** b`|`__pow__(self, other)`|幂运算|

Export to Sheets

_注：还有对应的“反向操作符” (`__radd__`, `__rsub__` 等)，用于 `b + a` 这种当左边的 `a` 不支持该操作时的情况。_

#### 2. 比较操作符 (Comparison Operators)

这些操作符用于比较两个对象。

|操作符|示例|对应的特殊方法|说明|
|:--|:--|:--|:--|
|`==`|`a == b`|`__eq__(self, other)`|等于|
|`!=`|`a != b`|`__ne__(self, other)`|不等于|
|`<`|`a < b`|`__lt__(self, other)`|小于|
|`>`|`a > b`|`__gt__(self, other)`|大于|
|`<=`|`a <= b`|`__le__(self, other)`|小于等于|
|`>=`|`a >= b`|`__ge__(self, other)`|大于等于|

Export to Sheets

#### 3. 赋值操作符 (Augmented Assignment Operators)

这些是“就地”修改的运算符。

|操作符|示例|对应的特殊方法|说明|
|:--|:--|:--|:--|
|`+=`|`a += b`|`__iadd__(self, other)`|就地加法 (In-place Add)|
|`-=`|`a -= b`|`__isub__(self, other)`|就地减法|
|`*=`|`a *= b`|`__imul__(self, other)`|就地乘法|
|`/=`|`a /= b`|`__itruediv__(self, other)`|就地真除法|

Export to Sheets

_如果一个类没有实现 `__iadd__`，`a += b` 会退化成 `a = a + b` (即调用 `__add__`)。_

#### 4. 一元操作符与函数 (Unary Operators & Functions)

|操作符/函数|示例|对应的特殊方法|说明|
|:--|:--|:--|:--|
|`-`|`-a`|`__neg__(self)`|取负数|
|`+`|`+a`|`__pos__(self)`|取正数 (不常用)|
|`abs()`|`abs(a)`|`__abs__(self)`|取绝对值|
|`bool()`|`bool(a)`|`__bool__(self)`|转换为布尔值 (在 `if a:` 时调用)|
|`int()`|`int(a)`|`__int__(self)`|转换为整数|
|`str()`|`str(a)`|`__str__(self)`|转换为对用户友好的字符串 (被 `print()` 调用)|
|`repr()`|`repr(a)`|`__repr__(self)`|转换为对开发者友好的、明确的字符串|

Export to Sheets

_`__str__` 和 `__repr__` 非常重要。如果只实现一个，优先实现 `__repr__`，因为 `__str__` 在未定义时会默认使用 `__repr__` 的结果。_

#### 5. 容器类型操作符 (Container Operators)

这是让你的对象表现得像一个列表、字典或集合的关键。

|操作符/函数|示例|对应的特殊方法|说明|
|:--|:--|:--|:--|
|`len()`|`len(a)`|`__len__(self)`|返回容器长度/元素个数|
|`in`, `not in`|`x in a`|`__contains__(self, item)`|**成员资格测试 (就是您之前问的)**|
|`[]` (取值)|`a[key]`|`__getitem__(self, key)`|获取指定索引或键对应的元素|
|`[]` (赋值)|`a[key] = val`|`__setitem__(self, key, value)`|设置指定索引或键对应的元素|
|`del`|`del a[key]`|`__delitem__(self, key)`|删除指定索引或键对应的元素|
|`for` 循环|`for x in a:`|`__iter__(self)`|返回一个迭代器对象|

Export to Sheets

---

### 一个实际例子：创建一个二维向量类

通过实现这些特殊方法，我们可以让我们自己写的 `Vector` 类像内置的数字一样进行加减法，并能用 `abs()` 计算它的长度。

Python

```
import math

class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    # 对应 print() 和 str()，对用户友好
    def __str__(self):
        return f"Vector({self.x}, {self.y})"

    # 对应 repr()，对开发者友好，可以用来重建对象
    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

    # 对应 '+' 操作符
    def __add__(self, other):
        # 允许向量与向量相加
        if isinstance(other, Vector):
            return Vector(self.x + other.x, self.y + other.y)
        return NotImplemented

    # 对应 '==' 操作符
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

    # 对应 abs() 函数，计算向量的模长
    def __abs__(self):
        return math.hypot(self.x, self.y)

# --- 现在来使用我们自定义的 Vector 类 ---

v1 = Vector(2, 3)
v2 = Vector(3, 4)

# 1. 使用 '+' 操作符 (背后调用了 __add__)
v3 = v1 + v2
print(f"v1 + v2 = {v3}")  # 输出: v1 + v2 = Vector(5, 7)

# 2. 使用 print() (背后调用了 __str__)
print(f"向量v1是: {v1}")   # 输出: 向量v1是: Vector(2, 3)

# 3. 使用 abs() 函数 (背后调用了 __abs__)
print(f"向量v1的长度是: {abs(v1)}") # 输出: 向量v1的长度是: 3.605...

# 4. 使用 '==' 操作符 (背后调用了 __eq__)
v4 = Vector(5, 7)
print(f"v3 和 v4 相等吗? {v3 == v4}") # 输出: v3 和 v4 相等吗? True
```

这个系统被称为 **Python 数据模型 (Python Data Model)**，它是 Python 语言强大、灵活和一致性的基石。通过学习并使用这些特殊方法，你可以写出非常优雅且符合 Python 风格的代码。