操作系统和网络

[TOC]

# MAC 地址

## 1. 什么是MAC地址
> MAC地址（英语：Media Access Control Address），直译为媒体存取控制位址，也称为局域网地址（LAN Address），MAC位址，以太网地址（Ethernet Address）或物理地址（Physical Address），它是一个用来确认网络设备位置的位址。在OSI模型中，第三层网络层负责IP地址，第二层数据链路层则负责MAC位址 。MAC地址用于在网络中唯一标示一个网卡，一台设备若有一或多个网卡，则每个网卡都需要并会有一个唯一的MAC地址。


## 2. Linux下查看MAC地址方法

### 2.1 `ifconfig -a` 其中 HWaddr字段就是MAC地址
这是最常用的方式
```shell
[root@node7133 ~]# ifconfig -a
enp3s0f0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.192.71.33  netmask 255.255.255.0  broadcast 10.192.71.255
        ether a4:bf:01:0d:3a:0b  txqueuelen 1000  (Ethernet)
        RX packets 29667379  bytes 14676568433 (13.6 GiB)
        RX errors 0  dropped 39  overruns 0  frame 0
        TX packets 34623075  bytes 13683025186 (12.7 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device memory 0x91a20000-91a40000

enp3s0f3: flags=4098<BROADCAST,MULTICAST>  mtu 1500
        ether a4:bf:01:0d:3a:0c  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device memory 0x91a00000-91a20000

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 0  (Local Loopback)
        RX packets 468062584  bytes 63792859121 (59.4 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 468062584  bytes 63792859121 (59.4 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```


### 2.2 `cat /proc/net/arp` 查看连接到本机的远端IP的MAC地址
不能看到本机的MAC，只能看到远程连接，服务端用的比较多。

```shell
[root@node7132 ~]# cat /proc/net/arp
IP address       HW type     Flags       HW address            Mask     Device
10.192.71.33     0x1         0x2         a4:bf:01:0d:3a:0b     *        bond1
...
```

# ARP协议
## 1. 什么是ARP协议
> 地址解析协议，即ARP（Address Resolution Protocol），是根据IP地址获取物理地址的一个TCP/IP协议。主机发送信息时将包含目标IP地址的ARP请求广播到局域网络上的所有主机，并接收返回消息，以此确定目标的物理地址；收到返回消息后将该IP地址和物理地址存入本机ARP缓存中并保留一定时间，下次请求时直接查询ARP缓存以节约资源。地址解析协议是建立在网络中各个主机互相信任的基础上的，局域网络上的主机可以自主发送ARP应答消息，其他主机收到应答报文时不会检测该报文的真实性就会将其记入本机ARP缓存；由此攻击者就可以向某一主机发送伪ARP应答报文，使其发送的信息无法到达预期的主机或到达错误的主机，这就构成了一个ARP欺骗。ARP命令可用于查询本机ARP缓存中IP地址和MAC地址的对应关系、添加或删除静态对应关系等。

## 2. `arp -a` 命令查看ARP缓存
> ARP缓存是个用来储存IP地址和MAC地址的缓冲区，其本质就是一个IP地址-->MAC地址的对应表，表中每一个条目分别记录了网络上其他主机的IP地址和对应的MAC地址。每一个以太网或令牌环网络适配器都有自己单独的表。当地址解析协议被询问一个已知IP地址节点的MAC地址时，先在ARP缓存中查看，若存在，就直接返回与之对应的MAC地址，若不存在，才发送ARP请求向局域网查询。

