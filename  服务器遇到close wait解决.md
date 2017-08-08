#服务器遇到close wait解决

昨晚上同事把代码部署到服务器上遇到web后端代码无反应的情况。

用命令netstat查看发现有很多close wait的情况。以前没有遇到这种情况。百度了下。更深入的了解到tcp的一些机制。

![](http://img.blog.csdn.net/20161105104500839?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

重点在关闭连接的时候，按照现在的情况是应该是浏览器主动关闭发送fin给服务器，然后服务器程序仍然在运行。没有关闭这个连接。多次模仿整个连接过程（通过发送请求）。发现浏览器在发送请求后一直没有得到响应（处于Pengding状态），然后1分钟后，浏览器会主动关闭连接（chrome）。这时候服务器就出现close wait的情况。这么看就是程序有问题，一直没有处理连接。但是查看程序日志没有一场。这很奇怪。


然后在程序上加上更多日志语句，再次观察，发现程序连接口开始都没进行。那肯定是程序设定有问题（用的是django写的后台，也可能是url loop,但是查过都没有。）。最后查出来是settings中的数据库信息没有做修改，导致一直没有连上数据库。把settings改过来就好了。
