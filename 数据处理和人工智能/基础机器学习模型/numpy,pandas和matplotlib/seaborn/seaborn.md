好的，这是一份为您精心准备的 Seaborn 系统学习指南，包含 **6个核心知识点** 和 **150道实战练习题**。

本计划旨在覆盖 Seaborn 的所有核心功能，从基本样式设置到各类统计图形的绘制与深度定制，帮助您全面掌握这个强大的数据可视化库。

---

### **学习结构**

1. **核心知识点 (Core Concepts)**: 介绍 Seaborn 的主要功能模块和设计理念。
    
2. **准备数据集 (Dataset Setup)**: Seaborn 自带了丰富的数据集，我们将直接加载使用。
    
3. **练习题 (Practice Questions)**: 围绕各类图形的实战应用，共150道。
    

---

### **准备工作：安装必要的库**

Python

```
pip install seaborn pandas matplotlib
```

---

### **模块一：Seaborn 介绍与环境美化 (15题)**

**核心知识点**:

- Seaborn 与 Matplotlib 的关系：Seaborn 是 Matplotlib 的高级封装。
    
- 主题与风格设置: `sns.set_theme()`, `sns.set_style()`, `sns.set_context()`。
    
- 调色板: `sns.color_palette()`, `palette` 参数的使用。
    
- 理解 Axes-level 函数和 Figure-level 函数的区别。
    

**准备工作**:

Python

```
import seaborn as sns
import matplotlib.pyplot as plt

# 加载数据以备后续使用
tips = sns.load_dataset("tips")
```

**练习题**:

1. 导入 `seaborn` 库，并使用 `sns` 作为别名。
    
2. 使用 `sns.set_theme()` 应用 Seaborn 的默认美化主题到所有后续的图中。
    
3. 设置绘图风格为 'darkgrid'。
    
4. 列出所有可用的 Seaborn 绘图风格 (style)。
    
5. 将绘图上下文 (context) 设置为 'notebook'，以获得适合笔记本展示的元素大小。
    
6. 列出所有可用的绘图上下文 (context)。
    
7. 创建一个包含10种颜色的默认调色板，并使用 `sns.palplot()` 展示它。
    
8. 获取并展示 'deep', 'muted', 'pastel', 'bright', 'dark', 'colorblind' 这几种内置调色板。
    
9. 创建一个 'viridis' 连续型调色板。
    
10. 使用 `sns.despine()` 移除图形的顶部和右侧轴线。
    
11. 什么是 Axes-level 函数？请举一个例子。
    
12. 什么是 Figure-level 函数？它和 Axes-level 函数的主要区别是什么？
    
13. 使用 `sns.set()` (旧版函数) 同时设置主题、风格和调色板。
    
14. 在一个 `with sns.axes_style("whitegrid"):` 代码块中创建一个图形，观察其风格与全局设置的不同。
    
15. 恢复所有主题、风格和上下文设置为默认值。
    

---

### **模块二：关系型绘图 (25题)**

**核心知识点**:

- `scatterplot()`: 散点图，探索两个数值变量间的关系。
    
- `lineplot()`: 线图，通常用于展示一个变量随另一个变量（如时间）变化的趋势。
    
- `relplot()`: Figure-level 接口，可以通过 `kind` 参数创建以上两种图形，并通过 `col` 和 `row` 参数创建分面网格。
    

**准备数据集**:

Python

```
import seaborn as sns
import matplotlib.pyplot as plt

tips = sns.load_dataset("tips")
penguins = sns.load_dataset("penguins")
flights = sns.load_dataset("flights")
```

练习题:

16. 使用 tips 数据集，创建一个散点图展示 total_bill 和 tip 之间的关系。

17. 在上一步的散点图中，使用 hue 参数根据 'smoker' 列为数据点着色。

18. 使用 style 参数根据 'time' 列改变数据点的标记样式。

19. 使用 size 参数根据 'size' 列的大小调整数据点的大小。

20. 创建一个散点图，同时使用 hue, style, size 三个参数来展示多维度信息。

21. 将散点图的透明度 (alpha) 设置为 0.7。

22. 使用 lineplot() 绘制 flights 数据集中每年（'year'）的平均乘客数（'passengers'）。

23. 在上一步的线图中，使用 hue 参数按 'month' 为不同月份绘制独立的线条。

24. lineplot() 默认会显示置信区间，如何隐藏它？

25. 改变线图的线条样式 (dashes) 和标记样式 (markers)。

26. 使用 relplot() 创建一个与问题16完全相同的散点图。

27. 使用 relplot()，根据 'sex' 列将 total_bill vs tip 的散点图绘制在不同的列 (col) 中。