```shell
[root@node7132 ~]# cat /proc/net/arp
IP address       HW type     Flags       HW address            Mask     Device
10.192.71.160    0x1         0x2         a0:42:3f:29:87:be     *        bond1
10.192.71.41     0x1         0x2         54:c4:15:07:dc:59     *        bond1
10.192.71.33     0x1         0x2         a4:bf:01:0d:3a:0b     *        bond1
10.192.71.144    0x1         0x2         a4:bf:01:04:17:34     *        bond1
10.192.71.57     0x1         0x2         a0:42:3f:29:87:be     *        bond1
172.17.0.2       0x1         0x2         02:42:ac:11:00:02     *        docker0
10.192.71.43     0x1         0x2         0c:c4:7a:7c:e8:e0     *        bond1
10.192.71.35     0x1         0x2         a0:42:3f:2e:f6:50     *        bond1
10.192.71.40     0x1         0x2         a4:bf:01:04:d2:f4     *        bond1
10.192.71.254    0x1         0x2         50:98:b8:15:7c:d8     *        bond1
10.192.71.45     0x1         0x2         0c:c4:7a:c0:36:bc     *        bond1
10.192.71.37     0x1         0x2         a4:bf:01:04:17:34     *        bond1
10.192.71.24     0x1         0x2         44:19:b6:ff:aa:82     *        bond1
10.192.71.156    0x1         0x2         00:0c:29:a9:62:62     *        bond1
10.192.71.58     0x1         0x2         0c:c4:7a:41:e9:d2     *        bond1
10.192.71.55     0x1         0x2         14:da:e9:44:15:88     *        bond1
10.192.71.150    0x1         0x2         a4:bf:01:0d:3a:0b     *        bond1
[root@node7132 ~]#

# arp 命令
[root@node7132 ~]# arp -a
? (10.192.71.160) at a0:42:3f:29:87:be [ether] on bond1
? (10.192.71.41) at 54:c4:15:07:dc:59 [ether] on bond1
? (10.192.71.33) at a4:bf:01:0d:3a:0b [ether] on bond1
? (10.192.71.144) at a4:bf:01:04:17:34 [ether] on bond1
node7157 (10.192.71.57) at a0:42:3f:29:87:be [ether] on bond1
? (172.17.0.2) at 02:42:ac:11:00:02 [ether] on docker0
? (10.192.71.43) at 0c:c4:7a:7c:e8:e0 [ether] on bond1
? (10.192.71.35) at a0:42:3f:2e:f6:50 [ether] on bond1
? (10.192.71.40) at a4:bf:01:04:d2:f4 [ether] on bond1
? (10.192.71.254) at 50:98:b8:15:7c:d8 [ether] on bond1
? (10.192.71.45) at 0c:c4:7a:c0:36:bc [ether] on bond1
? (10.192.71.37) at a4:bf:01:04:17:34 [ether] on bond1
? (10.192.71.24) at 44:19:b6:ff:aa:82 [ether] on bond1
? (10.192.71.156) at 00:0c:29:a9:62:62 [ether] on bond1
? (10.192.71.58) at 0c:c4:7a:41:e9:d2 [ether] on bond1
? (10.192.71.55) at 14:da:e9:44:15:88 [ether] on bond1
? (10.192.71.150) at a4:bf:01:0d:3a:0b [ether] on bond1

[root@node7132 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.192.71.32 node7132
10.192.71.57 node7157
[root@node7132 ~]#
```

# DNS
## 1. 什么是DNS
> 域名系统（英文：Domain Name System，缩写：DNS）是互联网的一项服务。它作为将域名和IP地址相互映射的一个分布式数据库，能够使人更方便地访问互联网。DNS使用TCP和UDP端口53。当前，对于每一级域名长度的限制是63个字符，域名总长度则不能超过253个字符。

## 2. 域名解析
注意不要把 `/etc/hosts` 和 `/etc/resolv.conf` 混淆。

1．	`/etc/hosts` 配置文件 是 主机名查询静态表，格式为：
ip地址   主机名/域名   （主机别名）
```shell
[root@node7132 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.192.71.32 node7132
10.192.71.57 node7157
```

2．	/etc/resolv.conf 配置文件用于配置客户端DNS域名服务器地址，格式为：
nameserver    DNS服务器的IP地址
域名服务器是按照文件中出现的顺序来查询的,且只有当第一个nameserver没有反应时才查询下一个nameserver。
```shell
[root@node7132 ~]# cat /etc/resolv.conf
# Generated by NetworkManager
nameserver 114.114.114.114
```

3．	解析域名的顺序：dns缓存 > hosts > dns服务

所以，配置DNS服务器时，需要将DNS的IP地址配置到 /etc/resolv.conf 文件中，如果有多个 nameserver，需要注意顺序。

### 2.1 `nslookup` 命令
```shell
[root@node7132 ~]# nslookup www.baidu.com 114.114.114.114
Server:         114.114.114.114
Address:        114.114.114.114#53

Non-authoritative answer:
www.baidu.com   canonical name = www.a.shifen.com.
Name:   www.a.shifen.com
Address: 61.135.169.121
Name:   www.a.shifen.com
Address: 61.135.185.32
```

