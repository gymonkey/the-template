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
    112.126.56.67.29394 > 172.21.108.90.46562: Flags [.], cksum 0x6379 (correct), seq 19393521:19394981, ack 19315231, win 64240, length 1460
    112.126.56.67.29394 > 172.21.108.90.46562: Flags [.], cksum 0x5dc5 (correct), seq 19394981:19396441, ack 19315231, win 64240, length 1460
    112.126.56.67.29394 > 172.21.108.90.46562: Flags [.], cksum 0x0bf9 (correct), seq 19396441:19397901, ack 19315231, win 64240, length 1460
    112.126.56.67.29394 > 172.21.108.90.46562: Flags [.], cksum 0x525d (correct), seq 19397901:19399361, ack 19315231, win 64240, length 1460
    112.126.56.67.29394 > 172.21.108.90.46562: Flags [.], cksum 0x4ca9 (correct), seq 19399361:19400821, ack 19315231, win 64240, length 1460
    112.126.56.67.29394 > 172.21.108.90.46562: Flags [.], cksum 0x46f5 (correct), seq 19400821:19402281, ack 19315231, win 64240, length 1460
```
