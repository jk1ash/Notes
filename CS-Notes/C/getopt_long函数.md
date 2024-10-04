## 描述

getopt_long()是一种函数，被用来解析命令行选项参数。与getopt相比，支持长选项的参数输入。

## 例子

```c
#include <stdio.h>
#include <getopt.h>

static void print_help()

{
    printf("\nUsage:command [option]\n\n\n"
           "-a, --all  \tShow all logs\n"
           "-c \t\tShow critical log\n"
           "-d \t\tShow debug log\n"
           "-e \t\tShow error log\n"
           "-h, --help \tShow Help\n"
           "-i \t\tShow info log\n"
           "-m \t\tShow message log\n"
           "-w \t\tShow warning log\n");
    return;
}

int main(int argc, char **argv)
{
    //所有的短选项，单冒号是必须要有参数，双冒号是选择性的有否参数，其它是无须参数
    const char *optstrings = "acdehimw";
    //绑定短选项和长选项
    const struct option longopts[] = {
        {"all", 0, NULL, 'a'},
        {"help", 0, NULL, 'h'},
    };

    if (argc > 1)
    {
        while (1)
        {
            int arg;
            arg = getopt_long(argc, argv, optstrings, longopts, NULL);

            if (arg == -1)
                break;

            switch (arg)
            {
            case 'a':
                printf("This is all log!\n");
                break;
            case 'c':
                printf("This is Critical!\n");
                break;
            case 'd':
                printf("This is Debug!\n");
                break;
            case 'e':
                printf("This is Error!\n");
                break;
            case 'h':
                print_help();
                break;
            case 'i':
                printf("This is Info!\n");
                break;
            case 'm':
                printf("This is Message!\n");
                break;
            case 'w':
                printf("This is Warning!\n");
                break;
            default:
                print_help();
                break;
            }
        }
    }
    else
    {
        print_help();
    }

    return 0;
}
```
