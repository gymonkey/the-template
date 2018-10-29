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

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[我们对dubbo的改造]

@snap[north text-white span-100]
@size[1.5em](我们对dubbo的改造)
@snapend

@snap[south span-100]
![](http://wx2.sinaimg.cn/mw690/0060lm7Tly1fwdkup8d7yj30p00iqjx2.jpg)
<br><br>
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[与现有服务框架的异同]

@snap[north text-white span-100]
@size[1.5em](服务调用者)
@snapend
```
	ProxyBuilder proxyBuilder = new ProxyBuilder();
		proxyBuilder.init(new DataCenterServiceConsumerCodec(), 
            "dc_proxy_office", 1, false);
		EchoService echoService = proxyBuilder.buildProxy(
            EchoService.class, "1.0.0", "default");
```

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[配置]

@snap[north text-white span-100]
@size[1.5em](配置)
@snapend

| 配置项 | 作用 |
|----------|----------|
|dubbo.reference.retries|调用重试次数|
|dubbo.reference.connections|与服务提供者的连接数|
|dubbo.reference.loadbalance|负载均衡策略|
|dubbo.protocol.threads|服务提供者线程数|
|dubbo.protocol.iothreads|io线程数|

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[扩展点]

@snap[north text-white span-100]
@size[1.5em](扩展点)
@snapend

|扩展点|用途|
|---|----|
|RouterFactory|从多个服务提者方中选择一个进行调用|
|LoadBalance|从多个服务提者方中选择一个进行调用|
