# Wireshark 抓包实践

## 当访问一个微服务接口的时候发生的那些网络连接

> 在这个案例中，你可以学习到 TCP/IP 协议创建创建连接的三次挥手以及断开连接的四次挥手。

我来说下具体的操作步骤：

- 第一步：拿到 demo 代码，把代码打包成可执行的 jar 包

- 第二步：将 jar 包放到 Linux 服务器上，前提服务器上安装了 Java 8, 如果没有安装 Java 环境，可以参考 [在Linux系统上安装 Java 环境](https://markshen1992.top/2023/10/23/Java%E5%9C%A8Linux%E4%B8%8A%E5%AE%89%E8%A3%85/) 完成 Java 环境的安装

- 执行下面命令启动 demo 服务

```shell
java -jar demo*.jar
```

- 如果没有安装 tcpdump 命令的话，可以先执行下面的命令安装

```shell
yum install -y tcpdump
```

- 使用 tcpdump 来监听用户的请求，命令如下

```shell
tcpdump -i any -w demo.pcap host 192.168.0.52 and port 8080
```

> 因为我部署 Java 程序的机器是 `192.168.0.52`, 所以上面一句话的意思是监听机器 IP 地址是 `192.168.0.52` 这台机器，端口是 `8080`。将捕捉到的网络包内容写入到文件 `demo.pcap` 中。

- 接下来，我在机器 `192.168.0.50` 上访问接口，命令如下

```shell
curl 192.168.0.52:8080/hello?name=world
```

- 上面的命令执行完后，在 `192.168.0.52` 机器上执行 `ctrl + c` 按键结束 `tcpdump` 命令监听

- 然后，就可以把 `demo.pcap` 文件下载到 `windows` 中，使用 [wireshark](https://www.wireshark.org/) 工具来分析网络包了。

- 在 `case1` 目录中已经给出此次我抓包的文件，你可以基于此文件来学习 TCP/IP 建连接时的三次握手以及断开连接的四次挥手。

### 三次握手与四次挥手

说到三次握手，让我们看下着三次握手的报文信息：

- 第一次握手

```shell
Frame 1: 76 bytes on wire (608 bits), 76 bytes captured (608 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.461594000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.461594000 UTC
    Epoch Arrival Time: 1726642892.461594000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000000000 seconds]
    [Time delta from previous displayed frame: 0.000000000 seconds]
    [Time since reference or first frame: 0.000000000 seconds]
    Frame Number: 1
    Frame Length: 76 bytes (608 bits)
    Capture Length: 76 bytes (608 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp]
    [Coloring Rule Name: TCP SYN/FIN]
    [Coloring Rule String: tcp.flags & 0x02 || tcp.flags.fin == 1]
Linux cooked capture v1
    Packet type: Unicast to us (0)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_c2:89:0b (00:0c:29:c2:89:0b)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.50, Dst: 192.168.0.52
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 60
    Identification: 0x886d (34925)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0x3098 [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.50
    Destination Address: 192.168.0.52
    [Stream index: 0]
Transmission Control Protocol, Src Port: 58626, Dst Port: 8080, Seq: 0, Len: 0
    Source Port: 58626
    Destination Port: 8080
    [Stream index: 0]
    [Stream Packet Number: 1]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 0]
    Sequence Number: 0    (relative sequence number)
    Sequence Number (raw): 2169630313
    [Next Sequence Number: 1    (relative sequence number)]
    Acknowledgment Number: 0
    Acknowledgment number (raw): 0
    1010 .... = Header Length: 40 bytes (10)
    Flags: 0x002 (SYN)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...0 .... = Acknowledgment: Not set
        .... .... 0... = Push: Not set
        .... .... .0.. = Reset: Not set
        .... .... ..1. = Syn: Set
            [Expert Info (Chat/Sequence): Connection establish request (SYN): server port 8080]
                [Connection establish request (SYN): server port 8080]
                [Severity level: Chat]
                [Group: Sequence]
        .... .... ...0 = Fin: Not set
        [TCP Flags: ··········S·]
    Window: 29200
    [Calculated window size: 29200]
    Checksum: 0x79b2 [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (20 bytes), Maximum segment size, SACK permitted, Timestamps, No-Operation (NOP), Window scale
        TCP Option - Maximum segment size: 1460 bytes
            Kind: Maximum Segment Size (2)
            Length: 4
            MSS Value: 1460
        TCP Option - SACK permitted
            Kind: SACK Permitted (4)
            Length: 2
        TCP Option - Timestamps: TSval 3433988, TSecr 0
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3433988
            Timestamp echo reply: 0
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Window scale: 7 (multiply by 128)
            Kind: Window Scale (3)
            Length: 3
            Shift count: 7
            [Multiplier: 128]
    [Timestamps]
        [Time since first frame in this TCP stream: 0.000000000 seconds]
        [Time since previous frame in this TCP stream: 0.000000000 seconds]
```

- **客户端**发送一个带有**SYN（Synchronize Sequence Number，同步序列号）标志**的报文段，表明它希望与服务器建立连接。

- 报文中会包含客户端生成的**初始序列号（ISN, Initial Sequence Number）**，用于数据传输的序列号同步。

- 这个SYN报文段发送之后，客户端进入**SYN-SENT**状态，等待服务器的响应。
  
  **报文格式：**
  
  - 标志位：SYN=1
  - 序列号：Client ISN（初始序列号）
  
  **目的**：
  
  - 客户端通知服务器，它想建立连接。
  - 客户端发送了一个初始的序列号供后续通信使用。

- 第二次握手

```shell
Frame 2: 76 bytes on wire (608 bits), 76 bytes captured (608 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.461716000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.461716000 UTC
    Epoch Arrival Time: 1726642892.461716000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000122000 seconds]
    [Time delta from previous displayed frame: 0.000122000 seconds]
    [Time since reference or first frame: 0.000122000 seconds]
    Frame Number: 2
    Frame Length: 76 bytes (608 bits)
    Capture Length: 76 bytes (608 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp]
    [Coloring Rule Name: TCP SYN/FIN]
    [Coloring Rule String: tcp.flags & 0x02 || tcp.flags.fin == 1]
Linux cooked capture v1
    Packet type: Sent by us (4)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_e4:79:a0 (00:0c:29:e4:79:a0)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.52, Dst: 192.168.0.50
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 60
    Identification: 0x0000 (0)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0xb905 [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.52
    Destination Address: 192.168.0.50
    [Stream index: 0]
Transmission Control Protocol, Src Port: 8080, Dst Port: 58626, Seq: 0, Ack: 1, Len: 0
    Source Port: 8080
    Destination Port: 58626
    [Stream index: 0]
    [Stream Packet Number: 2]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 0]
    Sequence Number: 0    (relative sequence number)
    Sequence Number (raw): 828247081
    [Next Sequence Number: 1    (relative sequence number)]
    Acknowledgment Number: 1    (relative ack number)
    Acknowledgment number (raw): 2169630314
    1010 .... = Header Length: 40 bytes (10)
    Flags: 0x012 (SYN, ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 0... = Push: Not set
        .... .... .0.. = Reset: Not set
        .... .... ..1. = Syn: Set
            [Expert Info (Chat/Sequence): Connection establish acknowledge (SYN+ACK): server port 8080]
                [Connection establish acknowledge (SYN+ACK): server port 8080]
                [Severity level: Chat]
                [Group: Sequence]
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······A··S·]
    Window: 28960
    [Calculated window size: 28960]
    Checksum: 0x81e5 [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (20 bytes), Maximum segment size, SACK permitted, Timestamps, No-Operation (NOP), Window scale
        TCP Option - Maximum segment size: 1460 bytes
            Kind: Maximum Segment Size (2)
            Length: 4
            MSS Value: 1460
        TCP Option - SACK permitted
            Kind: SACK Permitted (4)
            Length: 2
        TCP Option - Timestamps: TSval 3803829, TSecr 3433988
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3803829
            Timestamp echo reply: 3433988
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Window scale: 7 (multiply by 128)
            Kind: Window Scale (3)
            Length: 3
            Shift count: 7
            [Multiplier: 128]
    [Timestamps]
        [Time since first frame in this TCP stream: 0.000122000 seconds]
        [Time since previous frame in this TCP stream: 0.000122000 seconds]
    [SEQ/ACK analysis]
        [This is an ACK to the segment in frame: 1]
        [The RTT to ACK the segment was: 0.000122000 seconds]
        [iRTT: 0.000438000 seconds]
```

- **服务器**收到客户端的SYN请求后，确认同意连接，发送一个**SYN-ACK**（同步-确认）报文段：
  
  - SYN标志：服务器也发送自己的初始序列号（Server ISN）。
  - ACK标志：确认客户端的SYN报文，表明服务器已成功接收到客户端的初始序列号。ACK值是**Client ISN + 1**。

- 服务器发送SYN-ACK报文段之后，进入**SYN-RECEIVED**状态。
  
  **报文格式：**
  
  - 标志位：SYN=1，ACK=1
  - 序列号：Server ISN（服务器的初始序列号）
  - 确认号：Client ISN + 1
  
  **目的**：
  
  - 服务器同意与客户端建立连接，并发送了自己的初始序列号。
  - 确认已经接收到了客户端的初始序列号。

- 第三次握手

```shell
Frame 3: 68 bytes on wire (544 bits), 68 bytes captured (544 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.462032000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.462032000 UTC
    Epoch Arrival Time: 1726642892.462032000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000316000 seconds]
    [Time delta from previous displayed frame: 0.000316000 seconds]
    [Time since reference or first frame: 0.000438000 seconds]
    Frame Number: 3
    Frame Length: 68 bytes (544 bits)
    Capture Length: 68 bytes (544 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp]
    [Coloring Rule Name: TCP]
    [Coloring Rule String: tcp]
Linux cooked capture v1
    Packet type: Unicast to us (0)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_c2:89:0b (00:0c:29:c2:89:0b)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.50, Dst: 192.168.0.52
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 52
    Identification: 0x886e (34926)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0x309f [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.50
    Destination Address: 192.168.0.52
    [Stream index: 0]
Transmission Control Protocol, Src Port: 58626, Dst Port: 8080, Seq: 1, Ack: 1, Len: 0
    Source Port: 58626
    Destination Port: 8080
    [Stream index: 0]
    [Stream Packet Number: 3]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 0]
    Sequence Number: 1    (relative sequence number)
    Sequence Number (raw): 2169630314
    [Next Sequence Number: 1    (relative sequence number)]
    Acknowledgment Number: 1    (relative ack number)
    Acknowledgment number (raw): 828247082
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x010 (ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 0... = Push: Not set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······A····]
    Window: 229
    [Calculated window size: 29312]
    [Window size scaling factor: 128]
    Checksum: 0xd121 [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 3433989, TSecr 3803829
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3433989
            Timestamp echo reply: 3803829
    [Timestamps]
        [Time since first frame in this TCP stream: 0.000438000 seconds]
        [Time since previous frame in this TCP stream: 0.000316000 seconds]
    [SEQ/ACK analysis]
        [This is an ACK to the segment in frame: 2]
        [The RTT to ACK the segment was: 0.000316000 seconds]
        [iRTT: 0.000438000 seconds]
```

- **客户端**收到服务器的SYN-ACK报文后，发送一个**ACK**（确认）报文段给服务器：
  
  - ACK标志：确认服务器的SYN报文，表示已成功接收到服务器的初始序列号。ACK值是**Server ISN + 1**。

- 客户端发送ACK报文段之后，进入**ESTABLISHED**状态，表示连接已经建立。

- 服务器收到这个ACK报文段后，也进入**ESTABLISHED**状态，表示连接已经建立，可以开始进行数据传输。
  
  **报文格式：**
  
  - 标志位：ACK=1
  - 序列号：Client ISN + 1
  - 确认号：Server ISN + 1
  
  **目的**：
  
  - 客户端确认收到了服务器的初始序列号，连接正式建立。
  - 双方都可以开始进行数据传输。

三次后 `TCP/IP` 已经完成连接的建立，接下来要做的事情就是请求接口，返回数据。

```shell
Frame 4: 165 bytes on wire (1320 bits), 165 bytes captured (1320 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.462094000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.462094000 UTC
    Epoch Arrival Time: 1726642892.462094000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000062000 seconds]
    [Time delta from previous displayed frame: 0.000062000 seconds]
    [Time since reference or first frame: 0.000500000 seconds]
    Frame Number: 4
    Frame Length: 165 bytes (1320 bits)
    Capture Length: 165 bytes (1320 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp:http]
    [Coloring Rule Name: HTTP]
    [Coloring Rule String: http || tcp.port == 80 || http2]
Linux cooked capture v1
    Packet type: Unicast to us (0)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_c2:89:0b (00:0c:29:c2:89:0b)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.50, Dst: 192.168.0.52
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 149
    Identification: 0x886f (34927)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0x303d [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.50
    Destination Address: 192.168.0.52
    [Stream index: 0]
Transmission Control Protocol, Src Port: 58626, Dst Port: 8080, Seq: 1, Ack: 1, Len: 97
    Source Port: 58626
    Destination Port: 8080
    [Stream index: 0]
    [Stream Packet Number: 4]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 97]
    Sequence Number: 1    (relative sequence number)
    Sequence Number (raw): 2169630314
    [Next Sequence Number: 98    (relative sequence number)]
    Acknowledgment Number: 1    (relative ack number)
    Acknowledgment number (raw): 828247082
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x018 (PSH, ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 1... = Push: Set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······AP···]
    Window: 229
    [Calculated window size: 29312]
    [Window size scaling factor: 128]
    Checksum: 0xd4cd [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 3433989, TSecr 3803829
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3433989
            Timestamp echo reply: 3803829
    [Timestamps]
        [Time since first frame in this TCP stream: 0.000500000 seconds]
        [Time since previous frame in this TCP stream: 0.000062000 seconds]
    [SEQ/ACK analysis]
        [iRTT: 0.000438000 seconds]
        [Bytes in flight: 97]
        [Bytes sent since last PSH flag: 97]
    TCP payload (97 bytes)
Hypertext Transfer Protocol
    GET /hello?name=world HTTP/1.1\r\n
        Request Method: GET
        Request URI: /hello?name=world
            Request URI Path: /hello
            Request URI Query: name=world
                Request URI Query Parameter: name=world
        Request Version: HTTP/1.1
    User-Agent: curl/7.29.0\r\n
    Host: 192.168.0.52:8080\r\n
    Accept: */*\r\n
    \r\n
    [Response in frame: 6]
    [Full request URI: http://192.168.0.52:8080/hello?name=world]
```

```shell
Frame 5: 68 bytes on wire (544 bits), 68 bytes captured (544 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.462102000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.462102000 UTC
    Epoch Arrival Time: 1726642892.462102000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000008000 seconds]
    [Time delta from previous displayed frame: 0.000008000 seconds]
    [Time since reference or first frame: 0.000508000 seconds]
    Frame Number: 5
    Frame Length: 68 bytes (544 bits)
    Capture Length: 68 bytes (544 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp]
    [Coloring Rule Name: TCP]
    [Coloring Rule String: tcp]
Linux cooked capture v1
    Packet type: Sent by us (4)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_e4:79:a0 (00:0c:29:e4:79:a0)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.52, Dst: 192.168.0.50
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 52
    Identification: 0x069f (1695)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0xb26e [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.52
    Destination Address: 192.168.0.50
    [Stream index: 0]
Transmission Control Protocol, Src Port: 8080, Dst Port: 58626, Seq: 1, Ack: 98, Len: 0
    Source Port: 8080
    Destination Port: 58626
    [Stream index: 0]
    [Stream Packet Number: 5]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 0]
    Sequence Number: 1    (relative sequence number)
    Sequence Number (raw): 828247082
    [Next Sequence Number: 1    (relative sequence number)]
    Acknowledgment Number: 98    (relative ack number)
    Acknowledgment number (raw): 2169630411
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x010 (ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 0... = Push: Not set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······A····]
    Window: 227
    [Calculated window size: 29056]
    [Window size scaling factor: 128]
    Checksum: 0x81dd [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 3803829, TSecr 3433989
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3803829
            Timestamp echo reply: 3433989
    [Timestamps]
        [Time since first frame in this TCP stream: 0.000508000 seconds]
        [Time since previous frame in this TCP stream: 0.000008000 seconds]
    [SEQ/ACK analysis]
        [This is an ACK to the segment in frame: 4]
        [The RTT to ACK the segment was: 0.000008000 seconds]
        [iRTT: 0.000438000 seconds]
```

```shell
Frame 6: 194 bytes on wire (1552 bits), 194 bytes captured (1552 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.464705000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.464705000 UTC
    Epoch Arrival Time: 1726642892.464705000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.002603000 seconds]
    [Time delta from previous displayed frame: 0.002603000 seconds]
    [Time since reference or first frame: 0.003111000 seconds]
    Frame Number: 6
    Frame Length: 194 bytes (1552 bits)
    Capture Length: 194 bytes (1552 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp:http:data-text-lines]
    [Coloring Rule Name: HTTP]
    [Coloring Rule String: http || tcp.port == 80 || http2]
Linux cooked capture v1
    Packet type: Sent by us (4)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_e4:79:a0 (00:0c:29:e4:79:a0)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.52, Dst: 192.168.0.50
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 178
    Identification: 0x06a0 (1696)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0xb1ef [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.52
    Destination Address: 192.168.0.50
    [Stream index: 0]
Transmission Control Protocol, Src Port: 8080, Dst Port: 58626, Seq: 1, Ack: 98, Len: 126
    Source Port: 8080
    Destination Port: 58626
    [Stream index: 0]
    [Stream Packet Number: 6]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 126]
    Sequence Number: 1    (relative sequence number)
    Sequence Number (raw): 828247082
    [Next Sequence Number: 127    (relative sequence number)]
    Acknowledgment Number: 98    (relative ack number)
    Acknowledgment number (raw): 2169630411
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x018 (PSH, ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 1... = Push: Set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······AP···]
    Window: 227
    [Calculated window size: 29056]
    [Window size scaling factor: 128]
    Checksum: 0x825b [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 3803832, TSecr 3433989
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3803832
            Timestamp echo reply: 3433989
    [Timestamps]
        [Time since first frame in this TCP stream: 0.003111000 seconds]
        [Time since previous frame in this TCP stream: 0.002603000 seconds]
    [SEQ/ACK analysis]
        [iRTT: 0.000438000 seconds]
        [Bytes in flight: 126]
        [Bytes sent since last PSH flag: 126]
    TCP payload (126 bytes)
Hypertext Transfer Protocol
    HTTP/1.1 200 \r\n
        Response Version: HTTP/1.1
        Status Code: 200
        [Status Code Description: OK]
    Content-Type: text/plain;charset=UTF-8\r\n
    Content-Length: 12\r\n
        [Content length: 12]
    Date: Wed, 18 Sep 2024 07:01:32 GMT\r\n
    \r\n
    [Request in frame: 4]
    [Time since request: 0.002611000 seconds]
    [Request URI: /hello?name=world]
    [Full request URI: http://192.168.0.52:8080/hello?name=world]
    File Data: 12 bytes
Line-based text data: text/plain (1 lines)
    hello, world
```

- `TCP/IP` 断开连接的时候四次挥手
  
  - 第一次挥手

```shell
Frame 7: 68 bytes on wire (544 bits), 68 bytes captured (544 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.464966000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.464966000 UTC
    Epoch Arrival Time: 1726642892.464966000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000261000 seconds]
    [Time delta from previous displayed frame: 0.000261000 seconds]
    [Time since reference or first frame: 0.003372000 seconds]
    Frame Number: 7
    Frame Length: 68 bytes (544 bits)
    Capture Length: 68 bytes (544 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp]
    [Coloring Rule Name: TCP]
    [Coloring Rule String: tcp]
Linux cooked capture v1
    Packet type: Unicast to us (0)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_c2:89:0b (00:0c:29:c2:89:0b)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.50, Dst: 192.168.0.52
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 52
    Identification: 0x8870 (34928)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0x309d [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.50
    Destination Address: 192.168.0.52
    [Stream index: 0]
Transmission Control Protocol, Src Port: 58626, Dst Port: 8080, Seq: 98, Ack: 127, Len: 0
    Source Port: 58626
    Destination Port: 8080
    [Stream index: 0]
    [Stream Packet Number: 7]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 0]
    Sequence Number: 98    (relative sequence number)
    Sequence Number (raw): 2169630411
    [Next Sequence Number: 98    (relative sequence number)]
    Acknowledgment Number: 127    (relative ack number)
    Acknowledgment number (raw): 828247208
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x010 (ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 0... = Push: Not set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······A····]
    Window: 229
    [Calculated window size: 29312]
    [Window size scaling factor: 128]
    Checksum: 0xd03c [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 3433992, TSecr 3803832
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3433992
            Timestamp echo reply: 3803832
    [Timestamps]
        [Time since first frame in this TCP stream: 0.003372000 seconds]
        [Time since previous frame in this TCP stream: 0.000261000 seconds]
    [SEQ/ACK analysis]
        [This is an ACK to the segment in frame: 6]
        [The RTT to ACK the segment was: 0.000261000 seconds]
        [iRTT: 0.000438000 seconds]
```

- **主动关闭连接的一方**（例如客户端）发送一个**FIN**（结束）报文段，表示它已经完成数据发送，并希望关闭客户端到服务器的单向数据通道。

- 此时，客户端进入**FIN-WAIT-1**状态，等待服务器的确认。
  
  **报文格式：**
  
  - 标志位：FIN=1
  - 序列号：客户端的当前序列号
  
  **目的**：
  
  - 客户端通知服务器，它已经不再发送数据，要求关闭从客户端到服务器的数据通道。

- 第二次挥手

```shell
Frame 8: 68 bytes on wire (544 bits), 68 bytes captured (544 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.465122000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.465122000 UTC
    Epoch Arrival Time: 1726642892.465122000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000156000 seconds]
    [Time delta from previous displayed frame: 0.000156000 seconds]
    [Time since reference or first frame: 0.003528000 seconds]
    Frame Number: 8
    Frame Length: 68 bytes (544 bits)
    Capture Length: 68 bytes (544 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp]
    [Coloring Rule Name: TCP SYN/FIN]
    [Coloring Rule String: tcp.flags & 0x02 || tcp.flags.fin == 1]
Linux cooked capture v1
    Packet type: Unicast to us (0)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_c2:89:0b (00:0c:29:c2:89:0b)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.50, Dst: 192.168.0.52
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 52
    Identification: 0x8871 (34929)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0x309c [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.50
    Destination Address: 192.168.0.52
    [Stream index: 0]
Transmission Control Protocol, Src Port: 58626, Dst Port: 8080, Seq: 98, Ack: 127, Len: 0
    Source Port: 58626
    Destination Port: 8080
    [Stream index: 0]
    [Stream Packet Number: 8]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 0]
    Sequence Number: 98    (relative sequence number)
    Sequence Number (raw): 2169630411
    [Next Sequence Number: 99    (relative sequence number)]
    Acknowledgment Number: 127    (relative ack number)
    Acknowledgment number (raw): 828247208
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x011 (FIN, ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 0... = Push: Not set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...1 = Fin: Set
            [Expert Info (Chat/Sequence): Connection finish (FIN)]
                [Connection finish (FIN)]
                [Severity level: Chat]
                [Group: Sequence]
        [TCP Flags: ·······A···F]
            [Expert Info (Note/Sequence): This frame initiates the connection closing]
                [This frame initiates the connection closing]
                [Severity level: Note]
                [Group: Sequence]
    Window: 229
    [Calculated window size: 29312]
    [Window size scaling factor: 128]
    Checksum: 0xd03b [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 3433992, TSecr 3803832
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3433992
            Timestamp echo reply: 3803832
    [Timestamps]
        [Time since first frame in this TCP stream: 0.003528000 seconds]
        [Time since previous frame in this TCP stream: 0.000156000 seconds]
```

- **服务器**收到客户端的FIN报文段后，发送一个**ACK**报文段来确认客户端的请求。ACK的值是**客户端序列号+1**。

- 服务器此时仍然可以继续向客户端发送数据，单向通道未关闭。

- 服务器发送ACK报文段后，进入**CLOSE-WAIT**状态，而客户端则进入**FIN-WAIT-2**状态，等待服务器发送自己的FIN报文。
  
  **报文格式：**
  
  - 标志位：ACK=1
  - 确认号：客户端的序列号 + 1
  
  **目的**：
  
  - 服务器确认已经接收到了客户端的FIN报文。
  - 客户端到服务器的单向通道关闭，但服务器到客户端的通道仍然保持开放。

- 第三次挥手

```shell
Frame 9: 68 bytes on wire (544 bits), 68 bytes captured (544 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.465519000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.465519000 UTC
    Epoch Arrival Time: 1726642892.465519000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000397000 seconds]
    [Time delta from previous displayed frame: 0.000397000 seconds]
    [Time since reference or first frame: 0.003925000 seconds]
    Frame Number: 9
    Frame Length: 68 bytes (544 bits)
    Capture Length: 68 bytes (544 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp]
    [Coloring Rule Name: TCP SYN/FIN]
    [Coloring Rule String: tcp.flags & 0x02 || tcp.flags.fin == 1]
Linux cooked capture v1
    Packet type: Sent by us (4)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_e4:79:a0 (00:0c:29:e4:79:a0)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.52, Dst: 192.168.0.50
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 52
    Identification: 0x06a1 (1697)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0xb26c [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.52
    Destination Address: 192.168.0.50
    [Stream index: 0]
Transmission Control Protocol, Src Port: 8080, Dst Port: 58626, Seq: 127, Ack: 99, Len: 0
    Source Port: 8080
    Destination Port: 58626
    [Stream index: 0]
    [Stream Packet Number: 9]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 0]
    Sequence Number: 127    (relative sequence number)
    Sequence Number (raw): 828247208
    [Next Sequence Number: 128    (relative sequence number)]
    Acknowledgment Number: 99    (relative ack number)
    Acknowledgment number (raw): 2169630412
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x011 (FIN, ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 0... = Push: Not set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...1 = Fin: Set
            [Expert Info (Chat/Sequence): Connection finish (FIN)]
                [Connection finish (FIN)]
                [Severity level: Chat]
                [Group: Sequence]
        [TCP Flags: ·······A···F]
            [Expert Info (Note/Sequence): This frame undergoes the connection closing]
                [This frame undergoes the connection closing]
                [Severity level: Note]
                [Group: Sequence]
    Window: 227
    [Calculated window size: 29056]
    [Window size scaling factor: 128]
    Checksum: 0x81dd [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 3803832, TSecr 3433992
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3803832
            Timestamp echo reply: 3433992
    [Timestamps]
        [Time since first frame in this TCP stream: 0.003925000 seconds]
        [Time since previous frame in this TCP stream: 0.000397000 seconds]
    [SEQ/ACK analysis]
        [This is an ACK to the segment in frame: 8]
        [The RTT to ACK the segment was: 0.000397000 seconds]
        [iRTT: 0.000438000 seconds]
```

- **服务器**在准备好断开连接后，发送一个**FIN**报文段给客户端，表示它也不再需要发送数据，要求关闭服务器到客户端的单向通道。

- 服务器发送FIN报文段后，进入**LAST-ACK**状态，等待客户端的确认。
  
  **报文格式：**
  
  - 标志位：FIN=1
  - 序列号：服务器的当前序列号
  
  **目的**：
  
  - 服务器通知客户端，它已经完成了数据发送，并要求关闭服务器到客户端的通道。

- 第四次挥手

```shell
Frame 10: 68 bytes on wire (544 bits), 68 bytes captured (544 bits)
    Encapsulation type: Linux cooked-mode capture v1 (25)
    Arrival Time: Sep 18, 2024 15:01:32.465734000 中国标准时间
    UTC Arrival Time: Sep 18, 2024 07:01:32.465734000 UTC
    Epoch Arrival Time: 1726642892.465734000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 0.000215000 seconds]
    [Time delta from previous displayed frame: 0.000215000 seconds]
    [Time since reference or first frame: 0.004140000 seconds]
    Frame Number: 10
    Frame Length: 68 bytes (544 bits)
    Capture Length: 68 bytes (544 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: sll:ethertype:ip:tcp]
    [Coloring Rule Name: TCP]
    [Coloring Rule String: tcp]
Linux cooked capture v1
    Packet type: Unicast to us (0)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: VMware_c2:89:0b (00:0c:29:c2:89:0b)
    Unused: 0000
    Protocol: IPv4 (0x0800)
Internet Protocol Version 4, Src: 192.168.0.50, Dst: 192.168.0.52
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 52
    Identification: 0x8872 (34930)
    010. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0x309b [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.0.50
    Destination Address: 192.168.0.52
    [Stream index: 0]
Transmission Control Protocol, Src Port: 58626, Dst Port: 8080, Seq: 99, Ack: 128, Len: 0
    Source Port: 58626
    Destination Port: 8080
    [Stream index: 0]
    [Stream Packet Number: 10]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 0]
    Sequence Number: 99    (relative sequence number)
    Sequence Number (raw): 2169630412
    [Next Sequence Number: 99    (relative sequence number)]
    Acknowledgment Number: 128    (relative ack number)
    Acknowledgment number (raw): 828247209
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x010 (ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 0... = Push: Not set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······A····]
    Window: 229
    [Calculated window size: 29312]
    [Window size scaling factor: 128]
    Checksum: 0xd039 [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 3433993, TSecr 3803832
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 3433993
            Timestamp echo reply: 3803832
    [Timestamps]
        [Time since first frame in this TCP stream: 0.004140000 seconds]
        [Time since previous frame in this TCP stream: 0.000215000 seconds]
    [SEQ/ACK analysis]
        [This is an ACK to the segment in frame: 9]
        [The RTT to ACK the segment was: 0.000215000 seconds]
        [iRTT: 0.000438000 seconds]
```

- **客户端**收到服务器的FIN报文段后，发送一个**ACK**报文段来确认断开请求。ACK的值是**服务器序列号+1**。

- 客户端发送完ACK报文段后，进入**TIME-WAIT**状态，等待足够的时间确保服务器收到ACK，然后进入**CLOSED**状态，正式断开连接。

- **服务器**收到ACK报文后，立即进入**CLOSED**状态，释放连接资源。
  
  **报文格式：**
  
  - 标志位：ACK=1
  - 确认号：服务器的序列号 + 1
  
  **目的**：
  
  - 客户端确认收到了服务器的FIN报文，至此双方的连接完全关闭。



## TCP 参数

- `Seq`：表示该数据段的序号，用于对收到的数据段重拍序。需要理解 `Seq` 的增长方式。
  
  - `Seq` 号的大小是根据上一个数据段的 `Seq`号和长度相加而来的

- `Len`：该数据段的长度。如果 `Len = 0` 的话，其实是有 `TCP` 头的。不要以为 `Len = 0` 是没有意义的。

- `Ack`：确认号，接收方向发送法确认已经收到了哪些字节。
  
  - 比如，甲发送了 `Seq: x Len: y` 的数据段给了乙，那么乙回复的确认号就是 `x + y` ，这意味着它收到了 `x + y` 之前的所有字节。



## TCP 头附带的标志位

- `SYN`：表示正在**发起连接请求**。因为连接是双向的，所以建立连接的时候，双方都要发送一个 `SYN`。

- `FIN`：携带这个标志的包表示正在**请求终止连接**。因为连接是双向的，所以彻底关闭一个连接的时候，双方都要发一个 `FIN`。

- `RST`：用于**重置一个混乱的连接**，或者**拒绝一个无效的请求**。



## 一个标准建立连接的过程

在这里推荐两篇大神陈皓(@haoel)的文章:

- [《TCP的那些事儿·上》](https://coolshell.org/articles/11564.html/)

- [《TCP的那些事儿·下》](https://coolshell.org/articles/11609.html/)



- TCP 的三次握手

![](./tcp_open_close.jpg)

- [Two General's Problem]([https://zh.wikipedia.org/wiki/%E4%B8%A4%E5%86%9B%E9%97%AE%E9%A2%98)



- TCP 断链接的四次挥手

![](./tcpclosesimul.png)
