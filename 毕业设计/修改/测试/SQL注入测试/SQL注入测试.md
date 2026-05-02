Herr 何翊。看来你已经准备好面对真正的安全实战了。

放弃那些手动拼接几个单引号的“过家家”式测试。如果你的目标是进入顶尖技术团队，你必须展示出你懂得如何用黑客的视角来审视自己的代码。

完成这一部分的重构与测试，需要借助业界标杆的自动化 SQL 注入工具：**SQLMap**。我们将对你的 `/api/metadata/table/column/add`（增加列）或 `/api/table/create`（建表）接口发起猛烈的 Fuzzing（模糊）攻击。

为了在论文中呈现令人信服的安全测试结果，请严格按照以下三个步骤操作：

### 🎯 第一步：捕获标准请求报文 (准备“炮弹”)

SQLMap 需要知道如何向你的接口发送合法的请求，它才能在此基础上进行变异和攻击。

1. **正常发起请求：** 打开你的前端页面，或者使用 Apifox，向你的 `/api/metadata/table/column/add` 发送一次正常的、成功的“新增列”请求。
    
2. **抓取 Raw HTTP Request：** 在 Apifox 的控制台，或者浏览器的 Network 面板中，找到这次请求。
    
3. **保存为文件：** 将完整的 HTTP 请求报文（包括 URL、所有的 Headers、特别是那个重要的 `Authorization: Token`，以及 Body 中的 JSON 数据）复制下来。
    
4. 新建一个文本文档，命名为 `add_column_request.txt`，将内容粘贴进去。
    

**报文内容示例（必须包含真实的 Token）：**

HTTP

```
POST /api/metadata/table/column/add HTTP/1.1
Host: 127.0.0.1:8080
Content-Type: application/json
Authorization: 你的真实有效Token
...

{
  "tableName": "test_users",
  "schemaName": "dbo",
  "column": {
    "name": "age",
    "type": "INT",
    "length": "",
    "isNullable": true,
    "defaultValue": ""
  }
}
```

### ⚔️ 第二步：使用 SQLMap 发起 Fuzzing 攻击

如果你没有 Kali Linux，可以在 Windows/macOS 上安装 Python 并下载 SQLMap。

打开终端，进入保存了 `add_column_request.txt` 的目录，执行以下命令。注意：这是一个极具破坏性的指令，请确保你的后端程序正在运行，并且**绝对不要对生产环境执行**！

Bash

```
python sqlmap.py -r add_column_request.txt --batch --level=3 --risk=3
```

**参数解析：**

- `-r`: 告诉 SQLMap 读取这个请求文件作为模板。
    
- `--batch`: 自动回答所有提示（默认 Y），让攻击全自动进行。
    
- `--level=3 --risk=3`: 提高攻击的深度和广度。SQLMap 会自动解析 JSON Body，并在 `tableName`, `name`, `length`, `defaultValue` 等所有可能注入的地方疯狂注入单引号、分号、十六进制编码、时间盲注等各种恶意 Payload。
    

### 🛡️ 第三步：收集战果并在论文中展示（学术化输出）

此时，你的 Spring Boot 控制台应该在疯狂滚动报错。这正是我们想要的结果！

**在你的论文“4.3.2 安全性测试”章节中，你需要这样撰写和展示：**

#### 1. 阐述测试方法论

> “为验证本平台对高危动态 DDL 语句的防御能力，系统舍弃了常规的黑盒手工测试，转而引入业界标准的漏洞扫描工具 SQLMap 进行自动化的 Fuzzing 模糊测试。测试配置为 `--level=3 --risk=3`，针对 `AddColumn` 接口的表名、列名及长度属性发起涵盖布尔盲注、时间盲注及报错注入的全面攻击。”

#### 2. 展示防线被触发的铁证（堆栈日志）

从你的 Spring Boot 控制台中，挑选两条最典型的被你拦截的日志（一条是你的正则拦截，一条是你的中括号闭合拦截）。

> “测试结果表明，系统的多层防御机制有效抵御了极端恶意请求。例如，当 SQLMap 尝试在长度字段注入非数字的破坏性指令时，底层的白名单正则校验 `^[0-9,]+$` 瞬间熔断请求。系统抛出的底层异常堆栈如下所示，证明拦截器处于激活状态：”

Plaintext

```
[示例日志片段，你需要换成你真实跑出来的]
java.lang.IllegalArgumentException: 长度/精度格式非法，涉嫌 SQL 注入！
    at com.example.dbdesign.Service.TableDdlService.addColumn(TableDdlService.java:42)
    ...
```

#### 3. 展示全局异常拦截的优雅降级（HTTP 报文）

由于你在上一轮重构中已经加入了 `@RestControllerAdvice` 全局异常处理，现在即使底层抛出了 `IllegalArgumentException`，前端收到的也绝对不是 500 服务器崩溃页面，而是一个优雅的 200 业务报错 JSON。

> “得益于系统架构中的 `@RestControllerAdvice` 全局异常接管机制，底层的防御性异常并未向外暴露敏感的堆栈信息，而是被优雅地转换为标准化的业务级响应。面对 SQLMap 的高频攻击，前端收到的 HTTP 响应报文统一截获如下，确保了系统对外接口的安全封闭性：”

JSON

```
[示例 JSON，你需要换成你真实的]
{
  "code": 500,
  "msg": "操作失败: 长度/精度格式非法，涉嫌 SQL 注入！",
  "data": null
}
```

> “最终，SQLMap 生成的扫描报告显示该接口不存在可利用的 SQL 注入点（Injection not exploitable）。这以量化的方式佐证了本系统 DDL 防御策略的可靠性。”

---

去吧，Herr 何翊。释放 SQLMap 这头野兽去撕咬你的代码，然后把那些流着血（报错日志）的勋章，钉在你的毕业论文上。如果你能在控制台里捕获到这些报错，就说明你的盾牌真正经受住了实战的检验。