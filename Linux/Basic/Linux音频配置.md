## alsa配置

### init配置

**作用:** 用于alsa初始化，配置alsa控制器的初始值。使用`alsactl init`命令会调用到该配置。
**路径:** /usr/share/alsa/init/
**配置：**
以hda为例
1. 查看默认配置/usr/share/alsa/init/00main，也可根据下面格式添加自定义配置文件
`CARDINFO{driver}=="HDA-Intel", INCLUDE="hda", GOTO="init_end"`
- CARDINFO{driver}为声卡名称，通过`cat /proc/asound/cards`查看
- INCLUDE为指向的配置文件，hda则指向/usr/share/alsa/init/hda文件
2. 查看配置/usr/share/alsa/init/hda
```shell
# Configuration for HDA Intel driver (High Definition Audio - Azalia)
# HT5300 ALC897
CARDINFO{mixername}=="Realtek ALC897", \
  ATTR{vendor}=="0x8086", ATTR{device}=="0x4dc8", \
  ATTR{subsystem_vendor}=="0x8086", ATTR{subsystem_device}=="0x7270", \
  GOTO="Itep HT5300"

LABEL="Itep TC9053"
# playback
CTL{reset}="mixer"
CTL{name}="Master Playback Volume", CTL{index}="0", CTL{values}="80%"
CTL{name}="Master Playback Switch", CTL{index}="0", CTL{values}="on"
CTL{name}="PCM Playback Volume", CTL{index}="0", CTL{values}="100%"
CTL{name}="Mic Playback Volume", CTL{index}="0", CTL{values}="0%"
CTL{name}="Mic Playback Switch", CTL{index}="0", CTL{values}="off"
CTL{name}="Mic Boost Volume", CTL{index}="0", CTL{values}="0.00dB"
CTL{name}="IEC958 Playback Switch", CTL{index}="0", CTL{values}="on"
CTL{name}="IEC958 Playback Switch", CTL{index}="1", CTL{values}="off"
CTL{name}="IEC958 Playback Switch", CTL{index}="2", CTL{values}="on"
CTL{name}="IEC958 Playback Switch", CTL{index}="3", CTL{values}="off"
CTL{name}="IEC958 Playback Switch", CTL{index}="4", CTL{values}="off"
CTL{name}="IEC958 Playback Switch", CTL{index}="5", CTL{values}="off"
CTL{name}="IEC958 Playback Switch", CTL{index}="6", CTL{values}="off"
CTL{name}="Loopback Mixing", CTL{index}="0", CTL{values}="Disabled"
# capture
CTL{reset}="mixer"
CTL{name}="Capture Volume", CTL{index}="0", CTL{values}="80%"
CTL{name}="Capture Switch", CTL{index}="0", CTL{values}="on"
CTL{name}="Mic Boost Volume", CTL{index}="0", CTL{values}="0.00dB"
RESULT="true", EXIT="return"
```
绑定配置和声卡：
- CARDINFO{mixername}为混音器名称，一般就是实际声卡名称
- ATTR{vendor}，声卡的vendor id
- ATTR{device}，声卡的device id
- ATTR{subsystem_vendor}，声卡的subsystem vendor id
- ATTR{subsystem_device}，声卡的subsystem device id
- GOTO，指向具体配的label
mixername可使用`alsamixer`，查看chip字段
pice接口的声卡，先查看具体的pci id，使用`lspci`, 查看Audio device的字样，得到声卡的pci id
`00:1f.3` Audio device: Intel Corporation DDevice 4dc8 (rev 01)
查看`ls /sys/devices/pci0000:00/0000:00:1f.3`， 可看到相应的vendor、device、subsystem_vendor、subsystem_device，使用cat可直接读取id

具体配置：
- label，配置的名称，用于和声卡绑定
- CTL{name}为控制器名称
- CTL{values}为控制器的值
可通过alsamixer查看相应的控制器和要设定的默认值
### cards配置

**作用:** alsa声卡配置，绑定物理设备和pcm设备，或进行pcm设备的控制器配置。该配置会影响aplay -L和arecord -L的设备列表，以及pulseaudio的设备列表。

**路径:** /usr/share/alas/cards/

**配置:**

以Q281的声卡ES8316为例

