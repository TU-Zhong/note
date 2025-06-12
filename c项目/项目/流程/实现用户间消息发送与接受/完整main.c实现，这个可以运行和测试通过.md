好的，没问题。为了确保万无一失，避免在多处修改时出错，这里是为您准备好的、一个**完整且可以直接使用**的 `main.c` 文件。

这个版本包含了我们之前讨论的所有功能：

1. 能够处理 `list` 和 `exit` 命令。
2. 实现了新的 `send` 命令，并能正确解析参数。
3. 通过一个上下文结构体（`app_context_t`），安全地将所有需要的模块（`communicator`, `user_list`, `console`）传递给回调函数。

请直接用下面的代码**完整替换**您项目中 `src/main.c` 文件的内容。

C

```
#include "core/communicator.h"
#include "ui/console.h"
#include "model/user.h"
#include <stdio.h>
#include <stdlib.h> // For atoi
#include <winsock2.h>

// =================================================================
// || 上下文结构体，用于将多个模块的指针传递给回调函数          ||
// =================================================================
typedef struct {
    user_list_t* user_list;
    communicator_t* comm;
    console_t* console;
} app_context_t;


// =================================================================
// || 命令回调函数定义                                          ||
// =================================================================

/**
 * @brief 当用户在控制台输入 "list" 时被调用
 */
void on_list_command(void* context, int argc, char** argv) {
    app_context_t* app_ctx = (app_context_t*)context;
    console_display_users(app_ctx->console, app_ctx->user_list);
}

/**
 * @brief 当用户在控制台输入 "send" 命令时被调用
 */
void on_send_command(void* context, int argc, char** argv) {
    // 参数应为2个: [0]是用户索引, [1]是消息内容
    if (argc != 2 || argv[0] == NULL || argv[1] == NULL) {
        return; 
    }

    app_context_t* app_ctx = (app_context_t*)context;
    int user_index = atoi(argv[0]); // 将字符串索引转换为整数
    const char* message = argv[1];

    // 获取当前用户列表的快照，以进行安全访问
    user_node_t users_snapshot[128];
    int count = user_list_get_all(app_ctx->user_list, users_snapshot, 128);

    // 检查用户索引是否有效
    if (user_index < 0 || user_index >= count) {
        console_print_message(app_ctx->console, "Error: Invalid user index.");
        return;
    }

    // 从快照中获取目标用户的信息
    user_node_t* target_user = &users_snapshot[user_index];
    
    // 调用网络层发送消息
    communicator_send_message(app_ctx->comm, target_user->ipAddress, target_user->port, message);

    // （可选）在自己的控制台也显示一下发出的消息
    char self_display_buffer[256];
    snprintf(self_display_buffer, sizeof(self_display_buffer), "Me -> %s: %s", target_user->userName, message);
    console_print_message(app_ctx->console, self_display_buffer);
}


/**
 * @brief 当用户在控制台输入 "exit" 时被调用
 */
void on_exit_command(void* context, int argc, char** argv) {
    app_context_t* app_ctx = (app_context_t*)context;
    if (app_ctx->console) {
        app_ctx->console->should_exit = 1; // 设置退出标志
    }
}


// =================================================================
// || 程序主入口                                                ||
// =================================================================
int main(int argc, char* argv[]) {
    // 检查命令行参数，现在端口变为可选
    if (argc < 2) {
        printf("Usage: %s <UserName> [Port]\n", argv[0]);
        printf("Example: %s UserA 2425\n", argv[0]);
        printf("If Port is not specified, it will default to 2425.\n");
        return 1;
    }
    const char* user_name = argv[1];
    // 如果用户没有提供端口，则使用默认端口
    int port = (argc > 2) ? atoi(argv[2]) : 2425;

    // 1. 初始化 Winsock
    WSADATA wsaData;
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {
        printf("WSAStartup failed.\n");
        return 1;
    }

    // 2. 初始化各个模块
    communicator_t comm = {0};
    user_list_t user_list = {0};
    console_t console = {0};

    // 创建并设置统一的上下文
    app_context_t app_ctx = { &user_list, &comm, &console };

    user_list_init(&user_list);
    console_init(&console);
    
    // 注册所有回调函数，包括新的 on_send_command
    console_register_callbacks(&console, &app_ctx, on_list_command, on_send_command, on_exit_command);

    if (communicator_init(&comm, &user_list, &console, user_name, port) != 0) {
        printf("Communicator init failed.\n");
        // 清理已初始化的模块
        user_list_destroy(&user_list);
        console_destroy(&console);
        WSACleanup();
        return 1;
    }

    printf("Welcome, %s! Your client is running on port %d.\n", user_name, port);
    printf("--------------------------------------------------\n");
    printf("Commands:\n");
    printf("  list                  - Show online users\n");
    printf("  send <index> <message> - Send a message\n");
    printf("  exit                  - Quit the application\n");
    printf("--------------------------------------------------\n");


    // 3. 广播上线通知
    communicator_broadcast_entry(&comm);

    // 4. 运行控制台主循环
    console_run(&console);

    // 5. 程序即将退出，广播下线通知
    printf("Broadcasting exit notification...\n");
    communicator_broadcast_exit(&comm);
    
    // 6. 清理和销毁所有模块
    communicator_destroy(&comm);
    user_list_destroy(&user_list);
    console_destroy(&console);
    WSACleanup();

    printf("Cleanup complete. Goodbye!\n");
    return 0;
}
```

**下一步操作**：

1. 将以上代码**完整复制**并**覆盖**您项目中的 `src/main.c` 文件。
2. 确保您的 `CMakeLists.txt` 文件中包含了 `src/main.c`。
3. 点击 `Build -> Rebuild Project` 重新编译整个项目。
4. 开始进行我们上一轮讨论的端到端测试。

祝您测试顺利！