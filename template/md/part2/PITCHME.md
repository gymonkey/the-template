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
+++?i
![](https://s2.ax1x.com/2020/01/16/lvWqNn.png)
