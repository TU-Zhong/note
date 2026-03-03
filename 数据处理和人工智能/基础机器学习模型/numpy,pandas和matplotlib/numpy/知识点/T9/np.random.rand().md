### 方法一：使用 `np.random.rand()`

這是最直接的方法。`rand()` 函數根據傳入的維度參數創建一個數組，並用[0, 1)區間的均勻分布隨機數填充它。

**代碼如下：**

```Python
import numpy as np

# 創建一個 5x5 的數組，數值為 [0, 1) 之間的隨機浮點數
random_array = np.random.rand(5, 5)

# 打印結果
print(random_array)
```
**代碼解釋：**

- `np.random.rand(5, 5)`: 這裡的兩個參數 `5, 5` 直接定義了數組的形狀（5行5列）。