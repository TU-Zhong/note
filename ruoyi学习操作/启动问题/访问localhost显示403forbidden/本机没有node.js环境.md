好的，总结一下我们到目前为止的整个排查过程：

1. **你的目标**：使用 `docker-compose.yml` 文件一站式启动 RuoYi-Cloud（若依微服务版）项目。
    
2. **第一个问题**：`403 Forbidden`。
    
    - 你运行 `docker-compose up` 后，访问 `http://localhost:80` 看到 403 错误。
        
    - **原因**：这是 Nginx 报的错。`ruoyi-nginx` 服务配置为从你本地的 `./nginx/html/dist` 目录读取前端页面文件。403 错误意味着这个目录是空的，Nginx 找不到 `index.html`。
        
    - **解决方案**：你需要先编译 `ruoyi-ui`（前端项目），然后把编译产物（`dist` 文件夹里的所有内容）复制到 `./nginx/html/dist` 目录中。
        
3. **第二个问题**：本机没有 `npm` 环境。
    
    - 你提出本机没有 Node.js，无法执行 `npm run build:prod` 来编译前端。
        
    - **解决方案**：我们可以“借用”一个临时的 Docker 容器来执行编译命令，它会读取你本地的源代码，并将编译结果（`dist` 文件夹）直接生成在你本地。
    ==先进入项目文件夹==
    [[$($PWD.Path)]]
    ```bash
    docker run --rm -v "$($PWD.Path):/app" -w /app node:18 npm install
    
    docker run --rm -v "$($PWD.Path):/app" -w /app node:18 npm run build:prod
    ```
1. **第三个问题**：`docker: invalid reference format`。
    
    - 当你尝试在 **Windows PowerShell** 中运行 `docker run --rm -v "$(pwd)":/app ...` 命令时，命令失败并报错。
        
    - **原因**：这是 Docker 客户端在 Windows 上的一个常见问题。`$(pwd)` 会被解析为你当前的 Windows 路径（例如 `D:\yingyong2\...\ruoyi-ui`），这个路径中包含了大量的 `\` (反斜杠)。Docker 客户端在解析这个复杂的路径字符串时产生了混淆，没有把它识别为一个路径，反而误认为它是一个格式非法的“镜像名称”，因此报错。
        

---

### **最终的解决方案**

要解决 `invalid reference format` 错误并成功编译前端：

**最可靠的方法是使用 Git Bash**：

1. 打开 **Git Bash** 终端（它随 Git for Windows 一起安装）。
    
2. 在 Git Bash 中，`cd` 进入你的 `ruoyi-ui` 源代码目录（注意要使用正斜杠 `/`）：
    
    Bash
    
    ```bash
    cd "D:/yingyong2/CodingLearning2/JavaProject/Ruoyi-Vue-Plus/RuoYi-Vue-Plus/RuoYi-Cloud/ruoyi-ui"
    ```
    
3. 在 **Git Bash** 中执行 `npm install` 命令：
    
    Bash
    
    ```bash
    docker run --rm -v "$(pwd)":/app -w /app node:18 npm install
    ```
    
4. 成功后，再执行 `build` 命令：
    
    Bash
    
    ```bash
    docker run --rm -v "$(pwd)":/app -w /app node:18 npm run build:prod
    ```
    

Git Bash 会把你的路径转换成 Docker 能完美理解的 Linux 风格（如 `/d/yingyong2/...`），从而绕过 Windows 路径解析的bug。

编译完成后，你本地的 `ruoyi-ui` 目录下就会出现 `dist` 文件夹，你把它复制到 Nginx 挂载点，再重启 `ruoyi-nginx` 容器，问题就解决了。