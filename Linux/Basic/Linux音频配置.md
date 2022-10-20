# Linux音频配置

## alsa配置

### 初始化配置

**作用:** 用于alsa初始化，配置alsa控制器的初始值。使用`alsactl init`命令会调用到该配置。

**路径:** /usr/share/alsa/init/

**配置：**

(以Q281的声卡ES8316为例)

可使用`amixer`命令查看所有的控制器及其参数

1. 修改默认配置00main，添加自定义配置文件

`CARDINFO{driver}=="rockchip_es8316", INCLUDE="es8316", GOTO="init_end"`

2. 自定义配置es8316

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

### 声卡配置

**作用:** alsa声卡配置，绑定物理设备和pcm设备，或进行pcm设备的控制器配置。该配置会影响aplay -L和arecord -L的设备列表，以及pulseaudio的设备列表。

**路径:** /usr/share/alas/cards/

**配置:**

(以Q281的声卡ES8316为例)

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

**paths:** pulse设备路径映射表，一般不需要修改

**配置:**

1. 默认使用default.conf, 建议不要直接对default.conf操作，进行自定义配置
2. 自定义配置：

a. 修改/lib/udev/rules.d/90-pulseaudio.rules，添加自定义声卡的pid，vid以及配置文件名称

b. 在/usr/share/pulseaudio/alsa-mixer/profile-sets下添加配置文件

```shell
# example, 详细配置可参考default.conf
[Mapping analog-stereo]
device-strings = front:%f hw:%f
channel-map = left,right
paths-output = analog-output analog-output-speaker analog-output-desktop-speaker analog-output-headphones analog-output-headphones-2 analog-output-mono analog-output-lfe-on-mono
paths-input = analog-input-front-mic-cx analog-input-rear-mic-cx analog-input-mic-cx analog-input-linein-cx
priority = 10

# device-strings: PulseAudio绑定ALSA设备字符串，“%f”为卡号。ALSA设备字符串可用aplay -L和arecord -L进行查询。
# paths-output和path-input为conf设备路径
```
