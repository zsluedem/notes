# TCP_NODELAY 和 TCP_CORK
#介绍
TCP_NODELAY和TCP_CORK基本上控制了包的“Nagle化”，Nagle化在这里的含义是采用Nagle算法把较小的包组装为更大的帧。 John Nagle是Nagle算法的发明人，后者就是用他的名字来命名的，他在1984年首次用这种方法来尝试解决福特汽车公司的网络拥塞问题（欲了解详情请参 看IETF RFC 896）。他解决的问题就是所谓的silly window syndrome ，中文称“愚蠢窗口症候群”，具体含义是，因为普遍终端应用程序每产生一次击键操作就会发送一个包，而典型情况下一个包会拥有一个字节的数据载荷以及40 个字节长的包头，于是产生4000%的过载，很轻易地就能令网络发生拥塞,。 Nagle化后来成了一种标准并且立即在因特网上得以实现。它现在已经成为缺省配置了，但在我们看来，有些场合下把这一选项关掉也是合乎需要的。 

#TCP_NODELAY 
TCP_NODELAY通常用于ssh等实时要求较长的应用
```
socket.setsockopt(socket.IPPROTO_TCP, socket.TCP_NODELAY, 1)
```
设置socket为nodelay, socket马上会发送数据

#TCP_CORK
TCP_CORK通常用于文件传输等大流量传输，使用TCP_CORK能更大提高网络性能，减少网络阻塞。
```
socket.setsockopt(socket.IPPROTO_TCP, socket.TCP_CORK, 1)
```



####参考资料：
1. http://scotdoyle.com/python-epoll-howto.html
2. http://www.cnblogs.com/maowang1991/archive/2013/04/16/3024518.html