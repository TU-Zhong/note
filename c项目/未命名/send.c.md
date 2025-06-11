[[声明]]
char const *argv[]
[[宏定义]]
WIN32

[[WSADATA]]
wsadata

创建网络套接字
[[int sockfd = socket(AF_INET, SOCK_DGRAM, 0)]]

[[dest.sin_addr.s_addr]]

```c
//  
// Created by Administrator on 2025/6/6.  
//  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
#ifdef _WIN32  
    #include <winsock2.h>  
    #include <ws2tcpip.h>  
    #pragma comment(lib, "ws2_32.lib")  
#else  
    #include <unistd.h>  
    #include <arpa/inet.h>#endif  
  
//客户端  
int main(int argc, char const *argv[]) {  
    //输入服务器的ip和端口  
    if (argc < 3) {  
        fprintf(stderr, "Usage: %s <ip> <port>\n", argv[0]);  
        exit(1);  
    }  
  
#ifdef _WIN32  
    // 初始化Winsock  
    WSADATA wsaData;  
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {  
        fprintf(stderr, "WSAStartup failed\n");  
        return -1;  
    }  
#endif  
  
    //创建套接字  
    int sockfd = socket(AF_INET, SOCK_DGRAM, 0);  
    if (sockfd < 0) {  
        perror("socket");  
#ifdef _WIN32  
        WSACleanup();  
#endif  
        return -1;  
    }  
  
    //设置发送地址  
    struct sockaddr_in dest;  
    memset(&dest, 0, sizeof(dest));  
    dest.sin_family = AF_INET;  
    dest.sin_port = htons(atoi(argv[2]));  
    dest.sin_addr.s_addr = inet_addr(argv[1]);  
    char buf[128];  
    //从键盘读取需要发送的消息  
    while (fgets(buf, sizeof(buf), stdin)) {  
        buf[strcspn(buf, "\n")] = '\0'; // 去掉换行符  
        //发送消息  
        sendto(sockfd, buf, strlen(buf), 0, (struct sockaddr *)&dest, sizeof(dest));  
    }  
  
#ifdef _WIN32  
    closesocket(sockfd);  
    WSACleanup();  
#else  
    close(sockfd);  
#endif  
    return 0;  
}
```