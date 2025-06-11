```c
//  
// Created by Administrator on 2025/6/10.  
//  
//  
// Created by Administrator on 2025/6/6.  
//  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
// 替换UNIX网络头文件为Windows网络头文件  
#include <io.h>  
#include <winsock2.h>  
#include <ws2tcpip.h>  
  
// 需要链接Winsock库  
#pragma comment(lib, "ws2_32.lib")  
  
/*  
功能：创建UDP接收端(服务器)  
返回值：成功返回0，失败返回-1  
*/  
int main(int argc, char const *argv[]){  
    // 初始化Winsock  
    WSADATA wsaData;  
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {  
        fprintf(stderr, "WSAStartup failed\n");  
        return -1;  
    }  
  
    if(argc<3)  
    {  
        fprintf(stderr,"Usage %s <ip><port>\n",argv[0]);  
        WSACleanup(); // 清理Winsock  
        exit(1);  
    }  
    int sockfd;  
    struct sockaddr_in recvaddr,sentaddr;  
    socklen_t addr_len = sizeof(recvaddr);  
    char buffer[128]={0};  
  
    /* 1.创建UDP套接字 */    if ((sockfd = socket(AF_INET, SOCK_DGRAM, 0)) < 0) {  
        perror("socket creation failed");  
        return -1;  
    }  
  
    /* 2.配置地址 */    memset(&recvaddr, 0, sizeof(recvaddr));  
    recvaddr.sin_family = AF_INET;  
    recvaddr.sin_addr.s_addr = inet_addr(argv[1]);  
    recvaddr.sin_port = htons(atoi(argv[2]));  
  
    /* 3.绑定套接字 */    if (bind(sockfd, (struct sockaddr*)&recvaddr, sizeof(recvaddr)) < 0) {  
        perror("bind failed");  
        close(sockfd);  
        return -1;  
    }  
  
    /* 4.接收消息 */    printf("Waiting for messages...\n");  
    while (1) {  
        int n = recvfrom(sockfd, buffer, sizeof(buffer), 0,  
                         (struct sockaddr*)&sentaddr, &addr_len);  
        if (n < 0) {  
            perror("recvfrom failed");  
            break;  
        }  
        buffer[n] = '\0';  
        printf("Received from %s:%d - %s\n",  
               inet_ntoa(sentaddr.sin_addr),  
               ntohs(sentaddr.sin_port), buffer);  
    }  
  
    close(sockfd);  
    // 在程序结束前清理Winsock  
    WSACleanup();  
    return 0;  
}
```