28. 使用 relplot()，根据 'time' 列将图绘制在不同的行 (row) 中。

29. 使用 relplot()，同时使用 row 和 col 参数，根据 'smoker' 和 'time' 创建一个 2x2 的分面网格。

30. 使用 relplot(kind="line", ...) 创建一个与问题22相同的线图。

31. 在 relplot 中，使用 col_wrap 参数将超过3列的分面网格自动换行。

32. 在 scatterplot 中，使用 palette 参数指定一个自定义的颜色盘。

33. 使用 penguins 数据集，绘制 flipper_length_mm 和 bill_length_mm 的关系，并按 species 着色。

34. 使用 lineplot 绘制 penguins 数据集中，每个物种 (species) 的 bill_depth_mm 与 bill_length_mm 的关系。

35. 使用 relplot 探索 penguins 数据集中不同 island 的企鹅 flipper_length_mm 和 body_mass_g 的关系。

36. 在 lineplot 中，设置 estimator=None 来绘制原始数据点而不是聚合后的趋势。

37. 在 scatterplot 的基础上，使用 Matplotlib 命令添加一个标题 "Tip vs Total Bill"。

38. 使用 relplot 后，如何修改每个子图的标题？

39. 调整 relplot 中子图标题的模板。

40. 在 lineplot 中，使用 units 参数来为重复测量数据绘制正确的轨迹。

---

### **模块三：分布型绘图 (35题)**

**核心知识点**:

- `histplot()`: 直方图，展示单个变量的分布。
    
- `kdeplot()`: 核密度估计图，平滑地估计变量的分布。
    
- `ecdfplot()`: 经验累积分布函数图。
    
- `rugplot()`: 在坐标轴上为每个数据点绘制一条小刻度线。
    
- `displot()`: Figure-level 接口，用于绘制以上图形及其组合。
    

**准备数据集**:

Python

```
import seaborn as sns
import matplotlib.pyplot as plt

penguins = sns.load_dataset("penguins")
```

练习题:

41. 使用 penguins 数据集，为 flipper_length_mm 绘制一个直方图。

42. 在直方图上叠加一个核密度估计曲线 (kde=True)。

43. 将直方图的箱数 (bins) 设置为 20。

44. 使用 histplot()，根据 species 将不同物种的鳍长分布绘制在同一个图上，并使用不同颜色。

45. 设置 multiple="stack"，将不同物种的直方图堆叠起来。

46. 使用 kdeplot() 单独绘制 flipper_length_mm 的核密度估计图。

47. 在 kdeplot 中，使用 hue 参数同时绘制三个物种的密度曲线。

48. 设置 fill=True，为密度曲线下方的区域填充颜色。

49. 绘制 flipper_length_mm 和 bill_length_mm 的二维核密度估计图。

50. 在二维 kdeplot 上添加一个颜色条。

51. 使用 ecdfplot() 绘制 flipper_length_mm 的经验累积分布函数图。

52. 使用 hue 参数同时绘制三个物种的 ECDF 曲线。

53. 使用 rugplot() 在 flipper_length_mm 直方图的 x 轴上添加数据刻度线。

54. 使用 displot() 创建一个与问题41完全相同的直方图。

55. 使用 displot(kind="kde", ...) 创建一个与问题46相同的 KDE 图。

56. 使用 displot()，通过 col 参数为每个物种 (species) 单独绘制一列 flipper_length_mm 的直方图。

57. 在 displot 中，设置 rug=True 来为每个子图的分布添加 rugplot。

58. 创建一个 displot，其中行由 sex 决定，列由 species 决定。

59. 使用 histplot 绘制一个二维直方图 (bivariate histogram)。

60. 在二维 histplot 上使用 cbar=True 添加颜色条以表示频数。

61. 在 kdeplot 中，使用 bw_adjust 参数调整带宽，观察曲线平滑度的变化。

62. 在 histplot 中，使用 stat="probability" 将 y 轴转换为概率。

63. 在 histplot 中，使用 element="step" 绘制阶梯状的直方图。

64. 使用 displot，在一个图的中心绘制联合分布，并在边缘绘制各自的单变量分布 (jointplot 的效果)。

65. 什么是 jointplot()？用它绘制 penguins 的 flipper_length_mm 和 bill_length_mm 的联合分布和边缘分布。

66. 在 jointplot() 中，将中心图形的类型 (kind) 改为 'hex'。

67. 什么是 pairplot()？用它创建 penguins 数据集中数值变量两两之间的关系图。

68. 在 pairplot() 中，使用 hue="species" 为不同物种着色。

