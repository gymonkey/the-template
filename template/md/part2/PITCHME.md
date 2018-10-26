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
@size[1.5em](服务提供者)
@snapend
```
@ServiceProvider(group="group",version="version")
public class EchoServiceImpl implements EchoService {

}
```

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[与现有服务框架的异同]

@snap[north text-white span-100]
@size[1.5em](服务调用者)
@snapend
```
	ProxyBuilder proxyBuilder = new ProxyBuilder();
		proxyBuilder.init(new DataCenterServiceConsumerCodec(), "dc_proxy_office", 1, false);
		EchoService echoService = proxyBuilder.buildProxy(EchoService.class, "1.0.0", "default");
```

@snapend


