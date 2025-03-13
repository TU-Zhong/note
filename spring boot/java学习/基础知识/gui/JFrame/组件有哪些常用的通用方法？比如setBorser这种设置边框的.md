

以下是 Java Swing 组件常用的通用方法及其说明，涵盖外观、布局、状态和功能控制等方面：

---

### **一、外观与样式**
| 方法                                       | 说明                                                                 | 示例                                                                 |
|--------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `setBackground(Color color)`               | 设置组件背景色                                                       | `button.setBackground(Color.RED);`                                  |
| `setForeground(Color color)`               | 设置组件前景色（如文本颜色）                                         | `label.setForeground(new Color(0, 100, 0));`                        |
| `setFont(Font font)`                       | 设置组件字体                                                         | `textField.setFont(new Font("宋体", Font.BOLD, 14));`               |
| **`setBorder(Border border)`**             | 设置组件的边框（需配合 `BorderFactory`）                             | `panel.setBorder(BorderFactory.createLineBorder(Color.BLUE, 2));`   |
| `setOpaque(boolean opaque)`                | 设置组件是否不透明（需为 `true` 才能显示背景色）                     | `panel.setOpaque(true);`                                            |

---

### **二、尺寸与布局**
| 方法                                       | 说明                                                                 | 示例                                                                 |
|--------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `setPreferredSize(Dimension size)`         | 设置首选大小（布局管理器参考此值）                                   | `button.setPreferredSize(new Dimension(100, 30));`                  |
| `setMinimumSize(Dimension size)`           | 设置最小大小                                                         | `panel.setMinimumSize(new Dimension(200, 200));`                    |
| `setMaximumSize(Dimension size)`           | 设置最大大小                                                         | `textArea.setMaximumSize(new Dimension(400, 300));`                 |
| `setAlignmentX(float alignment)`           | 设置组件在 X 轴方向的对齐方式（范围 `0.0f`~`1.0f`，左到右）          | `label.setAlignmentX(Component.LEFT_ALIGNMENT);`                    |
| `setAlignmentY(float alignment)`           | 设置组件在 Y 轴方向的对齐方式（范围 `0.0f`~`1.0f`，上到下）          | `button.setAlignmentY(Component.CENTER_ALIGNMENT);`                 |

---

### **三、状态与交互**
| 方法                                       | 说明                                                                 | 示例                                                                 |
|--------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `setEnabled(boolean enabled)`              | 启用或禁用组件（禁用时变灰且不响应事件）                             | `button.setEnabled(false);`                                         |
| `setVisible(boolean visible)`              | 显示或隐藏组件                                                       | `panel.setVisible(false);`                                          |
| `setToolTipText(String text)`              | 设置鼠标悬停时的提示文本                                             | `button.setToolTipText("点击提交表单");`                            |

---

### **四、功能与事件**
| 方法                                       | 说明                                                                 | 示例                                                                 |
|--------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `addMouseListener(MouseListener listener)` | 添加鼠标事件监听器                                                   | `label.addMouseListener(new MouseAdapter() { ... });`               |
| `revalidate()`                             | 重新计算布局（常用于动态添加/移除组件后刷新界面）                    | `panel.add(new JButton("新按钮")); panel.revalidate();`             |
| `repaint()`                                | 强制重绘组件（用于自定义绘制后的刷新）                               | `customPanel.repaint();`                                            |

---

### **五、其他实用方法**
| 方法                                       | 说明                                                                 | 示例                                                                 |
|--------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `setName(String name)`                     | 为组件设置唯一标识（便于调试或查找）                                 | `textField.setName("username");`                                    |
| `putClientProperty(Object key, Object val)`| 存储自定义数据（键值对形式，与组件绑定）                             | `button.putClientProperty("userRole", "admin");`                    |

---
### **七、完整示例代码**
```java
import javax.swing.*;
import javax.swing.border.TitledBorder;
import java.awt.*;

public class CommonMethodsExample extends JFrame {
    public CommonMethodsExample() {
        setTitle("通用方法示例");
        setSize(400, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel panel = new JPanel();
        panel.setLayout(new FlowLayout());
        panel.setBackground(Color.WHITE);
        panel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10)); // 内边距

        // 按钮样式设置
        JButton button = new JButton("提交");
        button.setBackground(new Color(0, 120, 215));
        button.setForeground(Color.WHITE);
        button.setFont(new Font("微软雅黑", Font.BOLD, 12));
        button.setToolTipText("点击以保存数据");

        // 文本框
        JTextField textField = new JTextField(15);
        textField.setBorder(BorderFactory.createLineBorder(Color.GRAY, 1));

        // 带标题的面板
        JPanel subPanel = new JPanel();
        subPanel.setBorder(BorderFactory.createTitledBorder(
            BorderFactory.createLineBorder(Color.LIGHT_GRAY),
            "输入区域",
            TitledBorder.LEFT,
            TitledBorder.TOP,
            new Font("宋体", Font.ITALIC, 12),
            Color.DARK_GRAY
        ));
        subPanel.add(textField);

        panel.add(subPanel);
        panel.add(button);
        add(panel);

        setVisible(true);
    }

    public static void main(String[] args) {
        new CommonMethodsExample();
    }
}
```

---

通过合理使用这些通用方法，可以快速定制 Swing 组件的外观和交互行为，提升 GUI 应用的视觉效果和用户体验。