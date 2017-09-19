#TimeWait

[参考资料](https://huoding.com/2013/12/31/316)

以前我写过[探讨过TCP close_wait 状态过多](http://blog.csdn.net/willdeamon/article/details/53043948)的问题，现在遇到一个可能timewait过多的问题。

##查询命令

查询服务器timewait数量命令

```
shell> ss -ant | awk '
    NR>1 {++s[$1]} END {for(k in s) print k,s[k]}
'
```

或者

```
shell> cat /proc/net/sockstat
```

##TimeWait处于的状态
tcp关闭的时候时候如下图

![tcp close](https://huoding.com/wp-content/uploads/2013/12/tcp_close.png)

关闭的时候：

1. 主动关闭方发送fin信号， 进入fin-wait-1状态
2. 被动关闭方接受fin信号， 同时返回一个ack信号，进入close-wait状态
3. 主动关闭方，接受ack信号，进入fin-wait-2状态
4. 被动关闭方准备好关闭步骤后，发送一个fin信号。
5. 主动关闭方接受fin信号，返回一个ack信号，进入time_wait状态。
6. 被动关闭方接受ack信号，关闭连接
7. 主动关闭方等待2倍的msl(maximun segment lifetime)时间然后再关闭连接

以下引用参考资料：

```
穿插一点MSL的知识：MSL指的是报文段的最大生存时间，
如果报文段在网络活动了MSL时间，还没有被接收，那么会被丢弃。
关于MSL的大小，RFC 793协议中给出的建议是两分钟，
不过实际上不同的操作系统可能有不同的设置，
以Linux为例，通常是半分钟，两倍的MSL就是一分钟，也就是60秒，
并且这个数值是硬编码在内核中的，也就是说除非你重新编译内核，
否则没法修改它
```

##为什么不直接close
这是因为TCP是建立在不可靠网络上的可靠的协议。例子：主动关闭的一方收到被动关闭的一方发出的FIN包后，回应ACK包，同时进入TIME_WAIT状态，但是因为网络原因，主动关闭的一方发送的这个ACK包很可能延迟，从而触发被动连接一方重传FIN包。极端情况下，这一去一回，就是两倍的MSL时长。如果主动关闭的一方跳过TIME_WAIT直接进入CLOSED，或者在TIME_WAIT停留的时长不足两倍的MSL，那么当被动关闭的一方早先发出的延迟包到达后，就可能出现类似下面的问题：

* 旧的TCP连接已经不存在了，系统此时只能返回RST包
* 新的TCP连接被建立起来了，延迟包可能干扰新的连接

不管是哪种情况都会让TCP不再可靠，所以TIME_WAIT状态有存在的必要性。