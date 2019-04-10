@title[半连接]

## @color[black](半连接)

@fa[arrow-down text-black]

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[south span-100]
![](http://wx4.sinaimg.cn/large/0060lm7Tly1g1xkng3eg0j30dw0hm0tr.jpeg)
<br><br>
@snapend


+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](现象)
@snapend

@snap[south span-100]
另外一次重启慢查询，直播的人气服务只要重启，就会有依赖他的服务发生调用超时的情况，并且超时请求的seq在服务提供端无法找到，打开dubbo日志，会看到如下莫名其妙的连接被断开的日志
```
Close old netty channe xxxxx on create new netty channel xxx
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[south span-100]
阅读dubbo代码发现，dubbo每隔2s钟检查一次连接情况，如果中间使用有问题的连接（譬如上面这种的被断开的连接），就会出现seq找不到（其实就是没有成功发送出去）而导致的超时，联系运维部署脚本(tcpdump)，发现连接最终是被一个rst包干掉，一般进程都不会发送rst包来断开连接，尝试将问题抽象成一个简单的建立连接问题，在某个机器上面重现，得到下面的结果
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[south span-100]
```
16:58:54.159262 IP 192.168.80.111.34764 > 192.168.80.101.29394: Flags [S], seq 3939000581, win 2920, options [mss 1460], length 0
16:58:54.159323 IP 192.168.80.101.29394 > 192.168.80.111.34764: Flags [S.], seq 3097711187, ack 3939000582, win 2920, options [mss 1460], length 0
16:58:54.159338 IP 192.168.80.111.34764 > 192.168.80.101.29394: Flags [.], ack 1, win 2920, length 0
16:58:55.358435 IP 192.168.80.101.29394 > 192.168.80.111.34764: Flags [S.], seq 3097711187, ack 3939000582, win 2920, options [mss 1460], length 0
16:58:55.358441 IP 192.168.80.111.34764 > 192.168.80.101.29394: Flags [.], ack 1, win 2920, length 0
16:58:57.358422 IP 192.168.80.101.29394 > 192.168.80.111.34764: Flags [S.], seq 3097711187, ack 3939000582, win 2920, options [mss 1460], length 0
16:58:57.358431 IP 192.168.80.111.34764 > 192.168.80.101.29394: Flags [.], ack 1, win 2920, length 0
16:59:54.158495 IP 192.168.80.111.34764 > 192.168.80.101.29394: Flags [.], ack 1, win 2920, length 0
16:59:54.158549 IP 192.168.80.101.29394 > 192.168.80.111.34764: Flags [R], seq 3097711188, win 0, length 0
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[south span-100]
连接发起端
```
[dc@ym_AS_54_080111 connect]$ ss -tn | grep 29394 | wc -l
1000
```
连接接收端
```
[dc@ym_AS_44_080101 ~]$ ss -tn | grep 29394 | wc -l
999
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[south span-100]
可以看到对于客户端来说连接很快就建立起来，但是服务端经过两次最后一个ack重传后依然没办法把连接建立起来（次数是由net.ipv4.tcp_synack_retries 选项决定的），查看所有的相关的网络参数net.core.somaxconn = 262144，net.ipv4.tcp_max_syn_backlog=262144,net.ipv4.tcp_syncookies都已经重新设置成比较大的值
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[south span-100]
大杀器 systemptap
```
probe kernel.statement("tcp_v4_syn_recv_sock@net/ipv4/tcp_ipv4.c:1383"){
    printf("hello world")
}
probe kernel.function("sk_acceptq_removed"){
    printf("hello world")
}
```
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

@snap[south span-100]
参考图和linux代码，最终定位到是下面代码导致了收到最后ack的连接被丢弃掉
```
1257         /* Accept backlog is full. If we have already queued enough
1258          * of warm entries in syn queue, drop request. It is better than
1259          * clogging syn queue with openreqs with exponentially increasing
1260          * timeout.
1261          */
1262         if (sk_acceptq_is_full(sk) && inet_csk_reqsk_queue_young(sk) > 1)
1263                 goto drop;
```
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

@snap[south span-100]
```
static inline int sk_acceptq_is_full(struct sock *sk)
{
    return sk->sk_ack_backlog > sk->sk_max_ack_backlog;
}
```
@snapend


+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[半连接]

@snap[north text-white span-100]
@size[1.5em](猜想和排查)
@snapend

@snap[south span-100]
```
tcp_v4_syn_recv_sock_1383 capturing exec:swapper pid:0 destPort:D272 ack:1 syn:0 seq:681102691 ack_backlog:0 max_ack_backlog:0
```
@snapend

@snap[south span-100]
```
struct sock {
    unsigned short          sk_max_ack_backlog;
}
```
@snapend


