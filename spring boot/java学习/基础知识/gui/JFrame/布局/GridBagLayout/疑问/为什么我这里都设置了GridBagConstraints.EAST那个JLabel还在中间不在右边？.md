

出现 JLabel 未靠右对齐的问题，通常是因为 **未正确理解 `GridBagConstraints` 的约束范围** 或 **缺少关键参数设置**。以下是具体原因和解决方案：

---

### **原因分析**
1. **约束范围仅作用于分配的单元格**  
   `anchor=EAST` 的作用是让标签在 **其所在的单元格内** 靠右对齐。如果单元格本身的宽度等于标签的宽度，即使靠右，视觉上也不会变化。

2. **未设置列的扩展权重（`weightx`）**  
   如果标签所在列（`gridx=0`）的 `weightx=0`（默认值），该列不会扩展，导致单元格宽度等于标签宽度，靠右对齐后看起来仍居中。

---

### **解决方案**
#### **方法1：让单元格宽度大于标签宽度**
通过设置标签所在列的 `weightx`，使该列扩展，从而产生右侧空间：
```java
GridBagConstraints gbc = new GridBagConstraints();
gbc.gridx = 0;
gbc.gridy = 0;
gbc.anchor = GridBagConstraints.EAST;

// 关键：设置该列的权重，让列扩展
gbc.weightx = 1.0; // 该列会分到额外空间

JLabel label = new JLabel("用户名：");
panel.add(label, gbc);
```

#### **方法2：增加标签所在列的组件（强制扩展）**
在同一列添加一个占位组件（如 `Box.createHorizontalGlue()`），强制该列扩展：
```java
GridBagConstraints gbc = new GridBagConstraints();
gbc.gridx = 0;
gbc.gridy = 0;
gbc.anchor = GridBagConstraints.EAST;
panel.add(new JLabel("用户名："), gbc);

// 同一列添加占位组件，占据剩余空间
gbc.gridy = 1; // 下一行
panel.add(Box.createVerticalGlue(), gbc);
```

---

### **完整示例代码**
```java
import javax.swing.*;
import java.awt.*;

public class GridBagLabelExample extends JFrame {
    public GridBagLabelExample() {
        setTitle("标签靠右示例");
        setSize(400, 200);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel panel = new JPanel(new GridBagLayout());
        add(panel);

        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(5, 5, 5, 5);

        // 1. 第0列设置权重（关键！）
        gbc.weightx = 1.0; // 让该列自动扩展

        // 用户名标签（第0行，第0列）
        gbc.gridx = 0;
        gbc.gridy = 0;
        gbc.anchor = GridBagConstraints.EAST;
        panel.add(new JLabel("用户名："), gbc);

        // 2. 输入框（第0行，第1列，占2列）
        JTextField userField = new JTextField(15);
        gbc.gridx = 1;
        gbc.gridwidth = 2;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        panel.add(userField, gbc);

        setVisible(true);
    }

    public static void main(String[] args) {
        new GridBagLabelExample();
    }
}
```

---

### **效果对比**
| **未设置 `weightx=1.0`**      | **设置 `weightx=1.0`**            |
|-------------------------------|-----------------------------------|
| ![未设置权重](https://via.placeholder.com/300x100/FF9999/FFFFFF?text=Label%E5%B1%85%E4%B8%AD) | ![设置权重](https://via.placeholder.com/300x100/99FF99/FFFFFF?text=Label%E9%9D%A0%E5%8F%B3) |

---

### **总结**
- **`anchor=EAST` 仅在单元格有额外空间时生效** → 必须通过 `weightx` 或占位组件扩展列宽。
- **列宽扩展后，标签右侧才会出现空间** → 靠右对齐的视觉效果才会明显。