69. 在 pairplot() 中，使用 diag_kind="kde" 将对角线上的直方图改为 KDE 图。

70. 在 pairplot 中，使用 markers 为不同类别指定不同的标记样式。

71. 如何使用 JointGrid 或 PairGrid 对 jointplot 和 pairplot 进行更底层的定制？

72. 在 kdeplot 中，使用 cut 参数调整曲线超出数据范围的程度。

73. 在 ecdfplot 中，将 y 轴从比例改为计数。

74. 使用 displot，并设置 log_scale=True 来在对数尺度上绘制分布。

75. 在 histplot 中，使用 weights 参数绘制一个加权直方图。

---

### **模块四：分类型绘图 (45题)**

**核心知识点**:

- 分类散点图: `stripplot()` (带状图), `swarmplot()` (蜂群图)。
    
- 分类分布图: `boxplot()` (箱形图), `violinplot()` (小提琴图), `boxenplot()` (增强箱形图)。
    
- 分类估计图: `barplot()` (条形图), `pointplot()` (点图), `countplot()` (计数图)。
    
- `catplot()`: 以上所有分类图的 Figure-level 接口。
    

**准备数据集**:

Python

```
import seaborn as sns
import matplotlib.pyplot as plt

tips = sns.load_dataset("tips")
titanic = sns.load_dataset("titanic")
```

**练习题**:

分类散点图 (76-85)

76. 使用 tips 数据集，绘制 day (x轴) 与 total_bill (y轴) 的 stripplot。

77. 在上一步的图中，数据点有重叠，如何使用 jitter 参数来减少重叠？

78. 使用 swarmplot() 绘制同样的图，观察它如何避免数据点重叠。

79. 在 swarmplot 中，使用 hue 参数根据 'sex' 进行着色。

80. 将 swarmplot 和 violinplot 叠加在同一个图上，以同时展示分布和数据点。

81. 绘制一个水平方向的 stripplot。

82. 在 swarmplot 中，使用 order 参数自定义 x 轴类别的顺序。

83. 调整 swarmplot 中点的大小 (size)。

84. 使用 catplot(kind="strip", ...) 创建一个与问题76相同的图。

85. 使用 catplot 按 'smoker' 列将 day vs total_bill 的 swarmplot 分面显示。

分类分布图 (86-100)

86. 使用 tips 数据集，为每个 day 绘制 total_bill 的箱形图 (boxplot)。

87. 在箱形图中，根据 'smoker' 使用 hue 进行分组。

88. 绘制一个小提琴图 (violinplot) 来展示相同的数据。

89. 在小提琴图中，设置 split=True 将按 hue 分组的两个小提琴合并成一个。

90. 在小提琴图内部，设置 inner="quartiles" 来显示四分位数线。

91. 绘制一个增强箱形图 (boxenplot)，它适合于更大的数据集。

92. 在 boxplot 中，自定义异常值点的样式。

93. 隐藏 boxplot 中的异常值 (showfliers=False)。

94. 使用 catplot(kind="box", ...) 创建分面的箱形图。

95. 结合 swarmplot 和 boxplot，将数据点叠加在箱形图之上。

96. 使用 titanic 数据集，按 'class' 绘制 'age' 的小提琴图。

97. 在上一步的小提琴图中，再按 'sex' 进行 hue 分组。

98. 改变 violinplot 的带宽 (bw) 来调整其平滑度。

99. 在 boxplot 中，使用 width 参数调整箱体的宽度。

100. 使用 catplot 按 'survived' 将 'class' vs 'age' 的小提琴图分行显示。

分类估计图 (101-120)

101. 使用 tips 数据集，绘制一个条形图 (barplot)，展示每个 day 的平均 total_bill。

102. barplot 上的黑线代表什么？如何隐藏它？

103. 在条形图中，使用 estimator 参数将聚合函数从均值改为中位数。

104. 使用 countplot() 统计 titanic 数据集中每个 'class' 的乘客数量。

105. 在 countplot 中，使用 hue 按 'survived' 进一步细分。

106. 绘制一个水平方向的 countplot，统计 titanic 中的 'deck' 分布。

107. 使用 pointplot() 绘制每个 day 的平均 total_bill。

108. 比较 pointplot 和 barplot，pointplot 能更清晰地展示哪些信息？

109. 在 pointplot 中，使用 hue 按 'smoker' 进行分组，并使用 markers 和 linestyles 为不同组设置不同样式。

110. 如何在 pointplot 中连接不同 hue 分组的点 (dodge=True/False)？

111. 使用 barplot 显示 titanic 中不同 'class' 的乘客的幸存率。

112. 在 barplot 中，自定义误差条的样式。

