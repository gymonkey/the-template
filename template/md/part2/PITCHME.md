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

@snap[span-100]
```
    ALI_BJ   <----- 公网（100Mb/s，几乎没有丢包，rtt：80ms）------->    ALI_SG
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[TCP在长肥网络下的传输]

@snap[north text-white span-100]
@size[1.5em](测试场景)
@snapend

@snap[span-100]
### 测试场景
- 使用netty实现一个简单的echo-server和echo-client
- 拥塞算法使用cubic
- 尽量保证请求和响应贴近传输时间
- 观察当时发送时的带宽
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[span-100]
大杀器1: strace
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[span-100]
玄学
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[span-100]
怀疑是直接操作内存有关系，查看zbx，发现启动或者切流量的时候，<br>
都会有占用内存暴增的情况，继续阅读dubbo源代码，发现dubbo会直接使用池化的堆外内存，<br>
会导致进程预先申请大块内存，改成使用非池化的堆内内存，有效缓解了这种情况
@snapend

