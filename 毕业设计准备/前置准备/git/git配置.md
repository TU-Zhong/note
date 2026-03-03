太好了！在你新安装 Git 之后，你需要完成几个“一次性”的设置，然后就可以开始你的标准工作流程了。

这里是你需要完成的**完整流程**：

---

### 阶段一：一次性全局配置 (必做)

打开你的新终端 (PowerShell 或 Git Bash)，执行以下命令。这只需要在你的电脑上做**一次**，它会告诉 Git “你是谁”。

1. 设置你的用户名：

这个名字会出现在你所有的“提交记录”里。

Bash

```
git config --global user.name "你的名字或昵称"
```

2. 设置你的邮箱：

这个邮箱是 Git 用来识别你身份的（尤其是在 GitHub/GitLab 等平台上）。

Bash

```
git config --global user.email "你的邮箱@example.com"
```

3. (可选) 检查你的配置：

你可以用这个命令看看你刚才设置的对不对。

Bash

```
git config --list
```

---

### 阶段二：为你的 Vue 项目初始化 Git 仓库

现在，我们要进入你的 Vue 项目文件夹 (`test` 目录)，把它变成一个 Git "仓库"。

**1. 进入你的项目目录：**

PowerShell

```
cd D:\yingyong2\CodingLearning2\vue\Project\test
```

2. 初始化仓库：

这个命令会在你的文件夹里创建一个隐藏的 .git 目录，所有版本历史都存在这里。

Bash

```
git init
```

**3. 创建 `.gitignore` 文件 (极其重要！)**

- **为什么？** 你的项目里有 `node_modules` 文件夹，它里面有成千上万个小文件，而且体积巨大。**你绝对不能把它们提交到 Git。** `.gitignore` 文件就是告诉 Git：“请忽略这些文件”。
    
- **如何做？**
    
    - 在你的 `test` 文件夹根目录（和 `package.json` 同一级）**新建一个文件**。
        
    - 完整命名为 `.gitignore` (注意前面有个点，没有其他名字)。
        
    - 用 VS Code 打开它，把下面的内容复制进去：
        
    
    ```
    # .gitignore 文件内容
    
    # 依赖包 (最重要!)
    /node_modules
    
    # 构建产物
    /dist
    
    # 缓存
    .vite-inspect/
    
    # 本地环境变量
    .env.local
    .env.*.local
    
    # 日志文件
    npm-debug.log*
    yarn-debug.log*
    pnpm-debug.log*
    
    # 操作系统和 IDE 文件
    .DS_Store
    .idea/
    .vscode/
    ```
    

---

### 阶段三：你的日常工作流程

现在你已经准备好了。以后你每次写完代码，都将重复这个流程：

1. 查看状态：

这是你最常用的命令，用来查看“你改了哪些文件？”

Bash

```
git status
```

2. 暂存所有更改：

告诉 Git：“我准备把所有我改动过的文件（除了被 .gitignore 忽略的）打包，准备提交。”

Bash

```
git add .
```

(注意 `add` 和 `.` 之间有空格)

3. 提交你的“快照”：

给这个“快照”一个描述信息，告诉未来的你“这次都干了啥”。

Bash

```
git commit -m "这里写你的提交信息, 比如: 第一次提交"
```

**4. (可选) 查看提交历史：**

Bash

```
git log
```

---

### 阶段四：连接到远程仓库 (如 GitHub/GitLab)

你的代码现在只存在你的电脑上。为了备份和协作，你需要把它推送到一个远程服务器（比如 GitHub）。

1. 在 GitHub (或 Gitee/GitLab) 上创建一个新的空仓库。

(比如你创建了一个叫 my-vue-project 的仓库，不要勾选“Initialize with a README”)

2. 复制远程仓库的 URL。

(比如 https://github.com/你的用户名/my-vue-project.git)

**3. 把你的本地仓库和这个远程 URL 连接起来：**

Bash

```
git remote add origin https://github.com/你的用户名/my-vue-project.git
```

4. 更改默认分支名 (推荐)：

Git 默认分支可能叫 master，现在推荐用 main。

Bash

```
git branch -M main
```

5. 推送你的第一个提交：

把你的本地 main 分支推送到远程的 origin。

Bash

```
git push -u origin main
```

(第一次推送需要 `-u`，以后你只需要 `git push` 就行了)。

---

### 总结：你的最小流程

- **一次性设置 (全局)：**
    
    1. `git config --global user.name "..."`
        
    2. `git config --global user.email "..."`
        
- **一次性设置 (每个项目)：**
    
    1. `cd D:\...Project\test`
        
    2. `git init`
        
    3. 创建 `.gitignore` 文件
        
    4. `git remote add origin [你的远程URL]`
        
- **日常循环：**
    
    1. 写代码...
        
    2. `git add .`
        
    3. `git commit -m "我今天干了..."`
        
    4. `git push`