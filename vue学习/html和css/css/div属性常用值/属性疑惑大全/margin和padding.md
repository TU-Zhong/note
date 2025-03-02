### `margin` 和 `padding` 的属性值

#### `margin` 属性
`margin` 属性用于设置元素的外边距。它可以接受以下值：

1. **单个值**：所有四个方向的外边距相同。
   ```css
   margin: 10px;
   ```

2. **两个值**：第一个值表示上下外边距，第二个值表示左右外边距。
   ```css
   margin: 10px 20px;
   ```

3. **三个值**：第一个值表示上外边距，第二个值表示左右外边距，第三个值表示下外边距。
   ```css
   margin: 10px 20px 30px;
   ```

4. **四个值**：分别表示上、右、下、左四个方向的外边距（顺时针方向）。
   ```css
   margin: 10px 20px 30px 40px;
   ```

5. **特定方向的值**：
   ```css
   margin-top: 10px;
   margin-right: 20px;
   margin-bottom: 30px;
   margin-left: 40px;
   ```

#### `padding` 属性
`padding` 属性用于设置元素的内边距。它的值与 `margin` 类似：

1. **单个值**：所有四个方向的内边距相同。
   ```css
   padding: 10px;
   ```

2. **两个值**：第一个值表示上下内边距，第二个值表示左右内边距。
   ```css
   padding: 10px 20px;
   ```

3. **三个值**：第一个值表示上内边距，第二个值表示左右内边距，第三个值表示下内边距。
   ```css
   padding: 10px 20px 30px;
   ```

4. **四个值**：分别表示上、右、下、左四个方向的内边距（顺时针方向）。
   ```css
   padding: 10px 20px 30px 40px;
   ```

5. **特定方向的值**：
   ```css
   padding-top: 10px;
   padding-right: 20px;
   padding-bottom: 30px;
   padding-left: 40px;
   ```

---

### 速记方法
`margin` 和 `padding` 的速记语法遵循相同的规则，可以通过以下方式快速记忆：

1. **单个值**：
   - 应用于所有四个方向。
   ```css
   margin: 10px; /* 上下左右都是10px */
   padding: 10px; /* 上下左右都是10px */
   ```

2. **两个值**：
   - 第一个值：**上下**。
   - 第二个值：**左右**。
   ```css
   margin: 10px 20px; /* 上下10px，左右20px */
   padding: 10px 20px; /* 上下10px，左右20px */
   ```

3. **三个值**：
   - 第一个值：**上**。
   - 第二个值：**左右**。
   - 第三个值：**下**。
   ```css
   margin: 10px 20px 30px; /* 上10px，左右20px，下30px */
   padding: 10px 20px 30px; /* 上10px，左右20px，下30px */
   ```

4. **四个值**：
   - 分别表示**上、右、下、左**（顺时针方向）。
   ```css
   margin: 10px 20px 30px 40px; /* 上10px，右20px，下30px，左40px */
   padding: 10px 20px 30px 40px; /* 上10px，右20px，下30px，左40px */
   ```

---

### 记忆口诀
- **1 个值**：全一样。
- **2 个值**：上下，左右。
- **3 个值**：上，左右，下。
- **4 个值**：上，右，下，左（顺时针）。

通过这种方式，你可以快速写出 `margin` 和 `padding` 的简写形式！