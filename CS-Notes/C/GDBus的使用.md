> GDBus是glib库调用dbus的接口


## GBusType

```c
G_BUS_TYPE_STARTER  //激活该过程的消息总线的别名（如果有）

G_BUS_TYPE_NONE //非消息总线

G_BUS_TYPE_SYSTEM   //系统总线

G_BUS_TYPE_SESSION  //会话总线
```

## 方法一: [GDBusProxy](https://developer.gnome.org/gio/stable/GDBusProxy.html)

### 1.调用方法

初始化

```c
GDBusProxy *
g_dbus_proxy_new_for_bus_sync (GBusType bus_type, //要连接的dbus类型
                               GDBusProxyFlags flags, //flags类型
                               GDBusInterfaceInfo *info,
                               const gchar *name, //dbus总线名称
                               const gchar *object_path, //dbus对象路径
                               const gchar *interface_name, //dbus接口名称
                               GCancellable *cancellable, 
                               GError **error); //错误返回

//释放
g_object_unref(GDBusProxy);
```

调用方法

```c
GVariant *
g_dbus_proxy_call_sync (GDBusProxy *proxy,
                        const gchar *method_name,
                        GVariant *parameters,
                        GDBusCallFlags flags,
                        gint timeout_msec,
                        GCancellable *cancellable,
                        GError **error);
```

### 2.获取属性

初始化

```c
GDBusProxy *
g_dbus_proxy_new_for_bus_sync (GBusType bus_type,
                               GDBusProxyFlags flags,
                               GDBusInterfaceInfo *info,
                               const gchar *name,
                               const gchar *object_path,
                               "org.freedesktop.DBus.Properties", //选择获取属性的方法Get()
                               GCancellable *cancellable,
                               GError **error);

//释放
g_object_unref(GDBusProxy);
```

获取属性

```c
GVariant *
g_dbus_proxy_call_sync (GDBusProxy *proxy,
                        "Get", //使用Get()方法获取属性
                        g_variant_new("(ss)",
                                     "org.freedesktop.NetworkManager.Device", //dbus接口名称
                                     "State"), //要获取的dbus属性名称
                        GDBusCallFlags flags,
                        gint timeout_msec,
                        GCancellable *cancellable,
                        GError **error);
```

## 方法二: [GDBusConnection](https://developer.gnome.org/gio/stable/GDBusConnection.html)

### 1.初始化

```c
GDBusConnection *
g_bus_get_sync (GBusType bus_type,
                GCancellable *cancellable,
                GError **error);
// 释放
g_object_unref(GDBusConnection);
```

### 2.调用方法

```c
GVariant *
g_dbus_connection_call_sync (GDBusConnection *connection,
                             const gchar *bus_name,
                             const gchar *object_path,
                             const gchar *interface_name,
                             const gchar *method_name,
                             GVariant *parameters,
                             const GVariantType   *reply_type,
                             GDBusCallFlags flags,
                             gint timeout_msec,
                             GCancellable *cancellable,
                             GError **error);
```

### 3.获取属性

```c
GVariant *
g_dbus_connection_call_sync (GDBusConnection *connection,
                             const gchar *bus_name,
                             const gchar *object_path,
                             "org.freedesktop.DBus.Properties"
                             "Get", //使用Get()方法获取属性
                             g_variant_new("(ss)",
                                     "org.freedesktop.NetworkManager.Device", //dbus接口名称
                                     "State"), //要获取的dbus属性名称
                             const GVariantType *reply_type,
                             GDBusCallFlags flags,
                             gint timeout_msec,
                             GCancellable *cancellable,
                             GError **error);
```

#### 4.绑定信号

绑定

```c
guint
g_dbus_connection_signal_subscribe (GDBusConnection *connection,
                                    const gchar *sender,
                                    const gchar *interface_name,
                                    const gchar *member,
                                    const gchar *object_path,
                                    const gchar *arg0,
                                    GDBusSignalFlags flags,
                                    GDBusSignalCallback callback,
                                    gpointer user_data,
                                    GDestroyNotify user_data_free_func);
```

解绑

```c
void
g_dbus_connection_signal_unsubscribe (GDBusConnection *connection,
                                      guint subscription_id);
```

## 返回值[GVariant](https://developer.gnome.org/glib/stable/glib-GVariant.html)解析

1. 解析(以int32为例)

```c
void
g_variant_get (GVariant *value,
            const gchar *format_string,
            ...);
```

示例：

```c
GVariant *var1;
GVariant *var2;
gint ret;
g_variant_get(var1, "(v)", &var2);
g_variant_get(var2, "i", &ret);
//或者
ret = g_variant_get_int32(var2);
g_variant_unref(var1);
g_variant_unref(var2);
```

2. [GVariantType](https://developer.gnome.org/glib/stable/glib-GVariantType.html#G-VARIANT-TYPE-BOOLEAN:CAPS) 不同类型的值
3. 各种符号的含义

[GVariant Format Strings](https://developer.gnome.org/glib/stable/gvariant-format-strings.html)
4. 对于数组可以使用[g_variant_iter_loop()](https://developer.gnome.org/glib/stable/glib-GVariant.html#g-variant-iter-loop)进行遍历

```c
GVariant *var;
GVariant *iter;
gchar *str;
g_variant_get(var1, "(v)", &iter);
while(g_variant_iter_loop(iter, "(ao)", &str)) //ao为array of object path类型的值
    g_print("%s\n", str);
g_variant_unref(var);
g_variant_unref(iter);
g_free(str);
```
