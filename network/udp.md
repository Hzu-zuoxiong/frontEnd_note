# UDP

## 简介

UDP 协议全称是用户数据报协议（User Datagram Protocol），在网络中与 TCP 协议一样用于处理数据包，是一种无连接的协议。在 OSI 模型中，处于第四层传输层，处于 IP 协议的上一层。UDP 有不提供数据包分组、组装和不能对数据包进行排序的缺点，也就是说，当报文发送之后，是无法得知其是否安全 完整到达的。

与 TCP 协议（传输控制协议）一样，UDP 协议直接位于 IP 协议（网际协议）。根据 OSI 参考模型，UDP 和 TCP 都属于传输层协议。UDP 协议的主要作用是将网络数据流量压缩成数据包的形式。一个典型的数据包就是一个二进制数据的传输单位。每一个数据包的前 8 个字节用来包含报头信息，剩余字节则用来包含具体的传输数据。

UDP 的主要特点：

1. **无连接的**，即发送数据之前不需要建立连接，因此减少了开销和发送数据之前的时延。
2. **不保证可靠交付**，因此主机不需要为此复杂的连接状态表。
3. **面向报文的**，意思是 UDP 应用层交下来的报文，既不合并，也不拆分，而是保留这些报文的边界，在添加首部后向下交给 IP 层。
4. **没有阻塞控制**，因此网络出现的拥塞不会使发送方的发送速率降低。
5. **支持一对一、一对多、多对一和多对多的交互通信**，即是提供广播和多播的功能。
6. **首部开销小**，首部只有 8 个字节，分为四部分。

## UDP 和 TCP 的不同

TCP 在传送数据之前必须先建立连接，数据传送结束后要释放连接。TCP 不提供广播或多播服务，由于 TCP 要提供可靠的，面向连接的传输服务，因此不可避免地增加了许多的开销。

而 UDP 在传送数据之前不需要先建立连接。接收方收到 UDP 报文之后，不需要给出任何确认。

**UDP：单个数据报，不用建立连接，简单，不可靠会丢包会乱序。**

**TCP：流式，需要建立连接，复杂，可靠，有序。**

## UDP 报文结构

UDP 数据包分为首部字段和数据字段

首部字段为 8 个字节，由四个字段组成，每个字段 2 个字节。

![](/assets/UDP.png)

- **源端口**：源端口号，在需要对方回信时选用，不需要时可全 0。
- **目的端口**：目的端口号，在终点交付报文时必须要使用到。
- **长度**：UDP 用户数据报的长度，在只有首部的情况其最小值是 8。
- **检验和**：检测 UDP 用户数据报在传输中是否有错，有错就丢弃。
