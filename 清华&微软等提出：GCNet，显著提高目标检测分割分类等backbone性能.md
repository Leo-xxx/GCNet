## 清华&微软等提出：GCNet，显著提高目标检测/分割/分类等backbone性能

张凯 [CVer](javascript:void(0);) *今天*

点击上方“**CVer**”，选择加"星标"或“置顶”

重磅干货，第一时间送达![img](https://mmbiz.qpic.cn/mmbiz_jpg/ow6przZuPIENb0m5iawutIf90N2Ub3dcPuP2KXHJvaR1Fv2FnicTuOy3KcHuIEJbd9lUyOibeXqW8tEhoJGL98qOw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

> 作者：张凯
>
> https://zhuanlan.zhihu.com/p/65776424
>
> 本文已授权，未经允许，不得二次转载

**背景**

**《GCNet: Non-local Networks Meet Squeeze-Excitation Networks and Beyond》**是最近挂在arxiv上的论文，作者Yue Cao来自于微软和清华，另外三作Han Hu也是大名鼎鼎realation network的一作。目前代码已经开源在mmdetection上，非常赞。

![img](https://mmbiz.qpic.cn/mmbiz_png/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNuKOSkyZ56sJheuSFXfPicZDEhbEr15nlWr07fxbJWwxAqMVMPCkuMYXg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

arXiv：https://arxiv.org/abs/1904.11492

github：https://github.com/xvjiarui/GCNet

**一、研究动机**

本文首先是从Non-local Network的角度出发，发现对于不同位置点的attention map是几乎一致的，说明non-local中每个点计算attention map存在很大的计算浪费，从而提出了简化的NL，也就是SNL。关于这点，似乎有较大的争议，从论文本身来看，实验现象到论证过程都是完善的，但是有人在github项目中指出 OCNet和DANet两篇论文中的结论是attention map在不同位置是不一样的，似乎完全相关，作者目前也没有回复。这两篇论文我没有读过，所以在这里只按照本文的逻辑来。

更进一步地，作者还研究了和SENet的关联，基于SENet和SNL，提出了统一的框架，并结合两者优点提出了GCNet，计算量相对较小，又能很好地融合全局信息。

**二、具体方法**

首先是对Non-local Network的分析：

![img](https://mmbiz.qpic.cn/mmbiz_png/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNuTicDqXNCoXUIY3V6xeVJtByHpmpQbAASVmtDdy5IK2PcZxpRAUE8zvg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

左图是Non-local的示意图，其公式如下：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNu0t5RSZIEPt6JxTquVRG636vp5AqPiaicdPUJ8JYQ62tPjbxP0yGFL3iaw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

对其中的attention map进行可视化，也就是：

![img](https://mmbiz.qpic.cn/mmbiz_png/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNuPicFBCus63sOnXWY7nDwUqFOHP9E9zdcXPJSicLb7Oibergz3A7jcmwfg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

最后发现对于图像中不同位置计算的attention map几乎一致：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNu3QPlQHN0nFUibqzhvAUvE9k2chnickXz2ic5w4neaCBXKHtvL2kLiblAicQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

作者还统计了统计意义上的距离，对于不同的Non-local network采用的函数：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNu0V9LrVo9ehHn5N6LwUwTUePYAib5cYrh6lIIhPatyFGqb8asWkJtpDA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以看到，大部分情况下，attention map之间的余弦距离都很小，但是采用Gaussian下attention map的余弦距离还是有点大的，这个不知道是不是OCNet和DANet两篇论文的所述的情况。但整体上看，output最后又是非常一致的。

所以作者直接进行了简化：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNuB82rnDetYEnVskiaozyEqKQWfvkAgL2tM4p1icmfCmMAnRzEPK4fWF2Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

所有的位置共享一个attention map就可以了，计算量降为原来的 ![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==) 。

进一步地，作者将SNL抽象成了全局上下文信息模块，如下图的（a）。

![img](https://mmbiz.qpic.cn/mmbiz_png/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNuib1bClHic3zpsNhiaB7rOggsyyjJYbkp2N6nj3pIUCn94G8QQYOwHVW0g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

全局上下文信息模块可以表达为：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNubgickoEuI9mGXHBKlh7OFbzbOgfPL6Wkf3KEGkmHiaSfX7CppibeMzDdg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

并且说明了SNL和SE都是其一种特殊形式，并结合两者优点，提出了d，GC block，并有以下优点：

1）相比于SNL，SNL中的transform的1x1卷积在res5中是2048x1x1x2048，其计算量较大，所以借鉴SE的方法，加入压缩因子，为了更好的优化，还加入了layernorm。

2）相比于SE，一方面是提取的全局信息更加充分（其实在后续的实验中说服力不是很强，单独avg pooling+add，只掉了0.3个点，但是更加简洁），另一方面则是加号和乘号的区别，而且在实验结果上，加号比乘号有显著的优势。

**三、实验结果**

因为个人目前做检测方向，所以贴的主要是检测的性能。

![img](https://mmbiz.qpic.cn/mmbiz_png/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNuyAibsJcZzrxiaZU6SdWHgFGd7MsIia7fRd5ibib16g1VUOnd56yT2AUqJAA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNu6BgjmX3ibLe61avdxwuvPaZDLib3pbENgYmYNOYLBHQgP8uq0PutkPMQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从消融实验看，



1）GC比SNL有优势，并且stage3-5（ResNet50中也就是13个block）都加入GC，提升最大，有2.2个点的提升，还是非常明显的。

2）从d的实验看到，在压缩的过程中加入LN和RELU，可以提升性能，做到压缩不掉点。

3）融合中add的作用非常有效，f的实验说明add比scale有将近1个点的提升。

最后的检测性能在不同的backbone中加入均有提升：

![img](https://mmbiz.qpic.cn/mmbiz_png/yNnalkXE7oU0ib4Kc56UeiaBnDZRerAFNuRuZyES7BEDGo9A5SfUEbtYTPxpV10NpI7tWCZ6WwylTT31A2nNXibyQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以说在非常强的backbone X101+DCN上依然有0.7个点的提升，最后做到48.4，可以说是非常有用的技巧了。

**四、总结分析**

优点：论文从实验现象到论证过程，再到抽象模型都写得很有逻辑。实验结果也表明该技巧也非常的有用。目前在RetinaNet中加入了该trick，也有1.5个点的提升（没有用sbn），说明还是非常有效，会继续跟踪。

缺点：有其他人指出其他论文提供了似乎相反的结论，吃瓜观望中。



**CVer-目标检测交流群**



扫码添加CVer助手，可申请加入**CVer-目标检测交流群。****一定要备注：目标检测+地点+学校/公司+昵称**（如目标检测+上海+上交+卡卡），不根据格式申请，一律不通过。

![img](https://mmbiz.qpic.cn/mmbiz_png/yNnalkXE7oWwGaQHYUGCaoicqoQQalGZXe6jnkb9FIicAvM7PslNXrjExITE9dAMibnWkiaTH5e5MNVMVKfiavI2ibsw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

▲长按加群



这么硬的**论文精读**，麻烦给我一个在**在看**



![img](https://mmbiz.qpic.cn/mmbiz_png/e1jmIzRpwWg3jTWCAZ4BrnvIuN20lLkhIjtg4GRSDhTk9NpeF0GGTJwUpKPatscIQU7Ndj9hgl8BPpGj2BJoFw/640?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

▲长按关注我们

**麻烦给我一个在看****！**

[阅读原文](https://mp.weixin.qq.com/s?__biz=MzUxNjcxMjQxNg==&mid=2247489354&idx=2&sn=844ae1a79b5b373cffbc86eb4f013bd9&chksm=f9a265c5ced5ecd3526dbd3f13d88f38aa20530fa0cfe37807a16dc3fb612689147d43217b37&mpshare=1&scene=1&srcid=052048J1AJG8pFZF6zobH3dY&key=cfad420b0c7e89f9255222a533cfba76275f9fe8e8a00b43ad62ccfef7e6721a06f4b534e407b207bdff9bddfb48dd41831483b7c230261ed9dbf9f6c1a8fcd29d82cdcf37e70dedb423913e5d557742&ascene=1&uin=MjMzNDA2ODYyNQ%3D%3D&devicetype=Windows+10&version=62060739&lang=zh_CN&pass_ticket=UJ%2F%2Ffah4GKdcUykejNYGZrwkK6SmeEeT0q2V2A5zEGJdJNEqt1yLbUmAzkH4L7m%2F##)





微信扫一扫
关注该公众号