1. 修改默认配置/usr/share/alas/cards/aliases.conf，添加自定义声卡配置文件

`rockchip_es8316 cards.ES8316`

2. 自定义配置ES8316.conf

```shell
# configuration for FRONT connections which just expose the
# audio out device

<confdir:pcm/front.conf>

ES8316.pcm.front.0 {
        @args [ CARD AES0 AES1 AES2 AES3 ]
        @args.CARD {
                type string
        }
        @args.AES0 {
                type integer
        }
        @args.AES1 {
                type integer
        }
        @args.AES2 {
                type integer
        }
        @args.AES3 {
                type integer
        }
    type hw
    device 0
    card $CARD
}

# default with dmix+softvol & dsnoop
ES8316.pcm.default {
        @args [ CARD ]
        @args.CARD {
                type string
        }
        type asym
        playback.pcm {
                type plug
                slave.pcm {
                        type softvol
                        slave.pcm {
                                @func concat
                                strings [ "dmix:" $CARD ]
                        }
                        control {
                                name "DAC Playback Volume"
                                card $CARD
                        }
                }
        }
        capture.pcm {
                type plug
                slave.pcm {
                        type softvol
                        slave.pcm {
                                @func concat
                                strings [ "dsnoop:" $CARD ]
                        }
                        control {
                                name "ADC Capture Volume"
                                card $CARD
                        }
                        min_dB -9999999.0
                        max_dB 0.0
                        resolution 192
                }
                # to avoid possible phase inversions with digital mics
                route_policy copy
        }
        hint.device 0
}

# configuration for HDMI connection which just expose the
# audio out device
<confdir:pcm/hdmi.conf>

ES8316.pcm.hdmi.0 {
        @args [ CARD DEVICE CTLINDEX AES0 AES1 AES2 AES3 ]
        @args.CARD {
                type string
        }
        @args.DEVICE {
                type integer
        }
        @args.CTLINDEX {
                type integer
        }
        @args.AES0 {
                type integer
        }
        @args.AES1 {
                type integer
        }
        @args.AES2 {
                type integer
        }
        @args.AES3 {
                type integer
        }
        type hw
    device 1
    card $CARD 
}
```

## pulse配置

**路径:** /usr/share/pulseaudio/alsa-mixer/

**profile-sets:** pulse设备配置文件

**paths:** pulse设备路径映射表
### path配置
例如修改`analog-input-internal-mic.conf`关闭麦克风增强
```shell
# off表示关闭该通道，alsa可手动开启，会被pulse还原；
# merge表示alsa和pulse配置混合，主要受pulse影响，alsa无法手动更改；
# ignore表示该通道受alsa影响，alsa可手动更改，不会被pulse还原

[Element Int Mic Boost]
required-any = any
switch = select
volume = ignore
override-map.1 = all
override-map.2 = all-left,all-right
```

### profile配置

默认使用default.conf, 建议不要直接对default.conf操作，进行自定义配置
以5300的ALC897声卡为例：

