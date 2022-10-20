## g_log日志重定向到文件

```c
#include <stdio.h>
#include <glib.h>
#include <time.h>

enum
{
    LOG_INFO,
    LOG_MESSAGE,
    LOG_WARNING,
    LOG_DEBUG,
    LOG_CRIT,
    LOG_ERR,
};

void log2file(const gchar *log_domain, GLogLevelFlags log_level, const gchar *message, gpointer user_data)
{
    FILE *fp;
    time_t time_ptr;
    struct tm *tm_ptr = NULL;
    char current_time[30];
    char log_path[30];

    const char *logger_prioritynames[] = {
        "INFO",
        "MSG",
        "WARN",
        "DEBUG",
        "CRIT",
        "ERROR"};

    int prority = LOG_WARNING;

    switch (log_level)
    {
    case G_LOG_LEVEL_INFO:
        prority = LOG_INFO;
        break;
    case G_LOG_LEVEL_MESSAGE:
        prority = LOG_MESSAGE;
        break;
    case G_LOG_LEVEL_WARNING:
        prority = LOG_WARNING;
        break;
    case G_LOG_LEVEL_DEBUG:
        prority = LOG_DEBUG;
        break;
    case G_LOG_LEVEL_CRITICAL:
        prority = LOG_CRIT;
        break;
    case G_LOG_LEVEL_ERROR:
        prority = LOG_ERR;
        break;
    default:
        break;
    }

    /* 获取当前时间(年月日时分秒) */
    GDateTime* dt = g_date_time_new_now_local();
    sprintf(current_time, "%d/%d/%d %d:%d:%d",
                g_date_time_get_year(dt),
                g_date_time_get_month(dt),
                g_date_time_get_day_of_month(dt),
                g_date_time_get_hour(dt),
                g_date_time_get_minute(dt),
                g_date_time_get_second(dt));

    /* 追加到日志文件 */
    fp = fopen(user_data, "a");

    if (fp == NULL)
    {
        fclose(fp);
        return;
    }

    /* 日志格式：时间戳[日志等级]----------日志信息 */
    fprintf(fp, "%s[%s]-----------%s\n",
            current_time,
            logger_prioritynames[prority],
            message);

    fclose(fp);
}
```

调用

```c
g_log_set_handler(NULL,
                         G_LOG_LEVEL_MASK | G_LOG_FLAG_FATAL | G_LOG_FLAG_RECURSION,
                         log2file,
                        "/tmp/test.log");
```

## 如何开启g_dbug的所有调试信息

```bash
G_MESSAGES_DEBUG=all ./test
```
