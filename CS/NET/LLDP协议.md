# LLDP

## 定义：

**链路层发现协议**（Link Layer Discovery Protocol，LLDP）是一种数据链路层协议，网络设备可以通过在本地网络中发送LLDPDU（Link Layer Discovery Protocol Data Unit）来通告其他设备自身的状态。是一种能够使网络中的设备互相发现并通告状态、交互信息的协议。

## 必备组件：

- snmpd
- lldpd 或 lldpad

## 启用服务：

`sudo /etc/init.d/snmpd start`

`sudo /etc/init.d/lldpd start` 或 `sudo /etc/init.d/lldpad start`

## 测试

```bash
sudo tcpdump -epni eth0 ether host 01:80:c2:00:00:0e
```

结果类似下方，说明LLDP数据包正常收发

```bash
#lldpd
17:00:41.554021 00:0b:00:26:bd:0e > 01:80:c2:00:00:0e, ethertype LLDP (0x88cc), length 212: LLDP, length 198: CC12365478963

#lldpad
17:00:46.173634 00:0b:00:26:bd:0e > 01:80:c2:00:00:0e, ethertype LLDP (0x88cc), length 202: LLDP, length 188: CC12365478963
```

```bash
sudo tcpdump -s0 -vv -epni eth0 ether dst 01:80:c2:00:00:0e
```

结果如下显示，可以看到报文的全部信息

```bash
#lldpd
18:34:10.640508 00:0b:00:26:bd:0e > 01:80:c2:00:00:0e, ethertype LLDP (0x88cc), length 212: LLDP, length 198
        Chassis ID TLV (1), length 7
          Subtype MAC address (4): 00:0b:00:26:bd:0e
          0x0000:  0400 0b00 26bd 0e
        Port ID TLV (2), length 7
          Subtype MAC address (3): 00:0b:00:26:bd:0e
          0x0000:  0300 0b00 26bd 0e
        Time to Live TLV (3), length 2: TTL 120s
          0x0000:  0078
        System Name TLV (5), length 13: CC12365478963
          0x0000:  4343 3132 3336 3534 3738 3936 33
        System Description TLV (6), length 109
          Ubuntu 14.04.4 LTS Linux 3.19.0-28-lowlatency #30~14.04.1-Ubuntu SMP PREEMPT Tue Sep 1 10:18:51 UTC 2015 i686
          0x0000:  5562 756e 7475 2031 342e 3034 2e34 204c
          0x0010:  5453 204c 696e 7578 2033 2e31 392e 302d
          0x0020:  3238 2d6c 6f77 6c61 7465 6e63 7920 2333
          0x0030:  307e 3134 2e30 342e 312d 5562 756e 7475
          0x0040:  2053 4d50 2050 5245 454d 5054 2054 7565
          0x0050:  2053 6570 2031 2031 303a 3138 3a35 3120
          0x0060:  5554 4320 3230 3135 2069 3638 36
        System Capabilities TLV (7), length 4
          System  Capabilities [Bridge, WLAN AP, Router] (0x001c)
          Enabled Capabilities [none] (0x0000)
          0x0000:  001c 0000
        Management Address TLV (8), length 12
          Management Address length 5, AFI IPv4 (1): 172.16.65.33
          Interface Index Interface Numbering (2): 2
          0x0000:  0501 ac10 4121 0200 0000 0200
        Port Description TLV (4), length 4: eth0
          0x0000:  6574 6830
        Organization specific TLV (127), length 9: OUI IEEE 802.3 Private (0x00120f)
          Link aggregation Subtype (3)
            aggregation status [supported], aggregation port ID 0
          0x0000:  0012 0f03 0100 0000 00
        Organization specific TLV (127), length 9: OUI IEEE 802.3 Private (0x00120f)
          MAC/PHY configuration/status Subtype (1)
            autonegotiation [supported, enabled] (0x03)
            PMD autoneg capability [10BASE-T hdx, 10BASE-T fdx, 100BASE-TX hdx, 100BASE-TX fdx, Pause for fdx links, Asym PAUSE for fdx, 1000BASE-T hdx, 1000BASE-T fdx] (0x6cc3)
            MAU type 100BASETX fdx (0x0010)
          0x0000:  0012 0f01 036c c300 10
        End TLV (0), length 0

#lldpad
18:20:32.613599 00:0b:00:26:bd:0e > 01:80:c2:00:00:0e, ethertype LLDP (0x88cc), length 202: LLDP, length 188
        Chassis ID TLV (1), length 7
          Subtype MAC address (4): 00:0b:00:26:bd:0e
          0x0000:  0400 0b00 26bd 0e
        Port ID TLV (2), length 7
          Subtype MAC address (3): 00:0b:00:26:bd:0e
          0x0000:  0300 0b00 26bd 0e
        Time to Live TLV (3), length 2: TTL 120s
          0x0000:  0078
        Port Description TLV (4), length 21: Interface   2 as eth0
          0x0000:  496e 7465 7266 6163 6520 2020 3220 6173
          0x0010:  2065 7468 30
        System Name TLV (5), length 13: CC12365478963
          0x0000:  4343 3132 3336 3534 3738 3936 33
        System Description TLV (6), length 104
          Linux CC12365478963 3.19.0-28-lowlatency #30~14.04.1-Ubuntu SMP PREEMPT Tue Sep 1 10:18:51 UTC 2015 i686
          0x0000:  4c69 6e75 7820 4343 3132 3336 3534 3738
          0x0010:  3936 3320 332e 3139 2e30 2d32 382d 6c6f
          0x0020:  776c 6174 656e 6379 2023 3330 7e31 342e
          0x0030:  3034 2e31 2d55 6275 6e74 7520 534d 5020
          0x0040:  5052 4545 4d50 5420 5475 6520 5365 7020
          0x0050:  3120 3130 3a31 383a 3531 2055 5443 2032
          0x0060:  3031 3520 6936 3836
        System Capabilities TLV (7), length 4
          System  Capabilities [Station Only] (0x0080)
          Enabled Capabilities [Station Only] (0x0080)
          0x0000:  0080 0080
        Management Address TLV (8), length 12
          Management Address length 5, AFI IPv4 (1): 172.16.65.33
          Interface Index Interface Numbering (2): 2
          0x0000:  0501 ac10 4121 0200 0000 0200
        End TLV (0), length 0
```

可看出，lldpd的报文长度为212，而lldpad的报文长度为202。System Description、Port Description报文格式和长度也不相同。
