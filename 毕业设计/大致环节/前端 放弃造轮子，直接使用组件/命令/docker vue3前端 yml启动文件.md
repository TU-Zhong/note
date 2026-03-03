```yml
version: '3.8' # 指定 compose 文件版本，建议使用  
  
services:  
  # 'app' 是我们给这个服务起的名字，你可以改成  
  # 比如 'vue-dev'，但 'app' 是最常见的  
  app:  
    image: node:latest     # 对应: node:latest  
    working_dir: /app      # 对应: -w /app  
    volumes:  
      - .:/app             # 对应: -v "${pwd}:/app" ('.' 代表当前目录)  
      # 这是一个优化：让 node_modules 只存在于容器中，  
      # 避免本地和容器共享它，可以提升性能和避免权限问题  
      - /app/node_modules  
  
    ports:  
      - "5173:5173"        # 对应: -p 5173:5173  
    environment:  
      - CHOKIDAR_USEPOLLING=1  
    # 这是默认命令，当我们运行 'docker-compose up' 时执行  
    command: sh -c "npm install && npm run dev -- --host"  
      
    stdin_open: true       # 对应: -i (保持标准输入打开)  
    tty: true              # 对应: -t (分配一个终端)
```