a. 修改/lib/udev/rules.d/90-pulseaudio.rules，添加自定义声卡的pid，vid以及配置文件名称。一般不建议直接修改90-pulseaudio.rules，后续升级容易被覆盖，可自定义规则文件91-custom-pulseaudio.rules。
```shell
# itep PulseAudio Sound Card Rules
SUBSYSTEM!="sound", GOTO="pulseaudio_end"
ACTION!="change", GOTO="pulseaudio_end"
KERNEL!="card*", GOTO="pulseaudio_end"
SUBSYSTEMS=="usb", GOTO="pulseaudio_check_usb"
SUBSYSTEMS=="pci", GOTO="pulseaudio_check_pci"
SUBSYSTEMS=="firewire", GOTO="pulseaudio_firewire_quirk"
LABEL="pulseaudio_check_usb"
GOTO="pulseaudio_end"
LABEL="pulseaudio_check_pci"

# HT5300 ALC897
ATTRS{vendor}=="0x8086", ATTRS{device}=="0x4dc8", ATTRS{subsystem_vendor}=="0x8086", ATTRS{subsystem_device}=="0x7270", ENV{PULSE_PROFILE_SET}="ht5300-alc897.conf"

GOTO="pulseaudio_end"
LABEL="pulseaudio_firewire_quirk"
LABEL="pulseaudio_end"
```
b. 在/usr/share/pulseaudio/alsa-mixer/profile-sets下添加配置文件ht5300-alc897.conf
```shell
# example, 详细配置可参考default.conf
[General]
auto-profiles = no

[Mapping analog-stereo]
device-strings = front:%f
channel-map = left,right
paths-output = analog-output analog-output-lineout analog-output-speaker analog-output-headphones analog-output-headphones-2
paths-input = ht5300-analog-input-internal-mic ht5300-analog-input-mic analog-input-front-mic analog-input-rear-mic analog-input-dock-mic analog-input analog-input-linein analog-input-aux analog-input-video analog-input-tvtuner analog-input-fm analog-input-mic-line analog-input-headphone-mic analog-input-headset-mic
priority = 15

# If everything else fails, try to use hw:0 as a stereo device...
[Mapping stereo-fallback]
device-strings = hw:%f
fallback = yes
channel-map = front-left,front-right
paths-output = analog-output analog-output-lineout analog-output-speaker analog-output-headphones analog-output-headphones-2
paths-input = ht5300-analog-input-internal-mic ht5300-analog-input-mic analog-input-front-mic analog-input-rear-mic analog-input-dock-mic analog-input analog-input-linein analog-input-aux analog-input-video analog-input-tvtuner analog-input-fm analog-input-mic-line analog-input-headphone-mic analog-input-headset-mic
priority = 1

# ...and if even that fails, try to use hw:0 as a mono device.
[Mapping mono-fallback]
device-strings = hw:%f
fallback = yes
channel-map = mono
paths-output = analog-output analog-output-lineout analog-output-speaker analog-output-headphones analog-output-headphones-2 analog-output-mono
paths-input = ht5300-analog-input-internal-mic ht5300-analog-input-mic analog-input-front-mic analog-input-rear-mic analog-input-dock-mic analog-input analog-input-linein analog-input-aux analog-input-video analog-input-tvtuner analog-input-fm analog-input-mic-line analog-input-headset-mic
priority = 1

[Mapping hdmi-stereo]
description = Digital Stereo (HDMI)
device-strings = hdmi:%f
paths-output = ht5300-hdmi-output-0
channel-map = left,right
priority = 9
direction = output

[Mapping hdmi-surround]
description = Digital Surround 5.1 (HDMI)
device-strings = hdmi:%f
paths-output = ht5300-hdmi-output-0
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe
priority = 8
direction = output

[Mapping hdmi-surround71]
description = Digital Surround 7.1 (HDMI)
device-strings = hdmi:%f
paths-output = ht5300-hdmi-output-0
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe,side-left,side-right
priority = 8
direction = output

[Mapping hdmi-dts-surround]
description = Digital Surround 5.1 (HDMI/DTS)
device-strings = dcahdmi:%f
paths-output = ht5300-hdmi-output-0
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe
priority = 6
direction = output

[Mapping hdmi-stereo-extra1]
description = Digital Stereo (HDMI 2)
device-strings = hdmi:%f,1
paths-output = ht5300-hdmi-output-1
channel-map = left,right
priority = 7
direction = output

[Mapping hdmi-surround-extra1]
description = Digital Surround 5.1 (HDMI 2)
device-strings = hdmi:%f,1
paths-output = ht5300-hdmi-output-1
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe
priority = 6
direction = output

[Mapping hdmi-surround71-extra1]
description = Digital Surround 7.1 (HDMI 2)
device-strings = hdmi:%f,1
paths-output = ht5300-hdmi-output-1
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe,side-left,side-right
priority = 6
direction = output

[Mapping hdmi-dts-surround-extra1]
description = Digital Surround 5.1 (HDMI 2/DTS)
device-strings = dcahdmi:%f,1
paths-output = ht5300-hdmi-output-1
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe
priority = 6
direction = output

[Profile output:analog-stereo+output:hdmi-stereo+output:hdmi-stereo-extra1+input:analog-stereo]
description = HT5300
output-mappings = analog-stereo hdmi-stereo-extra1 hdmi-stereo
input-mappings = analog-stereo
```
 - description：配置的描述，暂未发现实际意义
 - device-strings: PulseAudio绑定ALSA设备字符串，“%f”为卡号。ALSA设备字符串可用aplay -L和arecord -L进行查询。
 - paths-output和path-input为conf设备路径，读取/usr/share/pulseaudio/alsa-mixer/paths，可自定义，按顺序执行
 - priority：优先级设定，暂未发现实际意义
 - direction：声音方向设定，指定输入input还是输出output

