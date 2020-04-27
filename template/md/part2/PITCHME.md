@title[TCP在长肥网络下的传输]

## @color[black](TCP在长肥网络下的传输)

@fa[arrow-down text-black]


+++?
### @color[black](一张网上流传的图)

+++?
@title[TCP在长肥网络下的传输]

![](https://s2.ax1x.com/2020/01/16/lvyKfS.png)

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[TCP在长肥网络下的传输]

@snap[north text-white span-100]
@size[1.5em](测试的拓扑结构)
@snapend

```
ALI_BJ <--（100Mb/s，几乎没有丢包，rtt：80ms）-->ALI_SG
```

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[TCP在长肥网络下的传输]

@snap[north text-white span-100]
@size[1.5em](测试场景)
@snapend

- 使用netty实现一个简单的echo-server和echo-client
- 拥塞算法使用cubic
- 尽量保证请求和响应贴近传输时间
- 观察当时发送时的带宽

+++?
### 测试结果

+++?
![](https://s2.ax1x.com/2020/01/16/lvWqNn.png)

+++?

### 为什么会出现这样结果，明明还有很充足的带宽，为什么不能完全利用？

+++? 

影响tcp发送速度一些因素
- 应用产生数据的速度
- 拥塞窗口的大小
- 滑动窗口的大小

+++?

### 应用产生数据的速度

+++?

*tcpdump*
```
    112.126.56.67.29394 > 172.21.108.90.46562: win 64240
    112.126.56.67.29394 > 172.21.108.90.46562: win 64240
    112.126.56.67.29394 > 172.21.108.90.46562: win 64240
    112.126.56.67.29394 > 172.21.108.90.46562: win 64240
    112.126.56.67.29394 > 172.21.108.90.46562: win 64240
    112.126.56.67.29394 > 172.21.108.90.46562: win 64240
```

+++?
*systemtap*
```
1578451603306 sent_packets:0 in_flight:61 cwnd:77
1578451603306 sent_packets:0 in_flight:61 cwnd:77
1578451603306 sent_packets:0 in_flight:61 cwnd:77
1578451603306 sent_packets:0 in_flight:61 cwnd:77
1578451603306 sent_packets:0 in_flight:61 cwnd:77
1578451603306 sent_packets:0 in_flight:61 cwnd:77
```

+++?

### 在拥塞窗口和滑动窗口的都很充足的情况下，居然出现了没有数据发送的情况！

+++?

![](https://s2.ax1x.com/2020/01/16/lv7ITU.png)

+++?
### 会不会是这个发送缓冲区没有新数据“喂”给协议栈，导致了没有新的数据可以发送出去？

+++?

```
channel.writeAndFlush().addListener(ChannelFutureListener() {
	@Override
	public void operationComplete(ChannelFuture future) {
		logger.info(now - sent_time);
	}
});
```

+++?

![](https://s2.ax1x.com/2020/01/17/lxzaJx.png)

+++?
### 结论

+++?

证实是发送缓冲区满了导致没有数据写入，为什么是会没有新的空位腾出来，<br>
因为这里面的数据需要接受到对端的ack后才能被释放掉，rtt越大，这里面的空间释放的速度就越慢，<br>
最终导致了完全被占满，后续的写入延迟越来越大

+++?

**tcp的发送缓冲区的释放速度是和rtt有关系的，长肥网络下的高rtt会导致了缓冲区更容易被占满，从而影响发送速度**

+++?
### 假设存在一个无限大的发送缓冲区后，发送速度是不是就可以同步提升?

+++?
```
1578451603613 sent_packets:0 in_flight:62 cwnd:99
1578451603613 sent_packets:0 in_flight:62 cwnd:99
1578451603613 sent_packets:0 in_flight:62 cwnd:99
1578451603613 sent_packets:0 in_flight:62 cwnd:99
1578451603613 sent_packets:0 in_flight:61 cwnd:64
// 一个增长过程
1578451604445 sent_packets:2 in_flight:79 cwnd:99

```

+++?
```
112.126.56.67.29394 > 172.21.108.90.46560 ack 2070385
112.126.56.67.29394 > 172.21.108.90.46560 ack 2070385
112.126.56.67.29394 > 172.21.108.90.46560 ack 2070385
```

+++？

连续出现了三个dup-ack，导致了拥塞窗口一下子缩小，然后又重新重新花了差不多1秒钟的时间才恢复到原来的大小，因为拥塞控制窗口是受到ack驱动的，所以如果rtt的时间越长，需要恢复的时间就越长

+++?
 
![](https://coolshell.cn/wp-content/uploads/2014/05/tcp.fr_-900x315.jpg)

+++?

**拥塞控制窗口的恢复速度收到rtt的影响，长肥网络下高rtt会导致了发送速度恢复很慢，一旦丢包影响很大，所谓辛辛苦苦三十年，一夜回到解放前**

+++？

### 如果发送缓冲区无限大，我们也处于一个永远不会出错的网络下，是不是就没有问题？

+++？

tcp还会受到滑动窗口大小的影响，滑动窗口也是受到ack的推动往前移动，如果rtt越大，滑动窗口往前移动的速度就越慢

+++？

![](https://coolshell.cn/wp-content/uploads/2014/05/tcpswslide.png)

+++？

**滑动窗口的推进也会受到rtt的影响，长肥网络下高rtt减缓了了滑动窗口的移动，造成了滑动窗口移动变慢，降低了发送的速度**

+++？

### tcp还有问题吗？

+++?

除了这三个问题，tcp还会存在诸如**头部阻塞**等等一系列影响应用程序获取到数据的缺陷