113. 使用 catplot(kind="bar", ...) 创建一个按 'sex' 分面的条形图。

114. 使用 catplot(kind="count", ...) 创建一个按 'alone' 分面的计数图。

115. 使用 catplot(kind="point", ...) 创建一个展示 sex 和 class 交互作用下 survived 概率的点图。

116. 在 barplot 中，改变条形的饱和度 (saturation)。

117. 在 countplot 中，使用 order 参数按特定顺序（如 'First', 'Second', 'Third'）排列 'class'。

118. 在 pointplot 中，设置 join=False 来移除连接点之间的线。

119. 如何在 barplot 的条形上显示具体的数值标签？(提示: 需要结合 Matplotlib)

120. 使用 catplot 创建一个复杂的 2x2 网格，行是 'alone'，列是 'sex'，内容是 'class' vs 'survived' 的条形图。

---

### **模块五：回归模型绘图 (15题)**

**核心知识点**:

- `regplot()`: 绘制散点图并拟合一条线性回归模型。
    
- `lmplot()`: Figure-level 接口，功能更强大，可以方便地按子集绘制多个回归图。
    

**准备数据集**:

Python

```
import seaborn as sns
import matplotlib.pyplot as plt

tips = sns.load_dataset("tips")
```

练习题:

121. 使用 tips 数据集，绘制 total_bill 和 tip 的 regplot。

122. 在 regplot 中，隐藏散点图，只显示回归线。

123. 改变回归线的颜色。

124. 使用 lmplot() 绘制同样的图。

125. 使用 lmplot，根据 'smoker' 使用 hue 参数拟合和绘制两条独立的回归线。

126. 使用 lmplot，根据 'time' 使用 col 参数将回归图分面显示。

127. 在 regplot 中，使用 order=2 来拟合一个二次多项式回归。

128. 对于一个分类变量（如 tips 中的 'size'），使用 x_jitter 参数为 regplot 的 x 轴添加抖动。

129. 使用 lmplot，根据 'sex' 和 'smoker' 同时使用 row 和 col 创建一个回归图网格。

130. 在 regplot 中，自定义散点和回归线的样式属性。

131. 使用 residplot() 绘制回归模型的残差图，检查模型假设。

132. 在 lmplot 中，将 hue 变量 ('smoker') 的图例移动到图外。

133. 在 regplot 中，使用 logistic=True 为一个二元结果变量拟合一个逻辑回归模型。

134. 在 lmplot 中，使用 robust=True 拟合一个对异常值不敏感的稳健回归。

135. 如何获取 lmplot 或 regplot 拟合的回归模型的统计摘要？(提示: Seaborn 本身不直接提供，需结合 statsmodels)

---

### **模块六：矩阵与网格绘图 (15题)**

**核心知识点**:

- `heatmap()`: 热力图，将一个矩阵的数据用颜色编码的形式可视化。
    
- `clustermap()`: 聚类图，在热力图的基础上，对行和列进行层次聚类。
    

**准备数据集**:

Python

```
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# 加载 flights 数据集并转换为适合热力图的矩阵格式
flights = sns.load_dataset("flights")
flights_pivot = flights.pivot("month", "year", "passengers")

# 创建一个随机相关系数矩阵
np.random.seed(0)
corr_data = pd.DataFrame(np.random.rand(10, 10), columns=[f'Var{i}' for i in range(10)])
corr_matrix = corr_data.corr()
```

练习题:

136. 使用 flights_pivot 数据绘制一张热力图。

137. 在热力图的每个单元格中显示数值 (annot=True)。

138. 改变热力图的色彩映射 (cmap)，例如使用 'coolwarm'。

139. 格式化热力图中的注释文字，例如保留整数 (fmt="d")。

140. 隐藏热力图的颜色条 (cbar=False)。

141. 使用 linewidths 参数在热力图的单元格之间添加分割线。

142. 使用 corr_matrix 数据绘制一个相关系数矩阵的热力图。

143. 创建一个掩码 (mask) 来隐藏相关系数矩阵的上三角部分。

144. 将掩码应用到热力图中，只显示下三角部分。

145. 使用 clustermap() 绘制 flights_pivot 的聚类图。

146. clustermap 默认使用的聚类方法和距离度量是什么？

147. 改变 clustermap 的聚类方法，例如改为 'ward'。

148. 将 clustermap 中的数据进行标准化（按行或按列）。

149. 隐藏 clustermap 中的其中一个树状图。

150. 自定义 clustermap 中热力图部分的色彩映射。

---

这份练习题单覆盖了 Seaborn 的绝大多数常用功能，完成它们将帮助您建立起坚实的数据可视化能力。祝您练习愉快！