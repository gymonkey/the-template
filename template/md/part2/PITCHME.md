@title[超长的STW]

## @color[black](超长的STW)

@fa[arrow-down text-black]

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](现象)
@snapend

@snap[south span-100]
重启某些量比较大的appserver时总会出现调用超时的情况，日志如下<br>
调用端
```
Waiting server-side response timeout. start time: 2019-01-10 16:23:30.702, end time: 2019-01-10 16:23:36.702, client elapsed: 0 ms, server elapsed: 6000 ms, timeout: 3000 ms, request: Request [id=51, version=2.0.2, twoway=true, event=false, broken=false, data=RpcInvocation [methodName=joinPopularityGroupWithParams, parameterTypes=[class fm.lizhi.live.popularity.guard.protocol.LivePopularityGuardProto$JoinPopularityGroupParams], arguments=[liveId: 5019990958483727926
```
提供端
```
2019-01-10 16:23:30`request`host=192.168.80.143:45366`cliAddr=192.168.80.143:45366`cliId=0`requestTime=1547108610702`ver=1`seq=51`domain=629`op=203`liveId=5019990958483727926`deviceId=864291037367117`data=400(bytes)
2019-01-10 16:23:30`response`host=192.168.80.143:45366`cliId=192.168.80.143:45366`t=0`ver=1`seq=51`domain=629`op=203`generalRCode=0`rCode=0`liveId=5019990958483727926`deviceId=864291037367117`data=8(bytes)
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[south span-100]
日志打印出现大概3s的延时，一般对于这种日志卡顿的情况都会怀疑stw的问题，打开安全点日志和gc日志，发现出现问题前后，有如下的情况<br>
```
554.585: no vm operation                  [    3508          0              9    ]      [     0   136  6630     0     0    ]  0
```
问题来了，为什么spin + block >> sync
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[south span-100]
开始时间戳(t1)->进行一些判断，利用mprotect让执行本地代码进入安全点，利用一个全局的page让执行编译代码的线程进入安全点->记录spin开始时间(t2)，自旋等待所有线程响应安全点->记录spin结束时间(t3)，打印spin使用时间(t3-t2)->阻塞等待所有线程停止，记录时间戳（t4），打印block花费时间(t4-t3)，打印sync花费时间(t4-t1)
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[south span-100]
大杀器1: strace
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[south span-100]
```
14:55:07.124843 mprotect(0x7f416b11f000, 4096, PROT_READ|PROT_WRITE) = 0 <7.641791>
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[south span-100]
玄学
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[south span-100]
继续排查，发现连接有这样一段注释
```
// On some platforms such as Solaris & Linux, the time duration of the page
// permission restoration is observed to be much longer than expected  due to
// scheduler starvation problem etc. To avoid the long synchronization
// time and expensive page trap spinning, 'SerializePageLock' is used to block
// the mutator thread if such case is encountered. See bug 6546278 for detail
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[south span-100]
怀疑是直接操作内存有关系，查看zbx，发现启动或者切流量的时候，都会有占用内存暴增的情况，继续阅读dubbo源代码，发现dubbo会直接使用池化的堆外内存，会导致进程预先申请大块内存，改成使用非池化的堆内内存，有效缓解了这种情况
@snapend

