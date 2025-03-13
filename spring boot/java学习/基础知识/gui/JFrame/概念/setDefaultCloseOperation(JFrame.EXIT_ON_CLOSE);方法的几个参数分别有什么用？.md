

`setDefaultCloseOperation(int operation)` 方法用于设置用户点击窗口关闭按钮（×）时的默认行为。以下是其参数的详细说明及用途：

---

### **参数选项（来自 `JFrame` 常量）**

| 参数值（常量）               | 说明                                                                 | 适用场景                     |
|------------------------------|----------------------------------------------------------------------|------------------------------|
| `JFrame.EXIT_ON_CLOSE`        | **直接终止程序**<br>关闭窗口并退出整个 Java 虚拟机（结束所有线程）    | 主窗口关闭时需要完全退出程序 |
| `JFrame.HIDE_ON_CLOSE`        | **隐藏窗口**<br>窗口不可见，但程序仍在运行（默认行为）               | 临时隐藏窗口，保留后台状态   |
| `JFrame.DISPOSE_ON_CLOSE`     | **释放窗口资源**<br>关闭窗口并释放资源，其他窗口未关闭时程序不终止    | 多窗口应用中的子窗口关闭     |
| `JFrame.DO_NOTHING_ON_CLOSE`  | **无操作**<br>点击关闭按钮无反应（需自定义关闭逻辑）                 | 需要拦截关闭事件（如弹窗确认） |

---

### **示例代码对比**
#### 1. 终止程序（`EXIT_ON_CLOSE`）
```java
JFrame frame = new JFrame();
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 关闭窗口后程序终止
frame.setVisible(true);
```

#### 2. 隐藏窗口（`HIDE_ON_CLOSE`）
```java
frame.setDefaultCloseOperation(JFrame.HIDE_ON_CLOSE); // 窗口隐藏，程序仍在后台运行
```

#### 3. 释放资源（`DISPOSE_ON_CLOSE`）
```java
JFrame childFrame = new JFrame();
childFrame.setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE); // 关闭子窗口，主程序继续运行
```

#### 4. 自定义关闭逻辑（`DO_NOTHING_ON_CLOSE`）
```java
frame.setDefaultCloseOperation(JFrame.DO_NOTHING_ON_CLOSE);
frame.addWindowListener(new WindowAdapter() {
    @Override
    public void windowClosing(WindowEvent e) {
        int choice = JOptionPane.showConfirmDialog(
            frame,
            "确定要退出吗？",
            "确认",
            JOptionPane.YES_NO_OPTION
        );
        if (choice == JOptionPane.YES_OPTION) {
            frame.dispose(); // 手动释放资源
            // System.exit(0); // 或者直接退出程序
        }
    }
});
```

---

### **注意事项**
1. **默认行为**：如果未调用此方法，默认值为 `HIDE_ON_CLOSE`（仅隐藏窗口）。
2. **多窗口场景**：
   - 使用 `DISPOSE_ON_CLOSE` 时，若所有窗口均已关闭，程序可能自动退出（依赖是否还有非守护线程）。
3. **资源释放**：
   - `EXIT_ON_CLOSE` 会直接终止所有线程，可能跳过资源清理代码（如文件关闭）。
   - 需要安全退出时，建议在关闭前手动释放资源。

---

通过合理选择参数，可以控制窗口关闭时的行为，确保程序按预期运行。