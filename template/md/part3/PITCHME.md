@title[我们对dubbo的改造]

## @color[black](我们对dubbo的改造)

@fa[arrow-down text-black]


+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[Header Bar + Image Body]

@snap[north text-white span-100]
@size[1.5em](兼容方案)
@snapend

@snap[south span-100]
![](http://wx1.sinaimg.cn/mw690/0060lm7Tly1fwp94yjmnaj30tn0cfaaf.jpg)
<br><br>
@snapend

+++
![](http://wx2.sinaimg.cn/mw690/0060lm7Tly1fwdkup8d7yj30p00iqjx2.jpg)
<br><br>

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[与现有服务框架的异同]

@snap[north text-white span-100]
@size[1.5em](兼容现有机制和灰度)
@snapend

@snap[south span-100]
@ol[bullet-green](false)
- config 配置层：封装获取默认配置，自定义配置的覆盖(LzDubboConfigLoader)
- proxy 服务代理层：兼容基于domain-op的调用方式（LzDubboProxyFactory）
- protocol 远程调用层：兼容现有的filter机制（LzDubboProtocol）
@olend
<br><br>
@snapend
+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[配置]

@snap[north text-white span-100]
@size[1.5em](兼容现有机制和灰度)
@snapend

@snap[south span-100]
@ol[bullet-green](false)
- transport 网络传输层：兼容目前domain-op的调用方式，传输domain、op、request-response的protobuf对象（LzDubboTransporter）
- container 容器层：兼容guice容器（LzDubboContainer）
- registry 注册中心层：记录已提供的dubbo服务（LzDubboRegisty）
@olend
<br><br>
@snapend
