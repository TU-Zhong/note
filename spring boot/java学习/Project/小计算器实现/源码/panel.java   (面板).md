```java
package Calculator;  
  
/*  
 * Created by JFormDesigner on Sat Mar 22 11:44:35 CST 2025 */  
  
import com.formdev.flatlaf.FlatDarculaLaf;  
import com.formdev.flatlaf.intellijthemes.materialthemeuilite.FlatAtomOneLightIJTheme;  
import com.formdev.flatlaf.intellijthemes.materialthemeuilite.FlatGitHubDarkIJTheme;  
  
import java.awt.*;  
import java.awt.event.MouseAdapter;  
import java.awt.event.MouseEvent;  
import javax.swing.*;  
import javax.swing.border.*;  
  
/**  
 * @author Administrator  
 */public class panel extends JFrame {  
    private StringBuilder textArea;  
    private check Check;  
    public panel() {  
        initComponents();  
    }  
    private find test;  
    private void initComponents() {  
        textArea=new StringBuilder();  
        Check=new check();  
        test =new find();  
        setDefaultCloseOperation(EXIT_ON_CLOSE);  
        // JFormDesigner - Component initialization - DO NOT MODIFY  //GEN-BEGIN:initComponents  @formatter:off  
        panel1 = new JPanel();  
        button1 = new JButton();  
        button2 = new JButton();  
        button3 = new JButton();  
        panel3 = new JPanel();  
        textField1 = new JTextField();  
        textField2 = new JTextField();  
        panel2 = new JPanel();  
        button5 = new JButton();  
        button6 = new JButton();  
        button7 = new JButton();  
        button13 = new JButton();  
        button14 = new JButton();  
        button15 = new JButton();  
        button16 = new JButton();  
        button18 = new JButton();  
        button19 = new JButton();  
        button20 = new JButton();  
        button21 = new JButton();  
        button8 = new JButton();  
        button9 = new JButton();  
        button10 = new JButton();  
        button23 = new JButton();  
        button24 = new JButton();  
        button25 = new JButton();  
        button26 = new JButton();  
        button11 = new JButton();  
        button12 = new JButton();  
        button17 = new JButton();  
        button28 = new JButton();  
        button29 = new JButton();  
        button30 = new JButton();  
        button31 = new JButton();  
        button22 = new JButton();  
        button27 = new JButton();  
        button32 = new JButton();  
        button33 = new JButton();  
        button34 = new JButton();  
        button35 = new JButton();  
        button36 = new JButton();  
        button37 = new JButton();  
        button39 = new JButton();  
        button41 = new JButton();  
        button38 = new JButton();  
        button4 = new JButton();  
        button40 = new JButton();  
        button45 = new JButton();  
        button42 = new JButton();  
        button43 = new JButton();  
  
        //======== this ========  
        setVisible(true);  
        setPreferredSize(new Dimension(14, 35));  
        setResizable(false);  
        var contentPane = getContentPane();  
        contentPane.setLayout(new BorderLayout());  
  
        //======== panel1 ========  
        {  
            panel1.setBorder(LineBorder.createBlackLineBorder());  
            panel1.setPreferredSize(new Dimension(224, 20));  
            panel1.setLayout(new BoxLayout(panel1, BoxLayout.X_AXIS));  
  
            //---- button1 ----  
            button1.setText("\u67e5\u770b(V)");  
            button1.setBorder(LineBorder.createBlackLineBorder());  
            button1.setBorderPainted(false);  
            button1.setHorizontalAlignment(SwingConstants.LEFT);  
            panel1.add(button1);  
  
            //---- button2 ----  
            button2.setText("\u7f16\u8f91(E)");  
            button2.setBorder(LineBorder.createBlackLineBorder());  
            button2.setBorderPainted(false);  
            panel1.add(button2);  
  
            //---- button3 ----  
            button3.setText("\u5e2e\u52a9(H)");  
            button3.setBorder(LineBorder.createBlackLineBorder());  
            button3.setBorderPainted(false);  
            panel1.add(button3);  
        }  
        contentPane.add(panel1, BorderLayout.NORTH);  
  
        //======== panel3 ========  
        {  
            panel3.setLayout(new BorderLayout());  
            panel3.add(textField1, BorderLayout.NORTH);  
            panel3.add(textField2, BorderLayout.CENTER);  
        }  
        contentPane.add(panel3, BorderLayout.CENTER);  
  
        //======== panel2 ========  
        {  
            panel2.setLayout(new GridBagLayout());  
            ((GridBagLayout)panel2.getLayout()).columnWidths = new int[] {0, 0, 0, 0, 0, 0, 0, 0};  
            ((GridBagLayout)panel2.getLayout()).rowHeights = new int[] {0, 0, 0, 0, 0, 0, 0};  
            ((GridBagLayout)panel2.getLayout()).columnWeights = new double[] {0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0E-4};  
            ((GridBagLayout)panel2.getLayout()).rowWeights = new double[] {0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0E-4};  
  
            //---- button5 ----  
            button5.setText("M+");  
            button5.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button5, new GridBagConstraints(4, 0, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.WEST, GridBagConstraints.VERTICAL,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button6 ----  
            button6.setText("M-");  
            button6.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button6, new GridBagConstraints(5, 0, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button7 ----  
            button7.setText("MR");  
            button7.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button7, new GridBagConstraints(6, 0, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button13 ----  
            button13.addActionListener(e->{  
                textArea.append("(");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                error(Check.Check(str));  
            });  
            button13.setText("\uff08");  
            button13.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button13, new GridBagConstraints(0, 0, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button14 ----  
            button14.addActionListener(e->{  
                textArea.append(")");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
  
            });  
            button14.setText(")");  
            button14.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button14, new GridBagConstraints(1, 0, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button15 ----  
            button15.addActionListener(e->{  
                textArea.append("^(-1)");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button15.setText("1/x");  
            button15.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button15, new GridBagConstraints(2, 0, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button16 ----  
            button16.setText("MC");  
            button16.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button16, new GridBagConstraints(3, 0, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button18 ----  
            button18.addActionListener(e->{  
                textArea.append("^(2)");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button18.setText("x\u00b2");  
            button18.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button18, new GridBagConstraints(0, 1, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button19 ----  
            button19.addActionListener(e->{  
                textArea.append("^(3)");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button19.setText("x\u00b3");  
            button19.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button19, new GridBagConstraints(1, 1, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button20 ----  
            button20.addActionListener(e->{  
                textArea.append("^(");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button20.setText("x^y");  
            button20.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button20, new GridBagConstraints(2, 1, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button21 ----  
            button21.addActionListener(e->{  
                textArea.delete(0,textArea.length());  
                textField1.setText(textArea.toString());  
                System.out.println(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText("");  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button21.setText("C");  
            button21.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button21, new GridBagConstraints(3, 1, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button8 ----  
            button8.addActionListener(e->{  
                textArea.append("÷");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button8.setText("\u00f7");  
            button8.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button8, new GridBagConstraints(4, 1, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button9 ----  
            button9.addActionListener(e->{  
                textArea.append("×");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button9.setText("\u00d7");  
            button9.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button9, new GridBagConstraints(5, 1, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
            //---- button10 ----  
            button10.addActionListener(e->{  
                if(!textArea.isEmpty()){  
                    textArea.deleteCharAt(textArea.length()-1);  
                }  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else if(!str.isEmpty()){  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button10.setText("back");  
            button10.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button10, new GridBagConstraints(6, 1, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button23 ----  
            button23.addActionListener(e->{  
                textArea.append("!");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button23.setText("x!");  
            button23.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button23, new GridBagConstraints(0, 2, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button24 ----  
            button24.addActionListener(e->{  
                textArea.append("√");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button24.setText("\u221a");  
            button24.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button24, new GridBagConstraints(1, 2, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button25 ----  
            button25.addActionListener(e->{  
                textArea.append("^(1÷");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button25.setText("y\u221ax");  
            button25.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button25, new GridBagConstraints(2, 2, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button26 ----  
            button26.addActionListener(e->{  
                textArea.append("7");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button26.setText("7");  
            button26.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button26, new GridBagConstraints(3, 2, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button11 ----  
            button11.addActionListener(e->{  
                textArea.append("8");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button11.setText("8");  
            button11.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button11, new GridBagConstraints(4, 2, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button12 ----  
            button12.addActionListener(e->{  
                textArea.append("9");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button12.setText("9");  
            button12.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button12, new GridBagConstraints(5, 2, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button17 ----  
            button17.addActionListener(e->{  
                textArea.append("-");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button17.setText("-");  
            button17.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button17, new GridBagConstraints(6, 2, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button28 ----  
            button28.addActionListener(e->{  
                textArea.append("e");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button28.setText("e");  
            button28.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button28, new GridBagConstraints(0, 3, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button29 ----  
            button29.addActionListener(e->{  
                textArea.append("ln(");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button29.setText("ln");  
            button29.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button29, new GridBagConstraints(1, 3, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button30 ----  
            button30.addActionListener(e->{  
                textArea.append("log(");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button30.setText("log");  
            button30.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button30, new GridBagConstraints(2, 3, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button31 ----  
            button31.addActionListener(e->{  
                textArea.append("4");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button31.setText("4");  
            button31.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button31, new GridBagConstraints(3, 3, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button22 ----  
            button22.addActionListener(e->{  
                textArea.append("5");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button22.setText("5");  
            button22.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button22, new GridBagConstraints(4, 3, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button27 ----  
            button27.addActionListener(e->{  
                textArea.append("6");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button27.setText("6");  
            button27.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button27, new GridBagConstraints(5, 3, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button32 ----  
            button32.addActionListener(e->{  
                textArea.append("+");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button32.setText("+");  
            button32.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button32, new GridBagConstraints(6, 3, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button33 ----  
            button33.addActionListener(e->{  
                textArea.append("sin(");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button33.setText("sin");  
            button33.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button33, new GridBagConstraints(0, 4, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button34 ----  
            button34.addActionListener(e->{  
                textArea.append("cos(");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button34.setText("cos");  
            button34.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button34, new GridBagConstraints(1, 4, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button35 ----  
            button35.addActionListener(e->{  
                textArea.append("tan(");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button35.setText("tan");  
            button35.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button35, new GridBagConstraints(2, 4, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button36 ----  
            button36.addActionListener(e->{  
                textArea.append("1");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button36.setText("1");  
            button36.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button36, new GridBagConstraints(3, 4, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button37 ----  
            button37.addActionListener(e->{  
                textArea.append("2");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button37.setText("2");  
            button37.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button37, new GridBagConstraints(4, 4, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button39 ----  
            button39.setText("3");  
            button39.addActionListener(e->{  
                textArea.append("3");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button39.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button39, new GridBagConstraints(5, 4, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button41 ----  
            button41.setText("=");  
            button41.addActionListener(e->{  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
                textArea.delete(0,textArea.length());  
                textField1.setText(textArea.toString());  
            });  
            button41.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button41, new GridBagConstraints(6, 4, 1, 2, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button38 ----  
            button38.setText("lnv");  
            button38.addMouseListener(new MouseAdapter() {  
                @Override public void mouseClicked(MouseEvent e) {  
                }});  
            button38.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button38, new GridBagConstraints(0, 5, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button4 ----  
            button4.addMouseListener(new MouseAdapter() {  
                @Override public void mouseClicked(MouseEvent e) {  
                }});  
            button4.setText("Rad");  
            button4.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button4, new GridBagConstraints(1, 5, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button40 ----  
            button40.addActionListener(e->{  
                textArea.append("Π");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button40.setText("\u03a0");  
            button40.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button40, new GridBagConstraints(2, 5, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button45 ----  
            button45.addActionListener(e->{  
                textArea.append("%");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button45.setText("%");  
            button45.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button45, new GridBagConstraints(3, 5, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button42 ----  
            button42.addActionListener(e->{  
                textArea.append("0");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button42.setText("0");  
            button42.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button42, new GridBagConstraints(4, 5, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
  
            //---- button43 ----  
            button43.addActionListener(e->{  
                textArea.append(".");  
                textField1.setText(textArea.toString());  
                String str=textField1.getText();  
                int checked=Check.Check(str);  
                if(checked!=0){  
                    error(Check.Check(str));  
                }else{  
                    try {  
                        textField2.setText(""+test.finalResult(test,str));  
                    } catch (Exception ex) {  
                        throw new RuntimeException(ex);  
                    }  
                }  
            });  
            button43.setText(".");  
            button43.setFont(new Font("Microsoft YaHei UI", Font.BOLD, 18));  
            panel2.add(button43, new GridBagConstraints(5, 5, 1, 1, 0.0, 0.0,  
                    GridBagConstraints.CENTER, GridBagConstraints.BOTH,  
                    new Insets(0, 0, 0, 0), 0, 0));  
        }  
        contentPane.add(panel2, BorderLayout.SOUTH);  
        setSize(545, 450);  
        setLocationRelativeTo(getOwner());  
        // JFormDesigner - End of component initialization  //GEN-END:initComponents  @formatter:on  
    }  
    public void error(int errorLabel){  
        switch (errorLabel){  
            case 0:textField2.setText("");break;  
            case -1: textField2.setText("输入非法字符错误！");break;  
            case 1: textField2.setText("括号不成对！");break;  
            case 2: textField2.setText("函数后面没有紧跟括号！");break;  
            case 3: textField2.setText("运算符位置错误！");break;  
            case 4: textField2.setText("小数点位置错误");break;  
            case 5: textField2.setText("阶乘符位置错误");break;  
            case 6: textField2.setText("首尾符号错误，应以数字，函数，或者（开头；以数字，）或者！结尾 ！");break;  
            default:return;  
        }  
    }  
    // JFormDesigner - Variables declaration - DO NOT MODIFY  //GEN-BEGIN:variables  @formatter:off  
    private JPanel panel1;  
    private JButton button1;  
    private JButton button2;  
    private JButton button3;  
    private JPanel panel3;  
    private JTextField textField1;  
    private JTextField textField2;  
    private JPanel panel2;  
    private JButton button5;  
    private JButton button6;  
    private JButton button7;  
    private JButton button13;  
    private JButton button14;  
    private JButton button15;  
    private JButton button16;  
    private JButton button18;  
    private JButton button19;  
    private JButton button20;  
    private JButton button21;  
    private JButton button8;  
    private JButton button9;  
    private JButton button10;  
    private JButton button23;  
    private JButton button24;  
    private JButton button25;  
    private JButton button26;  
    private JButton button11;  
    private JButton button12;  
    private JButton button17;  
    private JButton button28;  
    private JButton button29;  
    private JButton button30;  
    private JButton button31;  
    private JButton button22;  
    private JButton button27;  
    private JButton button32;  
    private JButton button33;  
    private JButton button34;  
    private JButton button35;  
    private JButton button36;  
    private JButton button37;  
    private JButton button39;  
    private JButton button41;  
    private JButton button38;  
    private JButton button4;  
    private JButton button40;  
    private JButton button45;  
    private JButton button42;  
    private JButton button43;  
    // JFormDesigner - End of variables declaration  //GEN-END:variables  @formatter:on  
    public static void main(String[] args) {  
        FlatGitHubDarkIJTheme.installLafInfo();  
        try {  
            UIManager.setLookAndFeel( new FlatGitHubDarkIJTheme());  
        } catch( Exception ex ) {  
            System.err.println( "Failed to initialize LaF" );  
        }  
        new panel();  
    }  
}
```