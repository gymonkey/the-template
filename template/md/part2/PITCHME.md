@title[TCP在长肥网络下的传输]

## @color[black](TCP在长肥网络下的传输)

@fa[arrow-down text-black]

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[TCP在长肥网络下的传输]

@snap[north text-white span-100]
@size[1.5em](一张网上流传的图)
@snapend

@snap[span-100]
![](https://s2.ax1x.com/2020/01/16/lvyKfS.png)@snapend
+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[span-100]
日志打印出现大概3s的延时，一般对于这种日志卡顿的情况都会怀疑stw的问题，打开安全点日志和gc日志，发现出现问题前后，有可疑日志
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[超长的STW]

@snap[north text-white span-100]
@size[1.5em](排查过程)
@snapend

@snap[span-100]
开始时间戳(t1)-><br/>
进行一些判断，添加标记->记录spin开始时间(t2)，自旋等待所有线程响应安全点-><br/>
记录spin结束时间(t3)，打印spin使用时间(t3-t2)-><br>
阻塞等待所有线程停止，记录时间戳（t4），打印block花费时间(t4-t3)，打印sync花费时间(t4-t1)
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

