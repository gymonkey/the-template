@title[奇怪的ThreadDump]

## @color[black](奇怪的ThreadDump)

@fa[arrow-down text-black]


+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[奇怪的ThreadDump]

@snap[north text-white span-100]
@size[1.5em](现象)
@snapend

@snap[south span-100]
app_sync在升级之后时不时会出现调用超时，增加gc日志和相应的安全点日志后，发现有如下日志
```
2018-12-10T10:13:13.911+0800: 848743.080: Total time for which application threads were stopped: 14.3280490 seconds
848728.750: ThreadDump                       [    2817          0              3    ]      [     0     0     0     8 14318    ]  0
```
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[奇怪的ThreadDump]

@snap[north text-white span-100]
@size[1.5em](猜想和解决)
@snapend
@snap[south span-100]
CAT的心跳操作?
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[奇怪的ThreadDump]

@snap[north text-white span-100]
@size[1.5em](猜想和解决)
@snapend

@snap[south span-100]
刘伟提醒的dubbo线程池会有自动dump线程的操作，排查后发现就是这个原因引起
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@title[奇怪的ThreadDump]

@snap[north text-white span-100]
@size[1.5em](猜想和解决)
@snapend

@snap[south span-100]
Dubbo调用端为什么使用到线程池？
@snapend

+++?image=template/img/bg/orange.jpg&position=top&size=100% 20%
@snap[south span-100]
![](http://wx1.sinaimg.cn/large/0060lm7Tly1g1wsgb76iij30ld0933ye.jpg)
<br><br>
@snapend

