---
layout: post
category: Computer Network
title: Computer Network Review
tagline: by Archer
tags:
- Computer Network
- Review

---

1.3 The network core

IP is hierachical structure. Router has a forwarding table that maps destination address to that router's outbound links.

Circuit Switching and Packaet Switching: Circuit 比较像电话线，独占一条线路，传输速率固定有保证。Packet Switching是Internet的模式，不一定保证速率，可能congest。

Access ISP, Regional ISP, tier-1 ISP，PoP(Points of Presence), Multihoming(Connect to multiple ISP providers), Peer(ISP on the same level can peer to reduce the cost), Content Provider Networks

1.4 Delay, Loss and Throughput in Packet-Switched Networks

Transmission Delay：发一个bit在router所耗费的时间. 

Propagation Delay: 从一个router到另一个router所耗费的时间

Queueing Delay: 网络拥堵情况下，包在router前排队

Queueing Delay Vary packet by packet.  

Traceroute,看到到target的路由情况，并不一定递增由于Queueing Delay，

Throughput，可能由access network 带宽决定，也可能由 core中某一条路线决定（多个流同时走这个路线导致并行速度变少）

1.5 Protocol layers and their service models

五层协议栈：Application(HTTP,SMTP,FTP), Transport(TCP,UDP), Network(IP), Link(WIFI,Ethernet,PPP), Phyiscal

Application layer packet: Messsage

Transport: segement

Network: datagram, 这一层将datagram从source经过一系列router传到destination

Link: frame，Network层的datagram可能会经过很多节点，节点之间的传输可能使用各种链路层协议（可靠或不可靠的）。这里的可靠和TCP是不一样的，TCP是保证从一个系统到另一个系统的可靠传输。
Link Layer的job是将整个frame从一个network element搬到另一个

Physical：搬运bits

下层协议为上层协议提供服务

Encapsulation：host会implements五层协议，网络内部的link-layer switch只implement两层，router implement 三层。 Internet的设计就是将complexity留给network的edge（host）。

2.0 Application Layer

2.1 Principles of Network Applications
         
Socket: Interface between the application layer and transport layer within a host

用户可以在socket的applicaiton layer一端控制很多，另一端只能控制transport layer的协议类型，以及一些参数比如buffer size

（TCP！！！） Socket是一个由(src_ip,src_port,dest_ip,dest_port,protocol)组成的五元组来决定的，其中任何一个不一样都不是相同的socket连接。因此服务端开80端口，可以有很多socket connection连到上面，只要src_ip或者src_port不一样，一般src_port是一个随机的高位port

Application layer的协议经常是由Server-Client模式来完成,也有P2P协议

HTTP协议，client在socket一端塞入HTTP Request交给TCP Connection, server在另一端从TCP Connection接收HttpRequest

TCP要求在application level message传输之前先要握手（交换transport layer control info），然后就算建立了TCP连接

SOCKET（UDP，TCP）是双向的，TCP要先connect建立连接，UDP则不用，直接发。建立连接（或者UDP发送报文）时Client端不用指定port，由操作系统指定，server端则需固定port

SSL是TCP的enhancement，并不是和TCP/UDP一层，传输的信息要先进入SSL Socket再传给TCP Socket

客户和服务器之间的状态通过Cookie和Session来维护，服务器端的Session仅仅是一个key-value pairs，第一次建立连接，如果方法中操作到了Session，服务端创建一个Session键值对组并存放，把id返回给客户端
之后的每次请求客户端把Cookie作为header（或者URL重写）发给服务端，服务端的getSession方法会在url，cookie header中找到SessionId并且在Session键值对组中找到那个Session，转换为Java中的Session对象再用

Conditional Get是在Web Cache和Server之间的，第一次Cache请求Server的内容，server会返回一个带Last-Modified头的包内容，Cache缓存内容并记录时间。第二次Cache再请求Server内容时，会带上If-Modified-Since头，如果Server的内容在该时间之后没有改动，
就直接返回304 Not Modified, Cache Layer就可以直接返回缓存的结果(不应该是浏览器缓存的吗？？？？？？)

DNS是基于UDP的application layer协议，一般的协议都是用户使用，DNS Server提供Naming Directory服务，将Host Name 翻译成IP Address

DNS有几种Server， root(根据com,edu,提供TLD DNS Server的解析,返回edu DNS server的列表), top-level Domain(提供Authroitative DNS解析，返回ucsb.edu DNS Server的列表), authoritative（可能多层，根据请求的url决定，如果请求yuesong.cs.ucsb.edu,返回cs.ucsb.edu DNS server的列表
（如果CS系有自己的DNS Server的话），否则就返回IP）

DNS的QUERY有Recurisve和Iterative类型的，Recursive相当于自己没找到就向下一层去找，最后返回给host的是其查找地址的最终IP，Iterative相当于自己没找到，但是自己知道下一层DNS的IP是什么，返回给请求者下一层DNS的IP，然后让请求者自己再去发Query给下一层DNS。一般模式是host和local Server之间recusive，local Server到其他DNS iterative

DNS会有缓存，并且有过期时间（一般两天）

还有local DNS server，一般作为DNS查询的proxy，host向local DNS server发查询请求，local DNS server完成一系列的和外部（root，TLD等）交互的动作


