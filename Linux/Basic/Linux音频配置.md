## alsa配置

### init配置

**作用:** 用于alsa初始化，配置alsa控制器的初始值。使用`alsactl init`命令会调用到该配置。

**路径:** /usr/share/alsa/init/

**配置：**

以Q281的声卡ES8316为例

可使用`amixer`命令查看所有的控制器及其参数

1. 修改默认配置00main，添加自定义配置文件

`CARDINFO{driver}=="rockchip_es8316", INCLUDE="es8316", GOTO="init_end"`
CARDINFO{driver}为声卡名称，
INCLUDE为指向的配置文件
2. 自定义配置es8316，可参考同路径的hda进行配置文件编写

```shell
# Configuration for es8316 driver

LABEL="rockchip_es8316"
#playback
CTL{reset}="mixer"
CTL{name}="Playback Polarity", CTL{values}="Normal"
CTL{name}="Capture Polarity", CTL{values}="Normal"
CTL{name}="ADC Double FS Mode", CTL{values}="false"
CTL{name}="ADC Soft Ramp", CTL{values}="true"
CTL{name}="ALC Capture Attack Time", CTL{values}="5"
CTL{name}="ALC Capture Decay Time", CTL{values}="0"
CTL{name}="ALC Capture Function", CTL{values}="On"
CTL{name}="ALC Capture Hold Time", CTL{values}="0"
CTL{name}="ALC Capture Max PGA", CTL{values}="13"
CTL{name}="ALC Capture Min PGA", CTL{values}="8"
CTL{name}="ALC Capture NG Switch", CTL{values}="true"
CTL{name}="ALC Capture NG Threshold", CTL{values}="1"
CTL{name}="ALC Capture NG Type", CTL{values}="Mute ADC Output"
CTL{name}="ALC Capture Target Volume", CTL{values}="10"
CTL{name}="DAC Playback Volume", CTL{values}="87%"
CTL{name}="DAC Double Fs Mode", CTL{values}="false"
CTL{name}="DAC Notch Filter", CTL{values}="false"
CTL{name}="DAC SRC Mux", CTL{values}="0"
CTL{name}="DAC Soft Ramp Rate", CTL{values}="0"
CTL{name}="DAC Stereo Enhancement", CTL{values}="0"
CTL{name}="DAC Volume Control-LeR", CTL{values}="0"
CTL{name}="Differential Mux", CTL{values}="lin2-rin2 with 20db Boost"
CTL{name}="Digital Mic Mux", CTL{values}="dmic disable"
CTL{name}="Enable DAC Soft Ramp", CTL{values}="true"
CTL{name}="HP Playback Volume", CTL{values}="0,0"
CTL{name}="HPMixer Gain", CTL{values}="3,3"
CTL{name}="Input PGA", CTL{values}="6"
CTL{name}="LLIN Switch", CTL{values}="false"
CTL{name}="Left DAC Switch", CTL{values}="true"
CTL{name}="Left Hp mux", CTL{values}="lin1-rin1"
CTL{name}="MIC Boost", CTL{values}="true"
CTL{name}="RLIN Switch", CTL{values}="false"
CTL{name}="Right DAC Switch", CTL{values}="true"
CTL{name}="Right Hp mux", CTL{values}="0"
#capture
CTL{name}="ADC Capture Volume", CTL{values}="77%"
CTL{name}="ALC Capture Attack Time", CTL{values}="5"
CTL{name}="ALC Capture Decay Time", CTL{values}="0"
CTL{name}="ALC Capture Hold Time", CTL{values}="0"
CTL{name}="ALC Capture Max PGA", CTL{values}="13"
CTL{name}="ALC Capture Min PGA", CTL{values}="8"
CTL{name}="ALC Capture NG Threshold", CTL{values}="1"
CTL{name}="ALC Capture Target Volume", CTL{values}="10"
CTL{name}="DAC Soft Ramp Rate", CTL{values}="0"
CTL{name}="DAC Stereo Enhancement", CTL{values}="0"
CTL{name}="DAC Volume Control-LeR", CTL{values}="0"
CTL{name}="HPMixer Gain", CTL{index}="0", CTL{values}="3,3"
CTL{name}="Input PGA", CTL{values}="6"
RESULT="true", EXIT="return"
```
如果想在一个配置中配置多个声卡，已hda为例：

