

在 Java Swing 中，`Box.createXxx()` 方法生成的组件通常用于辅助布局调整，尤其在 `BoxLayout` 或需要灵活间距的场景下。以下是 `Box` 类在 `add()` 方法中的常用用法及其作用：

---

### **1. `Box.createHorizontalGlue()`**
#### **作用**  
创建一个 **水平方向的弹性空白区域**，自动填充剩余水平空间，推动相邻组件向两端对齐。

#### **示例代码**  
```java
JPanel panel = new JPanel();
panel.setLayout(new BoxLayout(panel, BoxLayout.X_AXIS)); // 水平排列
panel.add(new JButton("左侧按钮"));
panel.add(Box.createHorizontalGlue()); // 中间弹簧
panel.add(new JButton("右侧按钮"));
```
**效果**：右侧按钮会被推到最右边，左侧按钮保持左对齐，中间区域被弹簧占据。

---

### **2. `Box.createVerticalGlue()`**
#### **作用**  
创建一个 **垂直方向的弹性空白区域**，自动填充剩余垂直空间，推动相邻组件向上下两端对齐。

#### **示例代码**  
```java
JPanel panel = new JPanel();
panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS)); // 垂直排列
panel.add(new JButton("顶部按钮"));
panel.add(Box.createVerticalGlue()); // 中间弹簧
panel.add(new JButton("底部按钮"));
```
**效果**：底部按钮被推到最下方，顶部按钮保持在上方。

---

### **3. `Box.createHorizontalStrut(int width)`**
#### **作用**  
创建一个 **固定宽度的不可见组件**，用于在水平布局中插入固定间距。

#### **示例代码**  
```java
panel.add(new JButton("按钮1"));
panel.add(Box.createHorizontalStrut(20)); // 插入20px的固定间距
panel.add(new JButton("按钮2"));
```
**效果**：按钮1和按钮2之间保持 20px 的固定间距。

---

### **4. `Box.createVerticalStrut(int height)`**
#### **作用**  
创建一个 **固定高度的不可见组件**，用于在垂直布局中插入固定间距。

#### **示例代码**  
```java
panel.add(new JButton("按钮1"));
panel.add(Box.createVerticalStrut(30)); // 插入30px的固定间距
panel.add(new JButton("按钮2"));
```
**效果**：按钮1和按钮2之间保持 30px 的垂直间距。

---

### **5. `Box.createRigidArea(Dimension size)`**
#### **作用**  
创建一个 **固定尺寸的不可见区域**，可同时控制宽度和高度。

#### **示例代码**  
```java
panel.add(new JButton("按钮"));
panel.add(Box.createRigidArea(new Dimension(50, 100))); // 50px宽，100px高
panel.add(new JButton("按钮"));
```
**效果**：两个按钮之间插入一个 50x100px 的不可见区域。

---

### **6. `Box.createGlue()`**
#### **作用**  
创建一个 **双向弹性空白区域**（根据布局方向自动切换水平或垂直弹性）。

#### **示例代码**  
```java
JPanel panel = new JPanel();
panel.setLayout(new BoxLayout(panel, BoxLayout.X_AXIS));
panel.add(Box.createGlue()); // 方向由布局决定，此处为水平弹性
panel.add(new JButton("居中按钮"));
panel.add(Box.createGlue());
```
**效果**：按钮在容器中水平居中，两侧弹性区域平分剩余空间。

---

### **7. 复杂布局示例**
结合 `GridBagLayout` 使用 `Box.createHorizontalGlue()` 实现输入框拉伸：
```java
GridBagConstraints gbc = new GridBagConstraints();
gbc.gridx = 1;
gbc.gridy = 0;
gbc.weightx = 1.0; // 允许列扩展
gbc.fill = GridBagConstraints.HORIZONTAL;
panel.add(Box.createHorizontalGlue(), gbc); // 弹性占位
```

---

### **总结表格**
| 方法                          | 作用                           | 适用布局方向      | 场景示例                     |
|-------------------------------|--------------------------------|------------------|----------------------------|
| `createHorizontalGlue()`      | 水平弹性填充剩余空间             | 水平布局 (`X_AXIS`) | 按钮靠右对齐                 |
| `createVerticalGlue()`        | 垂直弹性填充剩余空间             | 垂直布局 (`Y_AXIS`) | 组件底部对齐                 |
| `createHorizontalStrut(int)`  | 插入固定水平间距                 | 水平布局          | 按钮间固定间距               |
| `createVerticalStrut(int)`    | 插入固定垂直间距                 | 垂直布局          | 标签和输入框之间固定间距     |
| `createRigidArea(Dimension)`  | 插入固定二维区域                 | 任意布局          | 复杂间距调整               |
| `createGlue()`                | 根据布局方向弹性填充（自动适配）  | 任意布局          | 组件居中或两端对齐           |

---

### **关键注意事项**
1. **权重（`weightx`/`weighty`）配合使用**：  
   在 `GridBagLayout` 中，`Box.createHorizontalGlue()` 需结合 `weightx=1.0` 才能生效。
2. **动态调整**：  
   若窗口大小变化后布局异常，调用 `revalidate()` 和 `repaint()` 刷新界面。
3. **组合使用**：  
   弹性组件和固定组件可组合使用，实现复杂对齐（如顶部留空 + 底部按钮靠右）。

通过灵活组合这些方法，可以轻松实现 Swing 界面中的组件对齐和间距控制。