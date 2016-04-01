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

HTTP协议，client在socket一端塞入HTTP Request交给TCP Connection, server在另一端从TCP Connection接收HttpRequest

TCP要求在application level message传输之前先要握手（交换transport layer control info），然后就算建立了TCP连接

SSL是TCP的enhancement，并不是和TCP/UDP一层，传输的信息要先进入SSL Socket再传给TCP Socket


3.0 Transport Layer

Transport Layer extend the network layer's delivery service between two ends system to a delivery service between two application processes running on the end systems。

Network layer protocol provides logical communicaiton between hosts

Transport Layer 能在Network Layer不能保证可靠传输（lose，garbles，duplicate packets）的情况下通过某些机制来保证可靠传输。