### cards配置

**作用:** alsa声卡配置，绑定物理设备和pcm设备，或进行pcm设备的控制器配置。该配置会影响aplay -L和arecord -L的设备列表，以及pulseaudio的设备列表。

**路径:** /usr/share/alas/cards/

**配置:**

以Q281的声卡ES8316为例

1. 修改默认配置aliases.conf，添加自定义声卡配置文件

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
ATTRS{subsystem_vendor}=="0x8086", ATTRS{subsystem_device}=="0x7270", ENV{PULSE_PROFILE_SET}="ht5300-alc897.conf"
GOTO="pulseaudio_end"
LABEL="pulseaudio_firewire_quirk"
LABEL="pulseaudio_end"
```

b. 在/usr/share/pulseaudio/alsa-mixer/profile-sets下添加配置文件

```shell
# example, 详细配置可参考default.conf
# itep HT5300 ALC897 Sound Card Config
[General]
auto-profiles = yes
[Mapping analog-stereo]
device-strings = front:%f
channel-map = left,right
paths-output = analog-output analog-output-lineout analog-output-speaker analog-output-headphones analog-output-headphones-2
paths-input = analog-input-front-mic analog-input-rear-mic analog-input-internal-mic analog-input-dock-mic analog-input analog-input-mic analog-input-linein analog-input-aux analog-input-video analog-input-tvtuner analog-input-fm analog-input-mic-line analog-input-headphone-mic analog-input-headset-mic
priority = 15
[Mapping hdmi-stereo]
description = Digital Stereo (HDMI)
device-strings = hdmi:%f
paths-output = hdmi-output-0
channel-map = left,right
priority = 9
direction = output
[Mapping hdmi-surround]
description = Digital Surround 5.1 (HDMI)
device-strings = hdmi:%f
paths-output = hdmi-output-0
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe
priority = 8
direction = output
[Mapping hdmi-surround71]
description = Digital Surround 7.1 (HDMI)
device-strings = hdmi:%f
paths-output = hdmi-output-0
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe,side-left,side-right
priority = 8
direction = output
[Mapping hdmi-dts-surround]
description = Digital Surround 5.1 (HDMI/DTS)
device-strings = dcahdmi:%f
paths-output = hdmi-output-0
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe
priority = 6
direction = output
[Mapping hdmi-stereo-extra1]
description = Digital Stereo (HDMI 2)
device-strings = hdmi:%f,1
paths-output = hdmi-output-1
channel-map = left,right
priority = 7
direction = output
[Mapping hdmi-surround-extra1]
description = Digital Surround 5.1 (HDMI 2)
device-strings = hdmi:%f,1
paths-output = hdmi-output-1
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe
priority = 6
direction = output
[Mapping hdmi-surround71-extra1]
description = Digital Surround 7.1 (HDMI 2)
device-strings = hdmi:%f,1
paths-output = hdmi-output-1
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe,side-left,side-right
priority = 6
direction = output
[Mapping hdmi-dts-surround-extra1]
description = Digital Surround 5.1 (HDMI 2/DTS)
device-strings = dcahdmi:%f,1
paths-output = hdmi-output-1
channel-map = front-left,front-right,rear-left,rear-right,front-center,lfe
priority = 6
direction = output

# device-strings: PulseAudio绑定ALSA设备字符串，“%f”为卡号。ALSA设备字符串可用aplay -L和arecord -L进行查询。
# paths-output和path-input为conf设备路径
```

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
