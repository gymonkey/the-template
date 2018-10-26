@title[改造后dubbo的使用]

## @color[black](改造后dubbo的使用)

@fa[arrow-down text-black]


+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[Header Bar + Image Body]

@snap[north text-white span-100]
@size[1.5em](依赖)
@snapend
<br><br>
```
<dependency>
  <groupId>fm.lizhi.commons</groupId>
  <artifactId>lz-commons-serviceclient</artifactId>
  <version>1.0.0-SNAPSHOT</version>
</dependency>
```

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[dubbo的架构]

@snap[north text-white span-100]
@size[1.5em](dubbo的架构)
@snapend

@snap[south span-100]
![](http://wx4.sinaimg.cn/mw690/0060lm7Tly1fwdp9x22o7j30ci08cdg2.jpg)
<br></br>
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[与现有服务框架的异同]

@snap[north text-white span-100]
@size[1.5em](与现有服务框架的异同)
@snapend

@snap[south span-100]
@ol[bullet-green](false)
- 不再强依赖一个中心节点
- 更加灵活的路由、负载均衡策略（全都集中在客户端）
- 更加完善的服务治理工具
- 更加强悍的插件化功能
@olend
<br><br>
@snapend


