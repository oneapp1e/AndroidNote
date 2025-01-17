P2P原理_NAT穿透
===

### NAT

NAT（Network Address Translation，网络地址转换），也叫做网络掩蔽或者IP掩蔽。NAT是一种网络地址翻译技术，主要是将内部的私有IP地址（private IP）转换成可以在公网使用的公网IP（public IP）。


网络地址转换，就是替换IP报文头部的地址信息。NAT通常部署在一个组织的网络出口位置，通过将内部网络IP地址替换为出口的IP地址提供公网可达性和上层协议的连接能力。那么，什么是内部网络IP地址？

 [RFC1918](https://datatracker.ietf.org/doc/rfc1918/)规定了三个保留地址段落：10.0.0.0-10.255.255.255；172.16.0.0-172.31.255.255；192.168.0.0-192.168.255.255。这三个范围分别处于A,B,C类的地址段，不向特定的用户分配，被IANA作为私有地址保留。这些地址可以在任何组织或企业内部使用，和其他Internet地址的区别就是，仅能在内部使用，不能作为全球路由地址。这就是说，出了组织的管理范围这些地址就不再有意义，无论是作为源地址，还是目的地址。对于一个封闭的组织，如果其网络不连接到Internet，就可以使用这些地址而不用向IANA提出申请，而在内部的路由管理和报文传递方式与其他网络没有差异。

 对于有Internet访问需求而内部又使用私有地址的网络，就要在组织的出口位置部署NAT网关，在报文离开私网进入Internet时，将源IP替换为公网地址，通常是出口设备的接口地址。一个对外的访问请求在到达目标以后，表现为由本组织出口设备发起，因此被请求的服务端可将响应由Internet发回出口网关。出口网关再将目的地址替换为私网的源主机地址，发回内部。这样一次由私网主机向公网服务端的请求和响应就在通信两端均无感知的情况下完成了。依据这种模型，数量庞大的内网主机就不再需要公有IP地址了。

 那么，NAT与此同时也带来一些弊端：首先是，NAT设备会对数据包进行编辑修改，这样就降低了发送数据的效率；此外，各种协议的应用各有不同，有的协议是无法通过NAT的（不能通过NAT的协议还是蛮多的），这就需要通过穿透技术来解决。我们后面会重点讨论穿透技术。



**虽然实际过程远比这个复杂，但上面的描述概括了NAT处理报文的几个关键特点：**



- 1）网络被分为私网和公网两个部分，NAT网关设置在私网到公网的路由出口位置，双向流量必须都要经过NAT网关；
- 2）网络访问只能先由私网侧发起，公网无法主动访问私网主机；
- 3）NAT网关在两个访问方向上完成两次地址的转换或翻译，出方向做源信息替换，入方向做目的信息替换；
- 4）NAT网关的存在对通信双方是保持透明的；
- 5）NAT网关为了实现双向翻译的功能，需要维护一张关联表，把会话的信息保存下来。



 简单的背景了解过后，下面介绍下NAT实现的主要方式，以及NAT都有哪些类型。

### NAT的实现方式
- 静态NAT: 就是静态地址转换。是指一个公网IP对应一个私有IP，是一对一的转换，同时注意，这里只进行了IP转换，而没有进行端口的转换。

- NAPT： 端口多路复用技术。与静态NAT的差别是，NAPT不但要转换IP地址，还要进行传输层的端口转换。具体的表现形式就是，对外只有一个公网IP，通过端口来区别不同私有IP主机的数据。

### NAT映射

表内记录了私有ip和公有ip的对应关系，例如： 
```
192.168.1.35:8000 <--> 123.24.56.78:10000
....
```

### NAT打洞

正常两个人发送数据，例如通过QQ聊天，目前的步骤是： 
- A使用私有ip经过NAT映射转换为公有IP后将数据发送给腾讯
- 腾讯将数据转发给B用户
那后续能不能直接A和B进行链接，这样可以提高效率： 
- 直接通讯存在一个问题，路由器内部有一个保护支持，对于陌生的IP第一次发的数据包，路由器会丢弃或屏蔽掉，以此来防止陌生网络进行攻击。
- 所以要想能够让B的路由器直接接受到A发送的数据，那就必须保证B的路由器要熟悉A的IP地址，这样B才能够进行接收。而A和B首次登录QQ的时候腾讯已经将自己的ip返回给A和B了，所以A和B的路由中都认识腾讯的IP了，这样A和B都能接受腾讯发送的数据包。
- 那如何让A和B进行直接通信，这里就是腾讯所有的事情，腾讯就通过腾讯的公网IP对AB之间进行了打洞的操作，将对方的IP都下发给对应的AB用户，这样就把他俩形成了一个私有的通路，这个操作就是打洞。 




















---

- 邮箱 ：charon.chui@gmail.com  
- Good Luck! 
