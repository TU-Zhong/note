好的，在 SQL Server 中，新建一个会话来监控 CPU 运行时间，最现代、最高效的方法是使用**扩展事件 (Extended Events, XEvents)**。它取代了旧的、开销较大的 SQL Profiler/Trace。

下面我将为您提供两种创建方法：一种是通过 SSMS 图形化界面（适合初学者），另一种是通过 T-SQL 脚本（适合自动化和专业人士）。

### 我们要创建的会话目标

我们将创建一个名为 `Monitor_High_CPU_Queries` 的扩展事件会话，它会捕获**持续时间超过 1 秒**并且 **CPU 时间超过 500 毫秒**的 SQL 语句。这可以帮助我们精确地找到消耗 CPU 资源最多的查询。

---

### 方法一：使用 SSMS 图形化界面 (GUI)

这是最直观的方法，跟着向导一步步操作即可。

1. **打开 SSMS**，连接到您的 SQL Server 实例。
    
2. 在**对象资源管理器**中，展开 **管理 (Management)** -> **扩展事件 (Extended Events)**。
    
3. 右键点击 **会话 (Sessions)**，选择 **新建会话... (New Session...)**。
    
4. **常规 (General)** 页面：
    
    - **会话名称 (Session name)**: 输入 `Monitor_High_CPU_Queries`。
        
    - 勾选 “服务器启动时启动事件会话” (Start the event session at server startup)，如果你希望它一直运行。
        
5. **事件 (Events)** 页面：这是最核心的一步。
    
    - 在“事件库 (Event library)”的搜索框中，输入 `completed`。
        
    - 从列表中找到并选择以下两个事件，然后点击 `>` 按钮将它们添加到右侧的“所选事件”列表中：
        
        - `rpc_completed`: 捕获来自应用程序的远程过程调用（例如，通过 JDBC、ODBC 发送的整个 SQL 批处理）。
            
        - `sql_statement_completed`: 捕获单个 T-SQL 语句的完成。
            
6. **配置事件 (Adding Actions)**：
    
    - 选择这两个事件后，点击上方的 **配置 (Configure)** 按钮。
        
    - 在右侧的“全局字段(操作)” (Global Fields (Actions)) 选项卡中，勾选以下几个非常有用的字段，它们会作为附加信息被捕获：
        
        - `database_name`: 查询在哪个数据库中运行。
            
        - `sql_text`: 完整的 SQL 语句文本。
            
        - `username`: 执行查询的用户名。
            
        - `client_hostname`: 客户端的主机名。
            
        - `client_app_name`: 客户端应用程序名称（例如 "Microsoft JDBC Driver for SQL Server"）。
            
7. **设置筛选器 (Filter / Predicate)**：这是**至关重要**的一步，可以避免捕获过多无用的数据，减小性能开销。
    
    - 在同一个配置界面，点击 **筛选器(谓词) (Filter (Predicate))** 选项卡。
        
    - 点击“单击此处以添加子句” (Click here to add a clause)。
        
    - 添加第一个条件：
        
        - 字段 (Field): `cpu_time`
            
        - 运算符 (Operator): `>=`
            
        - 值 (Value): `500000` (这里的单位是**微秒**，500,000 微秒 = 500 毫秒)
            
    - 添加第二个条件：
        
        - 字段 (Field): `duration`
            
        - 运算符 (Operator): `>=`
            
        - 值 (Value): `1000000` (单位是**微秒**，1,000,000 微秒 = 1 秒)
            
    - **逻辑关系 (Grouping)**: 确保两个条件之间的逻辑是 `AND`。
        
8. **数据存储 (Data Storage)** 页面：
    
    - 选择 **添加目标 (Add a target)**。
        
    - 选择类型为 `event_file`。
        
    - 在下方的“文件名”处，指定一个路径和文件名，例如 `D:\XEvents\Monitor_High_CPU_Queries.xel`。
        
    - 可以设置文件大小和文件滚动数量，防止单个文件过大。
        
9. **高级 (Advanced)** 页面：
    
    - 可以保持默认设置。这里的事件保留模式等选项可以控制内存缓冲区的行为。
        
10. **确定 (OK)**：点击“确定”创建会话。
    
11. **启动会话**：在“对象资源管理器”中找到刚刚创建的 `Monitor_High_CPU_Queries` 会话，右键点击它，选择 **启动会话 (Start Session)**。
    

---

### 方法二：使用 T-SQL 脚本

对于 DBA 和高级用户，使用脚本更快捷、更易于版本控制和部署。

SQL

```
-- 如果会话已存在，先停止并删除
IF EXISTS (SELECT 1 FROM sys.server_event_sessions WHERE name = 'Monitor_High_CPU_Queries')
BEGIN
    ALTER EVENT SESSION Monitor_High_CPU_Queries ON SERVER STATE = STOP;
    DROP EVENT SESSION Monitor_High_CPU_Queries ON SERVER;
END
GO

-- 创建扩展事件会话
CREATE EVENT SESSION [Monitor_High_CPU_Queries] ON SERVER
-- 1. 添加事件
ADD EVENT sqlserver.rpc_completed(
    -- 2. 添加附加信息（全局字段/操作）
    ACTION(sqlserver.database_name, sqlserver.sql_text, sqlserver.username, 
           sqlserver.client_hostname, sqlserver.client_app_name)
    -- 3. 设置筛选器（谓词）
    WHERE ([cpu_time] >= 500000 AND [duration] >= 1000000) -- CPU >= 500ms AND Duration >= 1s
),
ADD EVENT sqlserver.sql_statement_completed(
    ACTION(sqlserver.database_name, sqlserver.sql_text, sqlserver.username,
           sqlserver.client_hostname, sqlserver.client_app_name)
    WHERE ([cpu_time] >= 500000 AND [duration] >= 1000000)
)
-- 4. 添加目标（数据存储）
ADD TARGET package0.event_file(
    SET filename=N'D:\XEvents\Monitor_High_CPU_Queries.xel', -- 文件路径
    max_file_size=(50),      -- 单个文件最大 50 MB
    max_rollover_files=(5)   -- 最多 5 个滚动文件
)
WITH (
    MAX_MEMORY=4096 KB,
    EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,
    MAX_DISPATCH_LATENCY=30 SECONDS,
    MAX_EVENT_SIZE=0 KB,
    MEMORY_PARTITION_MODE=NONE,
    TRACK_CAUSALITY=OFF,
    STARTUP_STATE=OFF -- 服务器启动时不自动开启，可设为 ON
);
GO

-- 启动会话
ALTER EVENT SESSION Monitor_High_CPU_Queries ON SERVER STATE = START;
GO
```

### 如何查看捕获的数据

1. 在 SSMS 的**对象资源管理器**中，展开 **管理** -> **扩展事件** -> **会话** -> `Monitor_High_CPU_Queries`。
    
2. 右键点击 `package0.event_file`，选择 **查看目标数据... (View Target Data...)**。
    
3. 这将打开一个实时的数据查看器。当有符合条件的查询（CPU > 500ms 且 持续时间 > 1s）发生时，记录就会出现在这里。
    
4. 您可以对结果进行排序（例如按 `cpu_time` 降序），分组和筛选，快速找到问题查询。
    

这个会话将成为您排查 SQL Server CPU 性能问题的得力助手。