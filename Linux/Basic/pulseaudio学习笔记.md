## 一、连接至pulse服务

```cpp
void context_state_callback(pa_context *c, void *userdata) {
    g_assert(c);
    switch (pa_context_get_state(c)) {
        case PA_CONTEXT_UNCONNECTED:
        case PA_CONTEXT_CONNECTING:
        case PA_CONTEXT_AUTHORIZING:
        case PA_CONTEXT_SETTING_NAME:
            break;

        case PA_CONTEXT_READY: {

        }
        case PA_CONTEXT_FAILED: {

        }
        case PA_CONTEXT_TERMINATED:
        default:
    }
}

gboolean connect_to_pulse(gpointer userdata) {
    //设置客户端属性表，自定义
    pa_proplist *proplist = pa_proplist_new();
    pa_proplist_sets(proplist, PA_PROP_APPLICATION_NAME, "Volume Control");
    pa_proplist_sets(proplist, PA_PROP_APPLICATION_ID, "org.PulseAudio.pavucontrol");
    pa_proplist_sets(proplist, PA_PROP_APPLICATION_ICON_NAME, "audio-card");
    pa_proplist_sets(proplist, PA_PROP_APPLICATION_VERSION, PACKAGE_VERSION);

    //创建连接上下文
    pa_glib_mainloop *m = pa_glib_mainloop_new(g_main_context_default());
    pa_mainloop_api *api = pa_glib_mainloop_get_api(m);
    pa_context *context = pa_context_new_with_proplist(api, nullptr, proplist);

    //上下文状态更改时的回调
    pa_context_set_state_callback(context, context_state_callback, userdata);

    //建立连接
    pa_context_connect(context, nullptr, PA_CONTEXT_NOFAIL, nullptr);
}
```

## 二、初始化获取信息

api: [https://freedesktop.org/software/pulseaudio/doxygen/introspect_8h.html](https://freedesktop.org/software/pulseaudio/doxygen/introspect_8h.html)

**常用：**

```cpp
//Server
pa_operation *     pa_context_get_server_info (pa_context *c, pa_server_info_cb_t cb, void *userdata);

//Client
pa_operation *     pa_context_get_client_info_list (pa_context *c, pa_client_info_cb_t cb, void *userdata);

//card 声卡
pa_operation *     pa_context_get_card_info_list (pa_context *c, pa_card_info_cb_t cb, void *userdata);

//Source 声卡输入设备
pa_operation *     pa_context_get_source_info_list (pa_context *c, pa_source_info_cb_t cb, void *userdata);

//Sink 声卡输出设备
pa_operation *     pa_context_get_sink_info_list (pa_context *c, pa_sink_info_cb_t cb, void *userdata);

//Module 模块
pa_operation *    pa_context_get_module_info_list (pa_context *c, pa_module_info_cb_t cb, void *userdata);

//Sink Input 正在使用的输入源
pa_operation *     pa_context_get_sink_input_info_list (pa_context *c, pa_sink_input_info_cb_t cb, void *userdata);

//Source Output 正在使用的输出源
pa_operation *     pa_context_get_source_output_info_list (pa_context *c, pa_source_output_info_cb_t cb, void *userdata);
```

## 三、订阅功能

api: [https://freedesktop.org/software/pulseaudio/doxygen/subscribe_8h.html](https://freedesktop.org/software/pulseaudio/doxygen/subscribe_8h.html)

```cpp
//绑定订阅的回调函数
pa_context_set_subscribe_callback(context, subscribe_cb, userdata);

//绑定订阅事件
pa_context_subscribe(context, (pa_subscription_mask_t)
                    (PA_SUBSCRIPTION_MASK_SINK|
                    PA_SUBSCRIPTION_MASK_SOURCE|
                    PA_SUBSCRIPTION_MASK_SINK_INPUT|
                    PA_SUBSCRIPTION_MASK_SOURCE_OUTPUT|
                    PA_SUBSCRIPTION_MASK_CLIENT|
                    PA_SUBSCRIPTION_MASK_SERVER|
                    PA_SUBSCRIPTION_MASK_CARD|
                    PA_SUBSCRIPTION_MASK_MODULE), nullptr, nullptr);

//subscribe_cb example
void subscribe_cb(pa_context *c, pa_subscription_event_type_t t, uint32_t index, void *userdata) {
    switch (t & PA_SUBSCRIPTION_EVENT_FACILITY_MASK) {
        case PA_SUBSCRIPTION_EVENT_SINK:
            if ((t & PA_SUBSCRIPTION_EVENT_TYPE_MASK) == PA_SUBSCRIPTION_EVENT_REMOVE)
                removeSink(index);
            else {
                pa_operation *o;
                //使用index获取单个输出设备的信息
                if (!(o = pa_context_get_sink_info_by_index(c, index, sink_cb, userdata))) {
                    return;
                }
                pa_operation_unref(o);
            }
            break;
        case PA_SUBSCRIPTION_MASK_SOURCE:
            break;
        case PA_SUBSCRIPTION_MASK_SINK_INPUT:
            break;
        case PA_SUBSCRIPTION_MASK_SOURCE_OUTPUT:
            break;
        case PA_SUBSCRIPTION_MASK_CLIENT:
            break;
        case PA_SUBSCRIPTION_MASK_SERVER:
            break;
        case PA_SUBSCRIPTION_MASK_CARD:
            break;
        case PA_SUBSCRIPTION_MASK_MODULE:
            break;
    }
}
```

## 四、设置接口

api:

[https://freedesktop.org/software/pulseaudio/doxygen/introspect_8h.html](https://freedesktop.org/software/pulseaudio/doxygen/introspect_8h.html)

[https://freedesktop.org/software/pulseaudio/doxygen/context_8h.html](https://freedesktop.org/software/pulseaudio/doxygen/context_8h.html)

## 五、pavucontrol-qt程序源码解析思维导图

![](https://image-hosts-1305019894.file.myqcloud.com/2021/04/15/8489c3f295104.png#alt=image)
