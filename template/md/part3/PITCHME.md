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
可以看到对于客户端来说连接很快就建立起来，但是服务端经过两次最后一个ack重传后依然没办法把连接建立起来（次数是由net.ipv4.tcp_synack_retries 选项决定的），查看所有的相关的网络参数net.core.somaxconn = 262144，net.ipv4.tcp_max_syn_backlog=262144,net.ipv4.tcp_syncookies都已经重新设置成比较大的值
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
@snap[south span-100]
![](http://wx2.sinaimg.cn/large/0060lm7Tly1g1xkrm4o6hj30dn0a5dh8.jpg)
<br><br>
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[span-100]
参考图和linux代码，最终定位到是下面代码导致了收到最后ack的连接被丢弃掉
@snapend


