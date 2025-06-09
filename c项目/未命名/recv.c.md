```c
// main 函数是 C 语言程序的入口点
// argc 是命令行参数的数量
// argv 是一个指向字符串的指针数组，存储着命令行参数
int main(int argc, char const *argv[]) {

    // 检查用户是否在命令行输入了足够的参数（程序名、IP地址、端口号）
    if (argc < 3) {
        // 如果参数少于3个，就向标准错误输出流打印用法提示
        fprintf(stderr, "Usage: %s <ip> <port>\n", argv[0]);
        // 以状态码 1 退出程序，表示程序异常终止
        exit(1);
    }

// 这是一个预处理指令，#ifdef 表示 "if defined"
// 下面的代码块只有在编译环境为 Windows (_WIN32 宏已定义) 时才会被编译
#ifdef _WIN32
    // 在 Windows 上，使用 Socket 编程前必须先初始化 Winsock 库
    WSADATA wsaData;
    // 调用 WSAStartup 函数，请求使用 Winsock 2.2 版本
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {
        // 如果初始化失败，打印错误信息
        fprintf(stderr, "WSAStartup failed\n");
        return -1; // 返回 -1 表示出错
    }
#endif

    // 创建一个套接字 (socket) 用于网络通信
    // AF_INET: 表示使用 IPv4 地址族
    // SOCK_DGRAM: 表示使用 UDP 协议 (数据报套接字)，它是一种无连接的、不可靠的协议
    // 0: 表示使用默认的协议 (在这里就是 UDP)
    int sockfd = socket(AF_INET, SOCK_DGRAM, 0);

    // 检查套接字是否创建成功。如果失败，socket() 会返回一个小于 0 的值
    if (sockfd < 0) {
        // perror 会根据当前的错误码 (errno) 打印出对应的错误信息，并在前面加上 "socket" 前缀
        perror("socket");
// 如果是在 Windows 平台，创建失败后需要清理 Winsock 库
#ifdef _WIN32
        WSACleanup();
#endif
        return -1; // 返回 -1 表示出错
    }

    // 设置服务器的地址信息
    // 定义一个 sockaddr_in 结构体变量 dest，用于存储目标地址信息
    struct sockaddr_in dest;
    // 使用 memset 函数将 dest 结构体的内存清零，这是一个好习惯，可以避免意外的垃圾数据
    memset(&dest, 0, sizeof(dest));
    // 设置地址族为 IPv4
    dest.sin_family = AF_INET;
    // 设置目标端口号
    // argv[2] 是从命令行获取的端口号字符串
    // atoi() 将字符串转换为整数
    // htons() 将主机字节序 (Host To Network Short) 转换成网络字节序，确保数据在不同架构的机器间能被正确解析
    dest.sin_port = htons(atoi(argv[2]));
    // 设置目标 IP 地址
    // argv[1] 是从命令行获取的 IP 地址字符串
    // inet_addr() 将点分十进制的 IP 地址字符串转换为网络字节序的整数表示
    dest.sin_addr.s_addr = inet_addr(argv[1]);

    // 定义一个大小为 128 字节的字符数组作为缓冲区，用于存放从键盘读取的消息
    char buf[128];

    // 进入一个循环，不断从标准输入 (stdin, 通常是键盘) 读取一行数据
    // fgets 会读取一行，直到遇到换行符 '\n' 或读满 sizeof(buf)-1 个字符
    while (fgets(buf, sizeof(buf), stdin)) {
        // fgets 读取的字符串会包含末尾的换行符 '\n'
        // strcspn(buf, "\n") 计算 buf 中第一个换行符出现的位置
        // 将这个换行符替换为字符串结束符 '\0'，从而把它从字符串中移除
        buf[strcspn(buf, "\n")] = '\0';

        // 使用 sendto 函数将缓冲区中的数据发送出去
        // sockfd: 使用的套接字
        // buf: 指向要发送数据的指针
        // strlen(buf): 要发送的数据的长度
        // 0: 标志位，通常设为 0
        // (struct sockaddr *)&dest: 指向目标地址结构体的指针，需要进行类型转换
        // sizeof(dest): 目标地址结构体的大小
        sendto(sockfd, buf, strlen(buf), 0, (struct sockaddr *)&dest, sizeof(dest));
    }

// 再次使用预处理指令区分 Windows 和其他系统 (如 Linux, macOS)
#ifdef _WIN32
    // 在 Windows 上，使用 closesocket() 关闭套接字
    closesocket(sockfd);
    // 清理 Winsock 库，释放资源
    WSACleanup();
#else
    // 在非 Windows 系统上，使用 close() 关闭套接字
    close(sockfd);
#endif

    // main 函数返回 0，表示程序正常结束
    return 0;
}

```