3.0 Transport Layer

traceroute的工作原理：发UDP包(或者ICMP，ICMP也是传输层协议，或者TCP ACK包)。host先发一个TTL是1的echo包到dest，到达最近的router时，TTL变为0，router会返回一个ICMP Time Exceed Message，host可以根据这个message来观察时间。之后host再发一个TTL为2的packet，以此类推知道包最终达到dest，返回一个ICMP Echo Reply message。

Transport Layer extend the network layer's delivery service between two ends system to a delivery service between two application processes running on the end systems。

Network layer protocol provides logical communicaiton between hosts while transport layer provides logical communication between process running on different hosts

Transport Layer 能在Network Layer不能保证可靠传输（lose，garbles，duplicate packets）的情况下通过某些机制来保证可靠传输。

IP是一个unreliable service(best efforts)，不保证一定送到，和UDP一样，TCP是relibale service

一个Application 层的process可以开很多socket（作为传输层和应用层的接口），用来接收网络传来的数据。接收端接收到一个segment后，根据segement中的fields去确定该接受的socket的过程叫做demultiplexing。从各个socket中获取data chunks，用header info把这些东西组合到一起成为segment的过程叫做multiplexing。 Transport Layer的作用就在于mutliplexing和demultiplexing(至少要完成这个，network包到application process之间的分发)。

Transport Layer的header主要由source port和dest port组成。当一个segment到达host，transport layer的协议检查port，并且将segment redirect到该port对应的socket上，segment传入socket对应的process

在Applicaiton Layer中提到TCP Socket是由五元组组成的（其实是四元组，protocol一定是TCP），UDP则不一样，是由两元组组成，只要dest_ip和dest_port一样，对应的就是同一个socket

使用TCP协议为基础的application有一个welcome socket（假设为12000端口），用来接收来自client的TCP连接请求，一旦transport layer收到segment并且demultiplex到12000端口上，这个application就会通过serverSocket.accept()创建一个新的TCP socket用来接收之后发来的数据，

4.0 Network Layer
Routing and fowarding(transfer a packet from an input link interface to the appropriate output link interface)

类似于transport layer的connection和connectionless的协议，network layer也有两种network，connection的virtual-circuit或者connectionless的data network. 一个网络只能是这两种的其中一种。

Input ports, Switching fabric and output ports构成了router fowarding plane,想一下高速公路，attendant和roundabout的类比，这些一般用硬件实现。还有一个Router control plane，一般用软件实现，跑在常规CPU上。

IPV4 datagram format: Version Number, Header Length(determin where data actually begins), Type of Service, Datagram Length(整个datagram长度)，Identifier,flags,fragmentation offset(用于ip fragmentation，有的link layer有包最大size限制，也用于最后的拼装),TTL

Protocol，这个field是把network layer和transport layer结合起来的关键，就像transport layer的port是applicaiton结合的关键

IP和interface有关而不是和contain interface的host或者router有关

CIDR(Classless interdomain routing),比ABC这样分类更好。 Address最左侧的x为作为prefix，是网段。 ISP还采用一种address aggregation的方法，来在自己的网段下分配子网给organization

255.255.255.255是广播地址，一个host发一个包到广播地址，信息将会在subnet中广播（通过传输层广播协议）

DCHP协议是一个application层协议，因为其用到了UDP。一般一个子网有一个DHCP服务器，用于在子网内分配IP。当一个新的host加入子网时，会发送一个广播（to 255.255.255.255,67 from 0.0.0.0:68）。DHCP server接受到广播后会offer一个地址（同样广播出去），但因为有可能网内有多个DHCP服务器，客户端接收到信息后，还要返回DHCP request信息说明自己使用哪个，最后被采用的DHCP服务器返回ACK，可以使用

NAT（Network Address Translation)协议用于解决子网内IP数目有限，但是接入的host增加很多的情况。NAT enabled的路由器会把自己的IP（可能由DHCP分配）的端口映射到自己子网内某台机器的端口（138.76.29.7:5001 -> 10.0.0.1,3345），注意这里是公网地址加端口映射到私网地址加端口。这样每个包的源地址被重写，新的包就可以发给公网上的服务器，返回的包可以逆向解包，由路由器再转发回内网的host。

但是，隐藏在NAT防火墙/路由器后的host无法被作为TCP连接的主机，因为它没有公网地址，只能通过NAT向公网发送包而无法主动被人连接（很多P2P应用要求peer a需要能连接网内任何一个peer b，b如果在NAT后就不行）。解决这种问题可以用connection reversal，发送一个请求让B主动去连A就可以了。还有UPnP协议，可以让NAT内网中的主机直接被外界连接。

并不是所有router都支持IPV6协议，因此如果传输IPV6包的话，遇到IPV4 router，可以用一个IPV4包将IPV6包内容包裹起来，学名Tunnel，使用IPv4包跨过这一段不支持IPv6的路由器。等到路由器支持IPV6再解包。

Routing Algo包括centralized algo（link-state）和decentralized alogo（distance-vector），centralized知道网络内所有边的状况，decen的只知道和自己相连的所有边的状况，点和点之间交换信息。还可以吧routing algo分为static和dynamic的两种。