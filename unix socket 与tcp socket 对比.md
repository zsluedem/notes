# unix socket 与tcp socket 对比

##unix socket简介
unix socket是用于同一台机器进程通信IPC,只供同一台机器不通进程之间的通信，通常绑定于unix文件

##tcp socket简介
tcp socket是网络间的通信，支持不同机器间的通信，绑定于主机端口。

##性能对比
unix socket 比tcp socket快且更有效率，unix socket 不需要经过网络协议栈，不需要打包拆包、计算校验和、维护序号和应答等，只是将应用层数据从一个进程拷贝到另一个进程。


###参考资料
```
1.https://serverfault.com/questions/124517/whats-the-difference-between-unix-socket-and-tcp-ip-socket
```