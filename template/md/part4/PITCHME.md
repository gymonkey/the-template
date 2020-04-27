@title[另一种思路-避免长肥网络的出现]

## @color[black](另一种思路-避免长肥网络的出现)

@fa[arrow-down text-black]


+++?

```
Aly-Bj-quic01 <----- rtt:167ms,丢包率：20% ------> Aly-FU-quic01
```

+++?

### 测试场景
- 使用netty实现一个简单的echo-client和echo-server
- 拥塞算法使用cubic
- 尽量保证请求和响应贴近传输时间
- 观察当时发送时的带宽

+++?

![](https://s2.ax1x.com/2020/02/12/1HxnF1.png) 

+++?

```
Aly-Bj-quic01 <--------> Aly-SG-quic01 <------------> Aly-FU-quic01 
```

+++?

### 测试场景
- 使用netty实现一个简单的echo-client、echo-server和一个中继节点
- 拥塞算法使用cubic
- 尽量保证请求和响应贴近传输时间
- 观察当时发送时的带宽

+++?

![](https://s2.ax1x.com/2020/02/12/1Hs0MQ.png)

+++?

### 我们身边有应用吗？

+++?

CDN
<br>
ANYCAST
