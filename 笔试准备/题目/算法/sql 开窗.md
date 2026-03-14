### 一、先明确 `DENSE_RANK()` 的核心特性

`DENSE_RANK()` 是窗口函数（开窗函数）中**密集排名函数**，核心特点：

- 相同值会得到**相同排名**；
- 排名是**连续的**（不会跳过数字），比如有 2 个第 1 名，下一个仍是第 2 名（区别于 `RANK()`，`RANK()` 会跳过，变成第 3 名）。

它的基础语法是：

sql

```
DENSE_RANK() OVER (
    [PARTITION BY 分组字段]  -- 可选：按指定字段分组，每组内单独排名
    ORDER BY 排序字段 [ASC/DESC]  -- 必选：按指定字段排序（升序/降序）
) AS 排名别名
```

下面结合 `employees` 表（补充常见字段如 `dept_no` 方便演示），讲解 `DENSE_RANK()` 的核心实用场景：

---

### 二、`DENSE_RANK()` 的关键用法场景

#### 场景 1：按分组维度排名（最常用，PARTITION BY + ORDER BY）

**需求**：按性别（gender）分组，对每组内的员工按入职日期（hire_date）降序排名（即每个性别下，入职最晚的排第 1）。

sql

```
SELECT 
    emp_no,
    gender,
    hire_date,
    -- 按gender分组，组内按hire_date降序密集排名
    DENSE_RANK() OVER (PARTITION BY gender ORDER BY hire_date DESC) AS rank_in_gender
FROM employees;
```

**示例输出（基于你的样本数据）**：

表格

|emp_no|gender|hire_date|rank_in_gender||
|---|---|---|---|---|
|10002|F|1985-11-21|1|（F 性别只有 1 人，排名 1）|
|10004|M|1986-12-01|1|（M 性别入职最晚，排名 1）|
|10003|M|1986-08-28|2|（M 性别第二晚，排名 2）|
|10001|M|1986-06-26|3|（M 性别第三晚，排名 3）|

#### 场景 2：多字段排序的密集排名

**需求**：先按入职日期（hire_date）降序，再按员工编号（emp_no）升序，对所有员工做密集排名（解决 “同入职日期” 的排名问题）。

sql

```
SELECT 
    emp_no,
    hire_date,
    -- 多字段排序：先按hire_date降序，再按emp_no升序
    DENSE_RANK() OVER (ORDER BY hire_date DESC, emp_no ASC) AS rank_multi
FROM employees;
```

**核心作用**：当排序字段（如 hire_date）有重复值时，通过第二个字段（如 emp_no）确定排名顺序，避免排名混乱。

#### 场景 3：取每组的前 N 名（结合筛选）

**需求**：按性别分组，取每个性别下入职最晚的前 2 名员工（利用子查询 + 排名筛选）。

sql

```
-- 第一步：先给每个性别内的员工排名
WITH ranked_emp AS (
    SELECT 
        emp_no,
        gender,
        hire_date,
        DENSE_RANK() OVER (PARTITION BY gender ORDER BY hire_date DESC) AS rank_in_gender
    FROM employees
)
-- 第二步：筛选每组排名≤2的员工
SELECT * 
FROM ranked_emp 
WHERE rank_in_gender <= 2;
```

**示例输出**：

表格

|emp_no|gender|hire_date|rank_in_gender||
|---|---|---|---|---|
|10002|F|1985-11-21|1|（F 性别只有 1 人，保留）|
|10004|M|1986-12-01|1|（M 性别前 2 名）|
|10003|M|1986-08-28|2||

#### 场景 4：升序排名（找 “最早”/“最小” 类数据）

**需求**：对所有员工按入职日期升序密集排名（找入职最早的员工）。

sql

```
SELECT 
    emp_no,
    hire_date,
    -- 升序排名：入职最早的排第1
    DENSE_RANK() OVER (ORDER BY hire_date ASC) AS rank_asc
FROM employees;
```

**示例输出（样本数据）**：

表格


|emp_no|hire_date|rank_asc||
|---|---|---|---|
|10002|1985-11-21|1|（最早入职）|
|10001|1986-06-26|2||
|10003|1986-08-28|3||
|10004|1986-12-01|4||

#### 场景 5：对比 `RANK()`/`ROW_NUMBER()` 理解差异（关键避坑）

为了更清楚 `DENSE_RANK()` 的特性，对比另外两个常用排名函数（以 “同入职日期” 为例）：

假设有新增数据：emp_no=10005，hire_date=1986-12-01，gender=M。

sql

```
SELECT 
    emp_no,
    hire_date,
    DENSE_RANK() OVER (ORDER BY hire_date DESC) AS dense_rank,
    RANK() OVER (ORDER BY hire_date DESC) AS rank,
    ROW_NUMBER() OVER (ORDER BY hire_date DESC) AS row_num
FROM employees;
```

**输出对比（核心差异）**：

表格

|emp_no|hire_date|dense_rank|rank|row_num|
|---|---|---|---|---|
|10004|1986-12-01|1|1|1|
|10005|1986-12-01|1|1|2|
|10003|1986-08-28|2|3|3|
|10001|1986-06-26|3|4|4|
|10002|1985-11-21|4|5|5|

- `DENSE_RANK()`：相同日期排名相同，后续排名连续（1→1→2）；
- `RANK()`：相同日期排名相同，后续排名跳过（1→1→3）；
- `ROW_NUMBER()`：即使日期相同，也会生成唯一连续排名（1→2→3）。

---

### 总结

1. `DENSE_RANK()` 核心是**密集连续排名**，相同值排名一致且不跳号，适合需要 “不跳过排名” 的场景（如取前 N 名、分组排名）；
2. 最常用搭配是 `PARTITION BY 分组字段 + ORDER BY 排序字段`，实现 “分组内排名”；
3. 可通过多字段排序（`ORDER BY 字段1, 字段2`）解决排序字段重复的问题，也可灵活切换 `ASC/DESC` 实现 “找最早 / 最晚” 类需求；
4. 与 `RANK()`/`ROW_NUMBER()` 相比，`DENSE_RANK()` 更适合需要 “同值同排名、排名连续” 的业务场景（如排行榜、分组取前 N）。