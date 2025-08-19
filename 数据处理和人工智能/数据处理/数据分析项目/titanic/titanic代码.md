好的，没问题。

下面是一份完整的、带有详细中文注释的Python代码，涵盖了从数据加载、探索性分析、数据清洗、特征工程到最终模型训练和预测的**完整流程**。

你可以将此代码复制到你的环境中（如 Jupyter Notebook），并将 `train.csv` 和 `test.csv` 文件放在同一目录下，然后直接运行。

---

### **Titanic 生还预测完整分析代码**

Python

```python
# =============================================================================
# 步骤 1: 导入所有需要的库
# =============================================================================
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

# 设置可视化风格，让图表更好看
sns.set(style="whitegrid")
print("库导入成功！")


# =============================================================================
# 步骤 2: 加载数据
# =============================================================================
try:
    # 读取训练数据和测试数据
    train_df = pd.read_csv("train.csv")
    test_df = pd.read_csv("test.csv")
    print("\n数据加载成功！")
    print(f"训练集大小: {train_df.shape}")
    print(f"测试集大小: {test_df.shape}")
except FileNotFoundError:
    print("\n错误：请确保 train.csv 和 test.csv 文件在当前代码运行的目录下。")
    # 如果文件不存在，退出程序
    exit()

# 为了方便数据预处理，我们经常将训练集和测试集合并
# 这样做可以确保对两个数据集执行相同的转换操作
# 'Survived' 列在测试集中不存在，合并时会变为 NaN，这没关系，后续会处理
all_df = pd.concat([train_df, test_df], sort=False).reset_index(drop=True)
print(f"合并后数据集大小: {all_df.shape}")


# =============================================================================
# 步骤 3: 探索性数据分析 (EDA) - 通过可视化理解数据
# =============================================================================
print("\n开始探索性数据分析 (EDA)...")

# --- 3.1 分析性别(Sex) vs 生还率(Survived) ---
plt.figure(figsize=(10, 4))
sns.barplot(x='Sex', y='Survived', data=train_df)
plt.title('Survival Rate by Sex')
plt.ylabel('Survival Rate')
plt.savefig('eda_sex_survival.png') # 保存图像
print("生成图像: eda_sex_survival.png (性别与生还率关系)")
# 结论: 女性生还率远高于男性，这是一个非常重要的特征。

# --- 3.2 分析船舱等级(Pclass) vs 生还率(Survived) ---
plt.figure(figsize=(10, 4))
sns.barplot(x='Pclass', y='Survived', data=train_df)
plt.title('Survival Rate by Pclass')
plt.ylabel('Survival Rate')
plt.savefig('eda_pclass_survival.png') # 保存图像
print("生成图像: eda_pclass_survival.png (船舱等级与生还率关系)")
# 结论: 船舱等级越高（1等舱），生还率越高。这也是一个关键特征。

# --- 3.3 分析年龄(Age) vs 生还率(Survived) ---
# 使用FacetGrid可以方便地画出分条件的图
g = sns.FacetGrid(train_df, col='Survived', height=5)
g.map(sns.histplot, 'Age', bins=25, kde=False)
plt.suptitle('Age Distribution by Survival Status', y=1.02)
plt.savefig('eda_age_survival.png') # 保存图像
print("生成图像: eda_age_survival.png (年龄与生还率关系)")
# 结论: 幼儿和儿童的生还率较高，而青壮年（20-40岁）的遇难人数最多。


# =============================================================================
# 步骤 4: 数据清洗与特征工程
# =============================================================================
print("\n开始数据清洗与特征工程...")

# --- 4.1 处理缺失值 ---
# 首先查看所有列的缺失值情况
print("\n合并后数据集各列的缺失值数量:")
print(all_df.isnull().sum())

# (1) Embarked (登船港口): 只有2个缺失值，用出现次数最多的值（众数）填充
# .mode()[0] 返回出现次数最多的那个值
embarked_mode = all_df['Embarked'].mode()[0]
all_df['Embarked'].fillna(embarked_mode, inplace=True)
print(f"\n'Embarked' 缺失值已用众数 '{embarked_mode}' 填充。")

# (2) Fare (票价): 测试集中有1个缺失值，用所有票价的中位数填充
fare_median = all_df['Fare'].median()
all_df['Fare'].fillna(fare_median, inplace=True)
print(f"'Fare' 缺失值已用中位数 '{fare_median}' 填充。")

# (3) Age (年龄): 缺失较多，处理需要更精细。
# 一个好方法是根据乘客的头衔（Mr., Mrs., Miss 等）来预测年龄。
# 我们从'Name'列中提取头衔，创建一个新特征'Title'
all_df['Title'] = all_df['Name'].str.extract(' ([A-Za-z]+)\.', expand=False)
# 将不常见的头衔统一替换为更通用的类别
all_df['Title'] = all_df['Title'].replace(['Lady', 'Countess','Capt', 'Col','Don', 'Dr', 'Major', 'Rev', 'Sir', 'Jonkheer', 'Dona'], 'Rare')
all_df['Title'] = all_df['Title'].replace('Mlle', 'Miss')
all_df['Title'] = all_df['Title'].replace('Ms', 'Miss')
all_df['Title'] = all_df['Title'].replace('Mme', 'Mrs')
# 计算每种头衔对应的年龄中位数
title_age_median = all_df.groupby('Title')['Age'].median()
# 使用头衔对应的年龄中位数来填充年龄的缺失值
all_df['Age'] = all_df.apply(
    lambda row: title_age_median[row['Title']] if pd.isnull(row['Age']) else row['Age'],
    axis=1
)
print("'Age' 缺失值已根据头衔的中位数年龄填充。")

# (4) Cabin (船舱号): 缺失太多，难以直接利用。
# 但我们可以创建一个新特征 'HasCabin'，表示该乘客是否有船舱记录。
# 有船舱记录可能意味着更高的社会地位。
all_df['HasCabin'] = all_df['Cabin'].notna().astype(int)
print("新特征 'HasCabin' 已创建。")

# --- 4.2 创建新特征 ---
# (1) FamilySize (家庭总人数) = 兄弟姐妹配偶数(SibSp) + 父母子女数(Parch) + 乘客自己(1)
all_df['FamilySize'] = all_df['SibSp'] + all_df['Parch'] + 1
print("新特征 'FamilySize' 已创建。")

# (2) IsAlone (是否独自一人)
all_df['IsAlone'] = (all_df['FamilySize'] == 1).astype(int)
print("新特征 'IsAlone' 已创建。")


# --- 4.3 转换分类变量为数值变量 ---
# 机器学习模型只能处理数值。我们需要将文本类别的特征转换为数值。
# (1) Sex (性别): male -> 0, female -> 1
all_df['Sex'] = all_df['Sex'].map({'male': 0, 'female': 1}).astype(int)

# (2) Embarked (登船港口) 和 Title (头衔): 使用独热编码 (One-Hot Encoding)
# get_dummies 会自动将分类列转换为多个0/1的列
all_df = pd.get_dummies(all_df, columns=['Embarked', 'Title'], prefix=['Embarked', 'Title'])

# --- 4.4 删除不再需要的列 ---
# Name, Ticket, Cabin, SibSp, Parch 这些原始信息已经被我们转化为更有用的特征，可以删除了
all_df.drop(['Name', 'Ticket', 'Cabin', 'SibSp', 'Parch'], axis=1, inplace=True)
print("\n无用特征已删除，数据清洗和特征工程完成！")
print("\n处理后的数据集前5行:")
print(all_df.head())


# =============================================================================
# 步骤 5: 模型训练与预测
# =============================================================================
print("\n开始模型训练与预测...")

# --- 5.1 将合并的数据重新分离为训练集和测试集 ---
# 训练集是原始 train_df 的长度
train_len = len(train_df)
train_final = all_df[:train_len]
test_final = all_df[train_len:]

# 'Survived' 列在测试集中是空的，需要从测试集中删除
test_final.drop('Survived', axis=1, inplace=True)

# --- 5.2 准备训练数据 ---
# X 是我们的特征 (所有列除了 'Survived' 和 'PassengerId')
# y 是我们的目标 ('Survived' 列)
X_train = train_final.drop(['Survived', 'PassengerId'], axis=1)
y_train = train_final['Survived'].astype(int) # 确保目标变量是整数类型
X_test = test_final.drop('PassengerId', axis=1) # 测试集的特征

print(f"\n训练特征集大小: {X_train.shape}")
print(f"训练目标集大小: {y_train.shape}")
print(f"测试特征集大小: {X_test.shape}")

# --- 5.3 训练分类模型 ---
# 我们选用随机森林分类器，这是一个强大且常用的模型
random_forest = RandomForestClassifier(n_estimators=100, max_depth=5, random_state=42)
random_forest.fit(X_train, y_train)
print("\n随机森林模型训练完成！")

# --- 5.4 进行预测 ---
predictions = random_forest.predict(X_test)
print("已在测试集上完成预测。")


# =============================================================================
# 步骤 6: 创建提交文件
# =============================================================================
print("\n正在生成提交文件...")

# 创建一个 DataFrame 用于提交
# 格式要求包含 'PassengerId' 和 'Survived' 两列
submission = pd.DataFrame({
    "PassengerId": test_df["PassengerId"],
    "Survived": predictions
})

# 将 DataFrame 保存为 csv 文件，index=False 表示不保存 DataFrame 的索引
submission.to_csv('submission.csv', index=False)

print("\n提交文件 'submission.csv' 已成功生成！")
print("分析流程结束。")
```