### 2.2 `dig`命令
```shell
[root@node7132 ~]# dig www.baidu.com @114.114.114.114

; <<>> DiG 9.9.4-RedHat-9.9.4-14.el7 <<>> www.baidu.com @114.114.114.114
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 19999
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.baidu.com.                 IN      A

;; ANSWER SECTION:
www.baidu.com.          117     IN      CNAME   www.a.shifen.com.
www.a.shifen.com.       212     IN      A       61.135.169.121
www.a.shifen.com.       212     IN      A       61.135.185.32

;; Query time: 18 msec
;; SERVER: 114.114.114.114#53(114.114.114.114)
;; WHEN: Tue Dec 01 09:16:06 CST 2020
;; MSG SIZE  rcvd: 101
```

## 3. 常用的DNS服务
### 3.1 CoreDNS+etcd
    - [centos7.5下coredns+etcd搭建DNS服务器](https://www.cnblogs.com/leffss/p/10148507.html)

# OSI七层模型 + TCP/IP五层模型
- [OSI七层模型与TCP/IP五层模型](https://www.cnblogs.com/qishui/p/5428938.html)

1. 应用层

    OSI参考模型中最靠近用户的一层，是为计算机用户提供应用接口，也为用户直接提供各种网络服务。我们常见应用层的网络服务协议有：HTTP，HTTPS，FTP，POP3、SMTP等。
2. 表示层

    表示层提供各种用于应用层数据的编码和转换功能,确保一个系统的应用层发送的数据能被另一个系统的应用层识别。如果必要，该层可提供一种标准表示形式，用于将计算机内部的多种数据格式转换成通信中采用的标准表示形式。数据压缩和加密也是表示层可提供的转换功能之一。
3. 会话层

    会话层就是负责建立、管理和终止表示层实体之间的通信会话。该层的通信由不同设备中的应用程序之间的服务请求和响应组成。      
4. 传输层
   
    传输层建立了主机端到端的链接，传输层的作用是为上层协议提供端到端的可靠和透明的数据传输服务，包括处理差错控制和流量控制等问题。该层向高层屏蔽了下层数据通信的细节，使高层用户看到的只是在两个传输实体间的一条主机到主机的、可由用户控制和设定的、可靠的数据通路。我们通常说的，TCP UDP就是在这一层。端口号既是这里的“端”。
5. 网络层

    本层通过IP寻址来建立两个节点之间的连接，为源端的运输层送来的分组，选择合适的路由和交换节点，正确无误地按照地址传送给目的端的运输层。就是通常说的IP层。这一层就是我们经常说的IP协议层。IP协议是Internet的基础。
6. 数据链路层 

    将比特组合成字节,再将字节组合成帧,使用链路层地址 (以太网使用MAC地址)来访问介质,并进行差错检测。
数据链路层又分为2个子层：逻辑链路控制子层（LLC）和媒体访问控制子层（MAC）。
MAC子层处理CSMA/CD算法、数据出错校验、成帧等；LLC子层定义了一些字段使上次协议能共享数据链路层。 在实际使用中，LLC子层并非必需的。
7. 物理层     

    实际最终信号的传输是通过物理层实现的。通过物理介质传输比特流。规定了电平、速度和电缆针脚。常用设备有（各种物理设备）集线器、中继器、调制解调器、网线、双绞线、同轴电缆。这些都是物理层的传输介质。

# HTTP 和 HTTPS

## 1. HTTPS 和 SSL 证书
- [HTTPS 与 SSL 证书概要](https://www.runoob.com/w3cnote/https-ssl-intro.html)
- [https-sequence-diagram](http://note.youdao.com/s/BCjF5frI)

## 2. cookie和session的区别
- [cookie和session的详解与区别](https://www.cnblogs.com/l199616j/p/11195667.html)

## 3. HTTP 的 GET 和 POST 区别？
Http协议定义了很多与服务器交互的方法，最基本的有4种，分别是GET,POST,PUT,DELETE. 一个URL地址用于描述一个网络上的资源，而HTTP中的GET, POST, PUT, DELETE就对应着对这个资源的查，改，增，删4个操作。 我们最常见的就是GET和POST了。GET一般用于获取/查询资源信息，而POST一般用于更新资源信息.

GET和POST的区别

1. GET提交的数据会放在URL之后，以?分割URL和传输数据，参数之间以&相连，如EditPosts.aspx?name=test1&id=123456.  POST方法是把提交的数据放在HTTP包的Body中.

2. GET提交的数据大小有限制（因为浏览器对URL的长度有限制），而POST方法提交的数据没有限制.

3. GET方式需要使用Request.QueryString来取得变量的值，而POST方式通过Request.Form来获取变量的值。

4. GET方式提交数据，会带来安全问题，比如一个登录页面，通过GET方式提交数据时，用户名和密码将出现在URL上，如果页面可以被缓存或者其他人可以访问这台机器，就可以从历史记录获得该用户的账号和密码.


## 4. HTTP状态码含义？1xx、2xx、3xx、4xx、5xx？
Response 消息中的第一行叫做状态行，由HTTP协议版本号， 状态码， 状态消息 三部分组成。

状态码用来告诉HTTP客户端,HTTP服务器是否产生了预期的Response.

HTTP/1.1中定义了5类状态码， 状态码由三位数字组成，第一个数字定义了响应的类别

1XX  提示信息 - 表示请求已被成功接收，继续处理

2XX  成功 - 表示请求已被成功接收，理解，接受

3XX  重定向 - 要完成请求必须进行更进一步的处理

4XX  客户端错误 -  请求有语法错误或请求无法实现

5XX  服务器端错误 -   服务器未能实现合法的请求

常见的状态码：
- 200 OK 表明该请求被成功地完成，所请求的资源发送回客户端
- 302 Found 重定向，新的URL会在response 中的Location中返回，浏览器将会自动使用新的URL发出新的Request
- 400 Bad Request  客户端请求与语法错误，不能被服务器所理解
- 403 Forbidden 服务器收到请求，但是拒绝提供服务
- 404 Not Found 请求资源不存在（输错了URL）
- 500 Internal Server Error 服务器发生了不可预期的错误
- 503 Server Unavailable 服务器当前不能处理客户端的请求，一段时间后可能恢复正常



# TCP
## 1. TCP 协议如何保证可靠传输的？
> TCP通过序列号、检验和、确认应答信号、重发控制、连接管理、窗口控制、流量控制、拥塞控制实现可靠性。

- 序列号： 应用数据被分割成 TCP 认为最适合发送的数据块。给发送的每一个包进行编号，接收方对数据包进行排序，把有序数据传送给应用层。
- 校验和： TCP 将保持它首部和数据的检验和。这是一个端到端的检验和，目的是检测数据在传输过程中的任何变化。如果收到段的检验和有差错，TCP 将丢弃这个报文段和不确认收到此报文段。TCP 的接收端会丢弃重复的数据。
- 流量控制： TCP 连接的每一方都有固定大小的缓冲空间，TCP的接收端只允许发送端发送接收端缓冲区能接纳的数据。当接收方来不及处理发送方的数据，能提示发送方降低发送的速率，防止包丢失。TCP 使用的流量控制协议是可变大小的滑动窗口协议。 （TCP 利用滑动窗口实现流量控制）
- 拥塞控制： 当网络拥塞时，减少数据的发送。
- 停止等待协议: 也是为了实现可靠传输的，它的基本原理就是每发完一个分组就- 停止发送，等待对方确认。在收到确认后再发下一个分组。 超时重传： 当 TCP 发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段。

## 2. TCP 的拥塞控制算法：慢启动、拥塞避免、快速重传、快速恢复



- [TCP的拥塞控制（详解）](https://blog.csdn.net/qq_41431406/article/details/97926927)
- [TCP拥塞控制机制（附面试题）](https://blog.csdn.net/shuxnhs/article/details/80644531)

## 3. 滑动窗口详解
- [一篇带你读懂TCP之“滑动窗口”协议](https://www.cnblogs.com/coder-programming/p/10627746.html)
- [TCP-IP详解：滑动窗口（Sliding Window）](https://blog.csdn.net/wdscq1234/article/details/52444277)


TCP中采用滑动窗口来进行传输控制，滑动窗口的大小意味着接收方还有多大的缓冲区可以用于接收数据。发送方可以通过滑动窗口的大小来确定应该发送多少字节的数据。当滑动窗口为0时，发送方一般不能再发送数据报，但有两种情况除外，一种情况是可以发送紧急数据，例如，允许用户终止在远端机上的运行进程。另一种情况是发送方可以发送一个1字节的数据报来通知接收方重新声明它希望接收的下一字节及发送方的滑动窗口大小。

> 接收端可以根据自己的状况通告窗口大小，从而控制发送端的接收，进行流量控制

## 对比 滑动窗口和拥塞窗口
- 滑动窗口是控制接收以及同步数据范围的，通知发送端目前接收的数据范围，用于流量控制，接收端使用。因为tcp是全双工，所以两边都有滑动窗口。 
- 拥塞窗口是控制发送速率的，避免发的过多，发送端使用。
- 两个窗口的维护是独立的，滑动窗口主要由接收方反馈缓存情况来维护，拥塞窗口主要由发送方的拥塞控制算法检测出的网络拥塞程度来决定的。


## 4. TCP 的三次握手和四次握手？

## 5. TCP 连接为什么不是两次握手或者四次握手？

## 6. TIME_WAIT 的存在解决了什么问题？等待时间为什么是 2MSL？

## 7. 半连接队列？全连接队列？
[TCP半连接队列和全连接](https://blog.csdn.net/weixin_30590285/article/details/94797171)

[关于TCP 半连接队列和全连接队列](https://www.cnblogs.com/sidesky/p/6844228.html)

在TCP三次握手中，服务器维护一个半连接队列（sync queue） 和一个全连接队列（accept queue）。 

-  当服务端接收到客户端第一次SYN握手请求时，将创建的request_sock结构，存储在半连接队列中（向客户端发送SYN+ACK，并期待客户端响应ACK），此时的连接在服务器端出于SYN_RECV状态。
-  当服务端收到客户端最后的ACK确认时，将半连接中的相应条目删除，然后将相应的连接放入 全连接队列中， 此时服务端连接状态为ESTABLISHED。 进入全连接队列中的连接等待accept()调用取用。



半连接队列的长度将为 max(64, /proc/sys/net/ipv4/tcp_max_syn_backlog) ，此时对半连接填满时的处理策略是 server将 丢弃请求连接的SYN,不回复SYN+ACK,这样就会造成client收不到握手响应，始终处在SYN_SENT状态，经过几次重传后，客户端 connect() 调用失败。

```shell
[root@node8220]# cat /proc/sys/net/ipv4/tcp_max_syn_backlog
2048
```



全连接队列的长度为 min(backlog, somaxconn)，默认情况下，somaxconn 的值为 128（/proc/sys/net/core/somaxconn），表示最多有 129 的 ESTAB 的连接等待 accept()，而 backlog 的值则是由 int listen(int sockfd, int backlog) 中的第二个参数指定，listen 里面的 backlog 可以有我们的应用程序去定义。 当全连接队列满了后的处理策略基于TCP参数net.ipv4.tcp_abort_on_overflow，在我的机器上默认为0。 

```shell
[root@node8220 log]# cat /proc/sys/net/core/somaxconn
128
[root@node8220 log]# cat /proc/sys/net/ipv4/tcp_abort_on_overflow
0
```

```shell
int listen(int sockfd, int backlog);
```



　　 **1.tcp_abort_on_overflow 关闭时**

　　当server收到最后一次ACK时，希望将连接从半连接队列中取出放入全连接队列，但是此时全连接队列已满，此时的策略是 将最后接收到的ACK丢弃，并且根据net.ipv4.tcp_synack_retries定义的次数重新向client发送SYN+ACK， client在接收到重传的SYN+ACK后会认为之前的ACK丢失了进而重传ACK，这样在下次重新接收到ACK后，如果全连接队列有空间了，连接就可以正确完成建立。 如果重传了规定次数后全连接队列中依旧没有空间，那么server会简单终止这次连接（这里简单终止的意思是server并没有像client发送RST表明连接无法建立，而是直接丢弃了，这样就会导致在client中的连接处在ESTABLISHED状态，并一直如此）。

　　 **2.tcp_abort_on_overflow 开启时**

　　在收到握手的最后一次ACK后，在全连接中如果没有空间，直接向client回复RST，表示连接无法建立。

## 8. 有很多close_wait怎么解决？
close_wait状态是被动关闭连接的一端，在确认客户端的FIN请求后，进入close_wait状态，等待服务端发送完数据后，调用close接口关闭连接。
有大量close_wait状态的连接，很有可能是发生了连接泄漏，即没有主动调用close。

# 网络编程

## 1. 进程间通信IPC方式？
- 信号量
- 管道（有名、无名）
- 消息队列
- 共享内存
- socket

## 2. Linux五种I/O模型
参考： [linux五种IO模型](https://blog.csdn.net/z_ryan/article/details/80873449)
- 阻塞式
- 非阻塞式
- I/O多路复用
- 信号驱动
- 异步I/O

## 3. 两种高性能I/O模式
参考： [彻底搞懂Reactor模型和Proactor模型](https://cloud.tencent.com/developer/article/1488120)
- Reactor
- Proactor

## 4. I/O复用机制

### 4.1 select、poll、epoll 之间的区别
参考： 
- [select、poll、epoll之间的区别总结[整理]](https://www.cnblogs.com/Anker/p/3265058.html)
- [大话 Select、Poll、Epoll](https://cloud.tencent.com/developer/article/1005481)


（1）select，poll实现需要自己不断轮询所有fd集合，直到设备就绪，期间可能要睡眠和唤醒多次交替。而epoll其实也需要调用epoll_wait不断轮询就绪链表，期间也可能多次睡眠和唤醒交替，但是它是设备就绪时，调用回调函数，把就绪fd放入就绪链表中，并唤醒在epoll_wait中进入睡眠的进程。虽然都要睡眠和交替，但是select和poll在“醒着”的时候要遍历整个fd集合，而epoll在“醒着”的时候只要判断一下就绪链表是否为空就行了，这节省了大量的CPU时间。这就是回调机制带来的性能提升。

（2）select，poll每次调用都要把fd集合从用户态往内核态拷贝一次，并且要把current往设备等待队列中挂一次，而epoll只要一次拷贝，而且把current往等待队列上挂也只挂一次（在epoll_wait的开始，注意这里的等待队列并不是设备等待队列，只是一个epoll内部定义的等待队列）。这也能节省不少的开销。

### 4.2 epoll 两种工作模式：水平触发、边缘触发
　　epoll对文件描述符的操作有两种模式：LT（level trigger）和ET（edge trigger）。LT模式是默认模式，LT模式与ET模式的区别如下：

　　LT模式：当epoll_wait检测到描述符事件发生并将此事件通知应用程序，应用程序可以不立即处理该事件。下次调用epoll_wait时，会再次响应应用程序并通知此事件。

　　ET模式：当epoll_wait检测到描述符事件发生并将此事件通知应用程序，应用程序必须立即处理该事件。如果不处理，下次调用epoll_wait时，不会再次响应应用程序并通知此事件。

　　ET模式在很大程度上减少了epoll事件被重复触发的次数，因此效率要比LT模式高。epoll工作在ET模式的时候，必须使用非阻塞套接口，以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。

## 5. C10K并发连接问题
参考：
- [高性能网络编程(二)：上一个10年，著名的C10K并发连接问题](https://blog.csdn.net/weixin_34015566/article/details/89661252?utm_medium=distribute.pc_relevant.none-task-blog-searchFromBaidu-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-searchFromBaidu-2.control)
- [聊聊C10K问题及解决方案](https://blog.csdn.net/wangtaomtk/article/details/51811011)

### 5.1 思路一：每个进程/线程处理一个连接（不行）
这一思路最为直接。但是由于申请进程/线程会占用相当可观的系统资源，同时对于多进程/线程的管理会对系统造成压力，因此这种方案不具备良好的可扩展性。

因此，这一思路在服务器资源还没有富裕到足够程度的时候，是不可行的。即便资源足够富裕，效率也不够高。总之，此思路技术实现会使得资源占用过多，可扩展性差。

### 5.2 思路二：每个进程/线程同时处理多个连接（IO多路复用）
IO多路复用从技术实现上又分很多种，我们逐一来看看下述各种实现方式的优劣。

● 实现方式1：传统思路最简单的方法是循环挨个处理各个连接，每个连接对应一个 socket，当所有 socket 都有数据的时候，这种方法是可行的。但是当应用读取某个 socket 的文件数据不 ready 的时候，整个应用会阻塞在这里等待该文件句柄，即使别的文件句柄 ready，也无法往下处理。

实现小结：直接循环处理多个连接。

问题归纳：任一文件句柄的不成功会阻塞住整个应用。

● 实现方式2：select要解决上面阻塞的问题，思路很简单，如果我在读取文件句柄之前，先查下它的状态，ready 了就进行处理，不 ready 就不进行处理，这不就解决了这个问题了嘛？于是有了 select 方案。用一个 fd_set 结构体来告诉内核同时监控多个文件句柄，当其中有文件句柄的状态发生指定变化（例如某句柄由不可用变为可用）或超时，则调用返回。之后应用可以使用 FD_ISSET 来逐个查看是哪个文件句柄的状态发生了变化。这样做，小规模的连接问题不大，但当连接数很多（文件句柄个数很多）的时候，逐个检查状态就很慢了。因此，select 往往存在管理的句柄上限（FD_SETSIZE）。同时，在使用上，因为只有一个字段记录关注和发生事件，每次调用之前要重新初始化 fd_set 结构体。

intselect(intnfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds,structtimeval *timeout);

实现小结：有连接请求抵达了再检查处理。

问题归纳：句柄上限+重复初始化+逐个排查所有文件句柄状态效率不高。

● 实现方式3：poll 主要解决 select 的前两个问题：通过一个 pollfd 数组向内核传递需要关注的事件消除文件句柄上限，同时使用不同字段分别标注关注事件和发生事件，来避免重复初始化。

实现小结：设计新的数据结构提供使用效率。

问题归纳：逐个排查所有文件句柄状态效率不高。

● 实现方式4：epoll既然逐个排查所有文件句柄状态效率不高，很自然的，如果调用返回的时候只给应用提供发生了状态变化（很可能是数据 ready）的文件句柄，进行排查的效率不就高多了么。epoll 采用了这种设计，适用于大规模的应用场景。实验表明，当文件句柄数目超过 10 之后，epoll 性能将优于 select 和 poll；当文件句柄数目达到 10K 的时候，epoll 已经超过 select 和 poll 两个数量级。

实现小结：只返回状态变化的文件句柄。

问题归纳：依赖特定平台（Linux）。

因为Linux是互联网企业中使用率最高的操作系统，Epoll就成为C10K killer、高并发、高性能、异步非阻塞这些技术的代名词了。FreeBSD推出了kqueue，Linux推出了epoll，Windows推出了IOCP，Solaris推出了/dev/poll。这些操作系统提供的功能就是为了解决C10K问题。epoll技术的编程模型就是异步非阻塞回调，也可以叫做Reactor，事件驱动，事件轮循（EventLoop）。Nginx，libevent，node.js这些就是Epoll时代的产物。

● 实现方式5：由于epoll, kqueue, IOCP每个接口都有自己的特点，程序移植非常困难，于是需要对这些接口进行封装，以让它们易于使用和移植，其中libevent库就是其中之一。跨平台，封装底层平台的调用，提供统一的 API，但底层在不同平台上自动选择合适的调用。按照libevent的官方网站，libevent库提供了以下功能：当一个文件描述符的特定事件（如可读，可写或出错）发生了，或一个定时事件发生了，libevent就会自动执行用户指定的回调函数，来处理事件。目前，libevent已支持以下接口/dev/poll, kqueue, event ports, select, poll 和 epoll。Libevent的内部事件机制完全是基于所使用的接口的。因此libevent非常容易移植，也使它的扩展性非常容易。目前，libevent已在以下操作系统中编译通过：Linux，BSD，Mac OS X，Solaris和Windows。使用libevent库进行开发非常简单，也很容易在各种unix平台上移植

## 6. 多线程编程
互斥锁、条件变量、读写锁、线程池等

### 1. 哲学家就餐问题
- [哲学家就餐问题](https://baike.baidu.com/item/%E5%93%B2%E5%AD%A6%E5%AE%B6%E5%B0%B1%E9%A4%90%E9%97%AE%E9%A2%98/10929794?fr=aladdin)


### 2. 读者写者问题 
- [操作系统——读者写者问题](https://www.cnblogs.com/wkfvawl/p/11538431.html)

## 7. fork()函数
下面这段代码的打印输出？（写出4组）
```cpp
#include <iostream>
#include <sys/types.h>
#include <unistd.h>
#include <time.h>

void demo(){
  pid_t pid1;
  pid_t pid2;

  pid1 = fork();
  pid2 = fork();

 std::cout<<"cur_pid:"<<getpid()<<", pid1:"<<pid1<<", pid2:"<<pid2<<std::endl;
}

int main(){
  demo();
  sleep(100000);
  return 0;
}

/*
cur_pid:26003, pid1:26004, pid2:26005
cur_pid:26004, pid1:0, pid2:26006
cur_pid:26005, pid1:26004, pid2:0
cur_pid:26006, pid1:0, pid2:0
*/
```

## ipc机制，为什么共享存储区效率最高？