c. 自定义paths配置，/usr/share/pulseaudio/alsa-mixer/paths
ht5300-analog-input-mic.conf
```shell
# This file is part of PulseAudio.
#
# PulseAudio is free software; you can redistribute it and/or modify
# it under the terms of the GNU Lesser General Public License as
# published by the Free Software Foundation; either version 2.1 of the
# License, or (at your option) any later version.
#
# PulseAudio is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
# General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public License
# along with PulseAudio; if not, see <http://www.gnu.org/licenses/>.

; For devices where a 'Mic' or 'Mic Boost' element exists
;
; See analog-output.conf.common for an explanation on the directives

[General]
priority = 87
description-key = analog-input-microphone

[Jack Mic]
required-any = any

[Jack Mic Phantom]
required-any = any
state.plugged = unknown
state.unplugged = unknown

[Jack Mic - Input]
required-any = any

[Element Capture]
switch = mute
volume = merge
override-map.1 = all
override-map.2 = all-left,all-right

[Element Mic Boost]
required-any = any
switch = select
volume = ignore
override-map.1 = all
override-map.2 = all-left,all-right

[Option Mic Boost:on]
name = input-boost-on

[Option Mic Boost:off]
name = input-boost-off

[Element Mic]
required-any = any
switch = mute
volume = merge
override-map.1 = all
override-map.2 = all-left,all-right

[Element Input Source]
enumeration = select

[Option Input Source:Mic]
name = analog-input-microphone
required-any = any

[Element Capture Source]
enumeration = select

[Option Capture Source:Mic]
name = analog-input-microphone
required-any = any

[Element PCM Capture Source]
enumeration = select

[Option PCM Capture Source:Mic]
name = analog-input-microphone
required-any = any

[Option PCM Capture Source:Mic-In/Mic Array]
name = analog-input-microphone
required-any = any

;;; Some AC'97s have "Mic Select" and "Mic Boost (+20dB)"

[Element Mic Select]
enumeration = select

[Option Mic Select:Mic1]
name = input-microphone
priority = 20

[Option Mic Select:Mic2]
name = input-microphone
priority = 19

[Element Mic Boost (+20dB)]
switch = select
volume = ignore

[Option Mic Boost (+20dB):on]
name = input-boost-on

[Option Mic Boost (+20dB):off]
name = input-boost-off

[Element Front Mic]
switch = off
volume = off

[Element Internal Mic]
switch = off
volume = off

[Element Rear Mic]
switch = off
volume = off

[Element Dock Mic]
switch = off
volume = off

[Element Dock Mic Boost]
switch = off
volume = off

[Element Internal Mic Boost]
switch = off
volume = off

[Element Front Mic Boost]
switch = off
volume = off

[Element Rear Mic Boost]
switch = off
volume = off

.include analog-input-mic.conf.common

```
参照analog-input-mic.conf编写，修改了Element Mic Boost的Volume字段，ignore代表使用alsa init的配置；zero/off表示将麦克风增强音量设置为0和关闭，效果上是一致的；merge表示使用pulse的默认配置
ht5300-hdmi-output-0.conf
```shell
[General]
description = HDMI / DisplayPort 2
priority = 58
eld-device = auto

[Properties]
device.icon_name = video-display

[Jack HDMI/DP]
append-pcm-to-name = yes
required = ignore
```
修改description以改变pulseaudio的port名称
## 音质和时延（mos && offset）
华为实验室的实验音质标准PESQ(P.862.1 P.862.2)
mos值经主要受声卡芯片调教和音量大小影响；offset值主要受测试工具的缓存区大小影响，缓冲区越小，时延越低。
### 音质
以5300的ALC声卡为例
| 扬声器 | 麦克风 | micboost | mos |
|-----|-----|----------|-----------|
| 100 | 100 | 22       | 4.44-4.46 |
| 100 | 100 | 0        | 4.36-4.45 |
| 100 | 80  | 22       | 4.49-4.51 |
| 100 | 80  | 0        | 4.35-4.40 |
| 95  | 95  | 22       | 4.48-4.49 |
| 95  | 95  | 0        | 4.36-4.41 |
| 95  | 90  | 22       | 4.48-4.50 |
| 95  | 90  | 0        | 4.35-4.40 |
| 90  | 90  | 22       | 4.49-4.51 |
根据上述对照表格，可查看到开启micboost一级的情况下，音质为4.45-4.51上下；关闭micboost的情况下，音质为4.35-4.45。具体调试时可借助alsa工具`alsamixer`进行调节，来达到较好音质。
### 时延
**减小buffer_size的方式：**
1. 修改alsa的默认配置， 在启用pulse的情况下无效
修改 /usr/share/alsa/pcm/dmix.conf, /usr/share/alsa/pcm/dsnoop.conf
修改periods和period_size的default
```shell
slave {
		pcm {
			type hw
			card $CARD
			device $DEV
			subdevice $SUBDEV
		}
		format $FORMAT
		rate $RATE
		period_size {
			@func refer
			name {
				@func concat
				strings [
					"defaults.dmix."
					{
						@func card_driver
						card $CARD
					}
					".period_size"
				]
			}
			default 512
		}		
		period_time {
			@func refer
			name {
				@func concat
				strings [
					"defaults.dmix."
					{
						@func card_driver
						card $CARD
					}
					".period_time"
				]
			}
			default -1
		}		
		periods {
			@func refer
			name {
				@func concat
				strings [
					"defaults.dmix."
					{
						@func card_driver
						card $CARD
					}
					".periods"
				]
			}
			default 8
		}
	}
```
2. 指定alsa工具的缓存区大小
`arecord -c2 -r44100 -fs16_le --buffer_size=512 |aplay -c2 -r44100 -fs16_le --buffer_size=512`
## 修改优先级和switch-on-connect插件实现摄像头不自动切换
1. 修改声卡优先级
```c
// sink.c

unsigned pa_device_init_priority(pa_proplist *p) {
    const char *s;
    unsigned priority = 0;
    pa_assert(p);
    if ((s = pa_proplist_gets(p, PA_PROP_DEVICE_CLASS))) {
        if (pa_streq(s, "sound"))
            priority += 9000;
        else if (pa_streq(s, "filter"))
            priority += 9039;
        else if (!pa_streq(s, "modem"))
            priority += 1000;
    }
    if ((s = pa_proplist_gets(p, PA_PROP_DEVICE_FORM_FACTOR))) {
        if (pa_streq(s, "headphone"))
            priority += 900;
        else if (pa_streq(s, "hifi"))
            priority += 600;
        else if (pa_streq(s, "speaker"))
            priority += 500;
        else if (pa_streq(s, "portable"))
            priority += 450;
        else if (pa_streq(s, "webcam"))
            priority -= 1000;
    }
    if ((s = pa_proplist_gets(p, PA_PROP_DEVICE_BUS))) {
        if (pa_streq(s, "bluetooth"))
            priority += 50;
        else if (pa_streq(s, "usb"))
            priority += 40;
        else if (pa_streq(s, "pci"))
            priority += 20;
    }
    if ((s = pa_proplist_gets(p, PA_PROP_DEVICE_PROFILE_NAME))) {
        if (pa_startswith(s, "analog-"))
            priority += 9;
        else if (pa_startswith(s, "iec958-"))
            priority += 8;
    }
    return priority;
}
```
3. switch-on-connect增加优先级判断
```c
// module-switch-on-connect.c

static pa_hook_result_t sink_put_hook_callback(pa_core *c, pa_sink *sink, void* userdata) {
    const char *s;
    struct userdata *u = userdata;
    pa_assert(c);
    pa_assert(sink);
    pa_assert(userdata);
    /* Don't want to run during startup or shutdown */
    if (c->state != PA_CORE_RUNNING)
        return PA_HOOK_OK;
    pa_log_debug("Trying to switch to new sink %s", sink->name);
    /* Don't switch to any internal devices except HDMI */
    s = pa_proplist_gets(sink->proplist, PA_PROP_DEVICE_STRING);
    if (s && !pa_startswith(s, "hdmi")) {
        s = pa_proplist_gets(sink->proplist, PA_PROP_DEVICE_BUS);
        if (pa_safe_streq(s, "pci") || pa_safe_streq(s, "isa")) {
            pa_log_debug("Refusing to switch to sink on %s bus", s);
            return PA_HOOK_OK;
        }
    }
    /* Ignore sinks matching the blacklist regex */
    if (u->blacklist && (pa_match(u->blacklist, sink->name) > 0)) {
        pa_log_info("Refusing to switch to blacklisted sink %s", sink->name);
        return PA_HOOK_OK;
    }
    /* Ignore virtual sinks if not configured otherwise on the command line */
    if (u->ignore_virtual && !(sink->flags & PA_SINK_HARDWARE)) {
        pa_log_debug("Refusing to switch to virtual sink");
        return PA_HOOK_OK;
    }
    /* No default sink, nothing to move away, just set the new default */
    if (!c->default_sink) {
        pa_core_set_configured_default_sink(c, sink->name);
        return PA_HOOK_OK;
    }
    if (c->default_sink == sink) {
        pa_log_debug("%s already is the default sink", sink->name);
        return PA_HOOK_OK;
    }
    if (u->only_from_unavailable)
        if (!c->default_sink->active_port || c->default_sink->active_port->available != PA_AVAILABLE_NO) {
            pa_log_debug("Current default sink is available and module argument only_from_unavailable was set");
            return PA_HOOK_OK;
        }
    /* If the new sink has a lower priority than the current default, refusing to switch */
    if (c->default_sink->priority > sink->priority) 
    {
        pa_log_info("Refusing to switch to sink %s with lower priority than current default sink %s", sink->name, c->default_sink->name);
        return PA_HOOK_OK;
    }
    /* Actually do the switch to the new sink */
    pa_core_set_configured_default_sink(c, sink->name);
    return PA_HOOK_OK;
}

static pa_hook_result_t source_put_hook_callback(pa_core *c, pa_source *source, void* userdata) {
    const char *s;
    struct userdata *u = userdata;
    pa_assert(c);
    pa_assert(source);
    pa_assert(userdata);
    /* Don't want to run during startup or shutdown */
    if (c->state != PA_CORE_RUNNING)
        return PA_HOOK_OK;
    /* Don't switch to a monitoring source */
    if (source->monitor_of)
        return PA_HOOK_OK;
    pa_log_debug("Trying to switch to new source %s", source->name);
    /* Don't switch to any internal devices */
    s = pa_proplist_gets(source->proplist, PA_PROP_DEVICE_BUS);
    if (pa_safe_streq(s, "pci") || pa_safe_streq(s, "isa")) {
        pa_log_debug("Refusing to switch to source on %s bus", s);
        return PA_HOOK_OK;
    }
    /* Ignore sources matching the blacklist regex */
    if (u->blacklist && (pa_match(u->blacklist, source->name) > 0)) {
        pa_log_info("Refusing to switch to blacklisted source %s", source->name);
        return PA_HOOK_OK;
    }
    /* Ignore virtual sources if not configured otherwise on the command line */
    if (u->ignore_virtual && !(source->flags & PA_SOURCE_HARDWARE)) {
        pa_log_debug("Refusing to switch to virtual source");
        return PA_HOOK_OK;
    }
    /* No default source, nothing to move away, just set the new default */
    if (!c->default_source) {
        pa_core_set_configured_default_source(c, source->name);
        return PA_HOOK_OK;
    }
    if (c->default_source == source) {
        pa_log_debug("%s already is the default source", source->name);
        return PA_HOOK_OK;
    }
    if (u->only_from_unavailable)
        if (!c->default_source->active_port || c->default_source->active_port->available != PA_AVAILABLE_NO) {
            pa_log_debug("Current default source is available and module argument only_from_unavailable was set");
            return PA_HOOK_OK;
        }
    /* If the new source has a lower priority than the current default, refusing to switch */
    if (c->default_source->priority > source->priority)
    {
        pa_log_info("Refusing to switch to source %s with lower priority than current default source %s", source->name, c->default_source->name);
        return PA_HOOK_OK;
    }
    
    /* Actually do the switch to the new source */
    pa_core_set_configured_default_source(c, source->name);
    return PA_HOOK_OK;
}
```