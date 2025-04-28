1. 线程代码分析 
	[[线程启动代码分析]]
2. 在JAVA中，假设A有构造方法A(int a)，则在类A的其他构造方法中调用该构造方法和语句格式应该为（）
	[[在JAVA中，假设A有构造方法A(int a)，则在类A的其他构造方法中调用该构造方法和语句格式应该为（）]]
3. equals()和==的区别
	 [Java中equals()方法的使用-CSDN博客](https://blog.csdn.net/BravoNathan/article/details/122098239)
4. java 虚拟机 堆的详细知识（重点）
	[Java内存区域详解（重点） | JavaGuide](https://javaguide.cn/java/jvm/memory-area.html#%E6%9C%AC%E5%9C%B0%E6%96%B9%E6%B3%95%E6%A0%88)
	方法区：
	[[方法区]]
	[Java内存区域详解（重点） | JavaGuide](https://javaguide.cn/java/jvm/memory-area.html#%E6%96%B9%E6%B3%95%E5%8C%BA)
	运行时常量池
	[[运行时常量池]]
	[Java内存区域详解（重点） | JavaGuide](https://javaguide.cn/java/jvm/memory-area.html#%E8%BF%90%E8%A1%8C%E6%97%B6%E5%B8%B8%E9%87%8F%E6%B1%A0)
	
5. 数值型变量在默认情况下为Int型，byte和short型在计算时会自动转换为int型计算，结果也是int 型。所以a1*a2的结果是int 型的。
6. 通过阅读源码可以知道，string与stringbuffer都是通过字符数组实现的。 
	  其中string的字符数组是<mark style="background: #FFF3A3A6;">final修饰</mark>的，所以字符数组不可以修改。 
	  stringbuffer的字符数组<mark style="background: #FFF3A3A6;">没有final</mark>修饰，所以字符数组可以修改。 
	 <mark style="background: #FFF3A3A6;"> string与stringbuffer都是final修饰</mark>，只是限制他们所存储的引用地址不可修改。 
	  至于地址所指内容能不能修改，则需要看字符数组可不可以修改。 
7. ![[Pasted image 20250428100850.png]]
8. String  intern()方法的作用
	[Java基础常见面试题总结(中) | JavaGuide](https://javaguide.cn/java/basis/java-basic-questions-02.html#string-intern-%E6%96%B9%E6%B3%95%E6%9C%89%E4%BB%80%E4%B9%88%E4%BD%9C%E7%94%A8)
9. String +的时候会发生什么
	
	[Java基础常见面试题总结(中) | JavaGuide](https://javaguide.cn/java/basis/java-basic-questions-02.html#string-%E7%B1%BB%E5%9E%8B%E7%9A%84%E5%8F%98%E9%87%8F%E5%92%8C%E5%B8%B8%E9%87%8F%E5%81%9A-%E8%BF%90%E7%AE%97%E6%97%B6%E5%8F%91%E7%94%9F%E4%BA%86%E4%BB%80%E4%B9%88)
10. hashCode()和equals();