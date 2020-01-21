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
