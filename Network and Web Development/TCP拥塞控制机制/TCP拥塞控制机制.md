# Congest Control简介

在TCP传输中，sender过快过多地发送segment，不仅可能超出receiver的接收能力，更有可能阻塞网络，影响同路由下其他终端的正常通信，因此，TCP协议不仅许需要Flow Control，也需要Congest Control。

TCP传输的双方都通过窗口来管理segment的收发，且窗口大小一般也决定着收发能力。对此，Congest Control最简单的思路就是，发生网络拥塞时，降低sender方的窗口大小，将其窗口大小降低到不超过某个特定的值即可。假设这个值为CWND。

假设sender的窗口大小为SWND，receiver的窗口大小为RWND，考虑到Flow Control和Congest Control，我们有：
```cpp
SWND = min(CWDN, RWDN)
```

由于网络状态是时刻变化的，拥塞可能产生也可能消失，CWND的值也需要随之动态变化。那如何检测网络拥塞？CWND具体应如何设置并变化（当网络拥塞消失后如何恢复双方的通信能力）？下面会介展开说明Congest Control的技术细节。以下将sender简称为S，receiver简称为R。

<br/><br/>

# Slow Start
即“慢启动”。通信双方三次握手建立连接后，会将CWDN初始化为1。每当S收到一个ACK，CWDN就会加1。Slow Start在通信初期增长比较缓慢，但如果双方正常通信，CWDN的大小会在每轮通信中增长一倍（也就是一次RTT的时间内增长一倍）。

![](TCP拥塞控制机制_1.png)

如图所示，一开始S一次只能发送一个segment，收到R的回复后，CWND增大为2，可以一次发送两个segment。R会为这两个segment回复两个ACK，CWND就增大到四个segment......

# Congestion Avoidance
当然，CWND不会无限增长下去，当其值超出一个阈值（ssthresh）后，拥塞控制机制就从Slow Start（指数增长）转变为Congestion Avoidance（线性增长）。每收到一个ACK，CWND仅增长1/CWND。换句话说，CWDN的大小会在每轮通信中增长1（也就是一次RTT的时间内增长1）。

举个例子，假如现在处于Congestion Avoidance阶段，当前CWND为8，则此轮通信S可以发送8个segment，一个RTT时间后收到8个ACK，CWND就增长1，下一轮通信S就能发送9个segment，以此类推......

# 拥塞发生
当出现segment丢失需要重传时，一般预示着拥塞发生了。重传有超时重传和快速重传两种机制，拥塞控制对这两种重传机制有不同的操作：
## 超时重传：回到Slow Start阶段
发生超时重传，S会重发segment，并将ssthresh设为当前CWND值的一半，然后将CWND初始化为1，并重新回到Slow Start阶段。
## 快速重传：执行Fast Recovery
当S收到正常回复的ACK后，又收到连续三个相同ACK时，会触发快速重传机制（Fast Retransmit）。实际上拥塞控制机制会认为快速重传被触发时的网络情况并不算差，毕竟还能收到后续的多个相同ACK。此时，S同样会重发segment，CWND被设置为原来的一半，ssthresh被设置为与减半后的CWND相同，然后进入“Fast Recovery”阶段。


