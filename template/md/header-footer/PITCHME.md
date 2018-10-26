@title[dubbo的基础介绍，与现在服务框架区别]

## @color[black](dubbo的基础介绍<br>与现在服务框架区别)

@fa[arrow-down text-black]

@snap[south docslink span-50]
[The Template Docs](https://gitpitch.com/docs/the-template)
@snapend


+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[Header Bar + Image Body]

@snap[north text-white span-100]
@size[1.5em](当前服务框架的架构)
@snapend

@snap[south span-100]
![](http://wx1.sinaimg.cn/mw690/0060lm7Tly1fwlngvse1tj318w0leqby.jpg)
<br><br>
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[dubbo的架构]

@snap[south text-white span-100]
@size[1.5em](dubbo的架构)
@snapend

@snap[north span-100]
<br>
![dubbo的架构](http://wx4.sinaimg.cn/mw690/0060lm7Tly1fwdp9x22o7j30ci08cdg2.jpg)
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[dubbo的架构]

@snap[north text-white span-100]
@size[1.5em](dubbo的架构)
@snapend

@snap[south span-100]
@ol[bullet-green](false)
- 服务容器负责启动，加载，运行服务提供者
- 服务提供者在启动时，向注册中心注册自己提供的服务
- 服务消费者在启动时，向注册中心订阅自己所需的服务
- 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者
- 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用
- 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心
@olend
<br><br>
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


