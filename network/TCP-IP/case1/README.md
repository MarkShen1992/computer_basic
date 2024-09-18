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

todo...
