这份 `docker-compose.yml` 文件是 Docker 编排的核心，它把原本又长又难记的 `docker run` 命令变成了**结构化、可维护的文档**。作为软件工程专业的学生，掌握这套“基础设施即代码”的逻辑，对你 2026 年的春招非常有竞争力。

---
[[npm run dev -- --host]]

### 🔍 逐行拆解：这段配置到底在干什么？

我们将这块配置分为 **环境、映射、指令** 三部分来理解：

#### 1. 运行环境 (Environment)

- **`image: node:22-alpine`**：指定基础镜像。`alpine` 代表极简版的 Linux 系统，体积小、启动快，非常适合开发环境。
    
- **`container_name: metadata-frontend`**：给容器起个名字，避免你在 `docker ps` 时看到一串随机生成的乱码。
    
- **`working_dir: /app`**：设定容器内部的“主战场”。后续所有的命令（如 `npm run dev`）都会在这个目录下执行。
    

#### 2. 映射桥梁 (Mapping)

- **`volumes: - ./frontend:/app`**：**最核心的配置**。它将你宿主机（本地电脑）的 `frontend` 文件夹挂载到容器的 `/app` 目录。这意味着你在 IDEA 里改代码，容器里会立刻同步，从而实现热更新。
    
- **`ports: - "5173:5173"`**：端口映射。前面的 `5173` 是你浏览器访问的端口，后面的 `5173` 是容器内部 Vite 监听的端口。
    

#### 3. 交互与指令 (Interaction & Commands)

- **`command: npm run dev -- --host`**：容器启动后自动执行的命令。`--host` 告诉 Vite 不要只监听 `localhost`，要监听所有来源，这样宿主机才能通过映射访问到它。
    
- **`tty: true` 和 `stdin_open: true`**：这两个组合起来等同于 `docker run -it`。它们保持了容器的交互性，防止容器启动后因为没有前台进程而立刻退出。
    

---

### 📚 学习路径：如何学会编写 Docker Compose？

想要从零开始写出这样的配置，建议按照“**理解 -> 转换 -> 编排**”的顺序学习：

#### 第一步：建立核心概念 (Conceptualize)

不要去背配置项，先搞懂这四个核心概念：

- **Services (服务)**：你的应用由哪些部分组成（前端、后端、数据库）。
    
- **Networks (网络)**：它们之间怎么互相通电话。
    
- **Volumes (卷)**：数据怎么存、代码怎么同步。
    
- **Environment (环境)**：账号密码、API 地址怎么传。
    

#### 第二步：练习“翻译”命令 (Translate)

把你之前敲过的 `docker run` 命令手动翻译成 YAML 格式。

- `-v` 对应 `volumes`。
    
- `-p` 对应 `ports`。
    
- `--name` 对应 `container_name`。
    

#### 第三步：掌握 YAML 语法 (Syntax)

Docker Compose 使用 YAML 格式，它是**靠空格缩进**来表示层级关系的（和 Python 类似）。

- **注意**：YAML 严格禁止使用 Tab 键，必须使用空格，否则 Docker 会报错。
    

#### 第四步：官方文档 + 优秀开源项目 (Reference)

- **官方文档**：Docker 文档的 [Get Started](https://docs.docker.com/get-started/08_using_compose/) 是全世界最好的入门教程。
    
- **实战模仿**：去 GitHub 找一些简单的 `Vue + Spring Boot` 项目，看看人家的 `docker-compose.yml` 是怎么写的。
    

---

### 💡 进阶建议

既然你已经在准备 **2026 年春招**，面试官非常喜欢问：“你如何保证开发环境和生产环境的一致性？”

这时候你可以回答：

> “我利用 **Docker Compose** 编排了整个开发环境，通过 Volume 挂载实现本地代码与容器环境的实时同步，并通过统一的镜像版本规避了‘在我的电脑上能跑，在服务器上不能跑’的典型环境冲突问题。”

**既然现在环境已经一键化了，我们要不要立刻把那个能联动 Pinia 的多标签页 (Tabs) 逻辑手写出来？**