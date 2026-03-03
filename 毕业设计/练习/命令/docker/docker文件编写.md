好的，如果你想在 **Spring Boot 项目的根目录**下（例如 `backend/` 文件夹内）放置一个 Docker 配置，来同时启动你的 Spring Boot 应用 **和** 它所依赖的 SQL Server 数据库，那么使用 `docker-compose.yml` 文件是最好的选择。

这个文件可以“编排”两个服务：你的 `backend` 应用和 `db` 数据库。

---

### 📝 你的 Spring Boot 项目文件夹

假设你的 Spring Boot 项目（我们之前称之为 `backend`）目录结构如下：

```
backend/
├── src/
├── pom.xml
├── Dockerfile              <-- (【必需】打包Spring Boot应用的Dockerfile)
└── docker-compose.yml      <-- (【新增】你想要的配置文件)
```

---

### 1. Spring Boot 的 `Dockerfile`

首先，你需要在 `backend` 根目录下有一个 `Dockerfile`，用来告诉 Docker 如何打包你的 Spring Boot 应用。

(这个和我们之前用过的一样，放在这里供你参考)

**文件：`backend/Dockerfile`**

Dockerfile
[[docker命令解释]]

```Dockerfile
# 阶段 1: 构建
FROM maven:3.8-openjdk-17 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# 阶段 2: 运行
FROM openjdk:17-jre-slim
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

### 2. Docker Compose 配置文件

然后，在**同一目录**（`backend/`）下，创建这个 `docker-compose.yml` 文件。

**文件：`backend/docker-compose.yml`**

YAML
[[模板文件]]

[[文件编写方法]]
```YAML
version: '3.8'

services:
  # 1. 数据库服务 (SQL Server)
  db:
    image: mcr.microsoft.com/mssql/server:2022-latest
    container_name: mssql_db_service # 给容器起个名字
    environment:
      ACCEPT_EULA: 'Y'
      MSSQL_SA_PASSWORD: 'YourStrong@Password123' # 【重要】设置你自己的强密码
      MSSQL_PID: 'Developer'
    ports:
      - "1433:1433" # 映射到宿主机，方便你用SSMS连接
    networks:
      - spring-net
    volumes:
      - sqlserver_data:/var/opt/mssql # 数据持久化

  # 2. 后端服务 (你的Spring Boot应用)
  backend:
    container_name: spring_app_service # 给容器起个名字
    build:
      context: .  # 告诉 Docker 在当前目录(.)寻找 Dockerfile 来构建
    ports:
      - "8080:8080" # 映射 Spring Boot 端口
    environment:
      # 【关键】这里是重点！
      # 我们覆盖 application.properties，连接到 'db' 服务
      # Spring Boot 应用会通过 'db' 这个主机名找到数据库
      SPRING_DATASOURCE_URL: 'jdbc:sqlserver://db:1433;databaseName=login_demo_db;encrypt=false;trustServerCertificate=true'
      SPRING_DATASOURCE_USERNAME: 'sa'
      SPRING_DATASOURCE_PASSWORD: 'YourStrong@Password123' # 【重要】必须与上面 db 服务的密码一致
      SPRING_JPA_HIBERNATE_DDL_AUTO: 'update' # 自动创建/更新数据库表
    depends_on:
      - db # 确保 db 服务先于 backend 启动
    networks:
      - spring-net

# 定义网络，让两个服务可以互相通信
networks:
  spring-net:
    driver: bridge

# 定义卷，用于数据库数据持久化
volumes:
  sqlserver_data:
```

---

### 🚀 如何使用

1. **修改 `application.properties` (重要提醒)**
    
    - 这个 `docker-compose.yml` 会通过 `environment` 变量**覆盖**你的 `application.properties` 里的数据库配置。
        
    - 你本地的 `application.properties` 可以保留 `localhost` 的配置，方便你本地IDE开发。
        
    - Docker 运行时，`SPRING_DATASOURCE_URL` 会被自动替换为 `jdbc:sqlserver://db:1433...`，这里的 `db` 就是你在上面定义的数据库服务名。
        
2. **启动服务**
    
    - 打开终端，`cd` 到你的 Spring Boot 项目根目录（即 `backend/` 目录，`docker-compose.yml` 所在的位置）。
        
    - 运行命令：
        
        Bash
        
        ```
        docker-compose up -d --build
        ```
        
    - `--build` 会强制重新构建你的 `backend` 镜像（如果你修改了Java代码）。
        
    - `-d` 会在后台运行。
        
3. **访问**
    
    - 你的 Spring Boot 应用将运行在 `http://localhost:8080`。
        
    - 你的 SQL Server 数据库将运行在 `localhost:1433` (可以被 SSMS 访问到)。
        

这样，你就有了一个独立的、可一键启动的后端开发环境了。