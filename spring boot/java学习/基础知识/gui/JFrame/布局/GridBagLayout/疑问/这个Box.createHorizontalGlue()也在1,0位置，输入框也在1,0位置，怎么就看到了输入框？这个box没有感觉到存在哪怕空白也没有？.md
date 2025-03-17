

在 GridBagLayout 中，当多个组件被添加到同一单元格（相同的 `gridx` 和 `gridy`）时，默认情况下 **后续添加的组件会覆盖之前的组件**，但具体表现取决于各组件的约束条件。以下是您代码中两个组件（输入框和 Box）在位置 `(1,0)` 的详细解析：

---

### **关键原因分析**
1. **输入框的 `gridwidth=2`**  
   输入框从 `gridx=1` 开始，占据 **2 列宽度**（即 `gridx=1` 和 `gridx=2`）。因此：
   - 输入框实际覆盖了 `gridx=1` 和 `gridx=2` 的单元格。
   - Box 被添加到 `gridx=1`、`gridy=0` 的单元格，但仅占据 **1 列**（默认 `gridwidth=1`）。

2. **Box 的约束条件问题**  
   - **`weightx=1.0`**：允许该列（`gridx=1`）扩展。
   - **未设置 `fill`**：Box 默认不填充空间（尺寸为 0），导致虽然列扩展了，但 Box 自身不可见。
   - **输入框的 `fill=HORIZONTAL`**：输入框会填满其占据的 2 列宽度，覆盖 Box。

3. **最终效果**  
   - **Box 的作用**：通过 `weightx=1.0` 让 `gridx=1` 列扩展，但自身不显示。
   - **输入框的表现**：受益于列的扩展，输入框横向拉伸，覆盖 Box 的位置。

---

### **可视化布局**
```
列号：0          1           2
行0：[标签] [输入框（占1-2列）]
行1：[标签] [密码框（占1-2列）]
行2：         [登录按钮（在2列）]
```

- **`Box` 的位置**：仅在 `(1,0)`，但输入框占据了 `(1,0)` 和 `(2,0)`。
- **`Box` 的显示**：宽度为 0（不可见），但通过 `weightx=1.0` 让列扩展，间接帮助输入框拉伸。

---

### **为什么看不到 Box？**
1. **Box 未设置填充方式**  
   `Box.createHorizontalGlue()` 默认不填充空间（相当于 `fill=NONE`），因此实际宽度为 0。

2. **输入框覆盖了 Box**  
   输入框的 `fill=HORIZONTAL` 和 `gridwidth=2` 使得它占据更多空间，视觉上覆盖了 Box。

---

### **验证方法**
若想看到 Box 的存在，可以临时修改代码：
1. **为 Box 设置背景色和填充**  
   ```java
   JComponent glue = Box.createHorizontalGlue();
   glue.setOpaque(true);
   glue.setBackground(Color.RED);
   gbc.fill = GridBagConstraints.HORIZONTAL; // 设置填充
   panel.add(glue, gbc);
   ```
   - 此时会看到红色区域，但输入框会覆盖它。

---

### **总结**
- **看似矛盾的现象**  
  Box 和输入框处于同一单元格，但由于输入框跨列且填充空间，Box 的存在仅通过 `weightx=1.0` 间接影响列宽，自身不可见。

- **设计意图**  
  这种写法是为了让输入框所在列（`gridx=1`）能够扩展，而无需手动计算其他组件的约束条件。

---

### **改进建议**
若希望更直观地控制布局，可以：
1. **为输入框单独设置 `weightx=1.0`**  
   ```java
   gbc.gridx = 1;
   gbc.gridy = 0;
   gbc.gridwidth = 2;
   gbc.weightx = 1.0;  // 直接设置输入框的权重
   gbc.fill = GridBagConstraints.HORIZONTAL;
   panel.add(userField, gbc);
   ```
2. **移除冗余的 Box 组件**  
   ```java
   // 删除以下代码：
   // gbc.gridx = 1;
   // gbc.gridy = 0;
   // panel.add(Box.createHorizontalGlue(), gbc);
   ```

---

通过理解 `weightx` 和 `fill` 的配合逻辑，可以更高效地利用 GridBagLayout 实现复杂界面。