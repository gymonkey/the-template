@title[半连接]

## @color[black](半连接)

@fa[arrow-down text-black]

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](现象)
@snapend

@snap[span-100]
另外一次重启慢查询，直播的人气服务只要重启，就会有依赖他的服务发生调用超时的情况，<br>
并且超时请求的seq在服务提供端无法找到，打开dubbo日志，会看到如下莫名其妙的连接被断开的日志<br>
```
Close old netty channe xxxxx on create new netty channel xxx
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[span-100]
阅读dubbo代码发现，dubbo每隔2s钟检查一次连接情况，如果中间使用有问题的连接，就会出现seq找不到而导致的超时，联系运维部署脚本，发现连接最终是被一个rst包干掉，一般进程都不会发送rst包来断开连接，尝试将问题抽象成一个简单的建立连接问题，在某个机器上面重现，得到下面的结果
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[span-100]
防火墙<br>
net.core.somaxconn<br>
net.ipv4.tcp_max_syn_backlog<br>
net.ipv4.tcp_syncookies<br>
@snapend


+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[span-100]
大杀器 systemptap
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[span-100]
参考图和linux代码，最终定位到是下面代码导致了收到最后ack的连接被丢弃掉
@snapend


