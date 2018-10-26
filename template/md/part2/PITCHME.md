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

@snap[south span-100]
启动类与现在对比，没有改动会默认使用lz-dubbo-config配置，加载默认配置，如果有必要可以启动参数-Ddubbo.config.key来修改配置,可以通过在ServiceProvider注解指定version和group的值，否则使用1.0.0和default作为默认值
@snapend
---?gist=gymonkey/39c5a4bcf637bfecf74a9e597cb2a490&lang=java&color=white&title=%E6%8F%90%E4%BE%9B%E8%80%85%E4%BE%8B%E5%AD%90
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


