# LattePanda入手笔记


## 绪论

LattePanda<https://www.lattepanda.com/>一个神奇的板子，它是可以运行Win10系统的单扳机。知道它已经有很多年了。那是因为一个项目，一个独立的电话录音机项目。它采用的是LattePanda作为主控设备，将我们原有的USB接口的电话录音机板子集中到LattePanda，由于它可以运行Win10系统，所以我们原有的windows应用程序可以很容易的移植到这个产品上面来。是我们的产品可以脱离电脑作为一个独立的产品销售。这是我们选择它的一个主要的原因。

做为这个项目，我的主要工作是将所有产品整合到一个盒子里面去。实际的应用程序修改主要有我们英国的同事来完成的。对于LattePanda的使用，我也是最近才接触到。写这个笔记，仅作为一个记录而已。

## 入手

#### LattePanda样机购买

对于LattePanda的购买，我一直搞不明白一些道理。作为一个国内的企业，做的板子在国内买的价格却比国外买的贵，难道它是在国外做的？不管怎么讲，这是人家的价格策略，我们无从说三道四。我们只考虑用还不用，买还是不买就行了。经过一番搜索，发现rs-online上面的价格含税了也比国内的便宜。于是果断入手，在rs-online上买了这块样品。

#### 外壳

最近我设计了一款LattePanda的外壳<https://hnhkj.github.io/blog/hote/lpa/README_cn>，铝合金钣金外壳，拉丝工艺，金色。外观自我感觉还是很漂亮的。

![LattePanda.jpg](img/LattePanda.jpg)

## 使用入门

<docs.lattepanda.com>

#### 开机

正常开机,我是仔细研究了一把,最后得到的结论是,电源.电源电源真的是很重要.电流(2A)要足够,电压(5V)也要足够,缺一不可.真的是汗颜了.搞了这么久,让电源给玩乐一把.<http://docs.lattepanda.com/content/1st_edition/power_on/>. 好了,OK了,可以正常开机了.

红色的LED灯上点后会常亮几秒钟进行初始化,初始化完成后红灯灭掉.这时我们可以按2个小按钮,靠近中间的那一个按钮,然后开机.LattePanda开始运行.期待的显示终于正常了.

##### windows系统下载

以为我的这个机子默认是安装了win10的.可是仔细一看,LattrePanda32 no S/W,是裸机,没有系统.怪不得我的系统进不了win10.安装吧,只能自己安装了.Lattepanda自己有提供win10系统.<http://docs.lattepanda.com/content/1st_edition/os/>.对应自己的板子，下载相应的系统。悲惨了，LattePanda用的是Google的Drive。这让我等不能翻墙的人可怎么下载。脑袋被...踢了？不知道我等是国内小斯.不能干违法的事情吗？一通搜索。终于找到了。这是QQ群里面的一个朋友提供的，非常好，各个版本都有了。解决了我的燃眉之急。在此谢谢cccccc朋友了！

```
Win10 Home系统

请先确认主板型号（详细比对排母侧边贴纸上的编号）
S70CR110 z8300 2/32 
链接：https://pan.baidu.com/s/1rt1rteV7MP5NdL89LZ2PPQ 密码：hu9b

S70CR110 z8300 4/64
链接：https://pan.baidu.com/s/1m6UCZger_C-o4JDai2Fodw 密码：brwk

S70CR110 z8350 2/32
链接：https://pan.baidu.com/s/1q44inrYMI1nwR2FLGo1-BA 密码：8btc

S70CR110 z8350 4/64
链接：https://pan.baidu.com/s/187P3PxJDkNjOBNFQFXnG6Q 密码：4lxx

S70CR200 z8350 2/32 & 4/64
链接：https://pan.baidu.com/s/1UVVBgh4MpdvlpR70YmDu7g 密码：eorq



最新版本bios 

请先确认主板型号（详细比对排母侧边贴纸上的编号）
https://github.com/LattePandaTeam/LattePanda-Win10-Software/tree/master/Bios


驱动包
https://github.com/LattePandaTeam/LattePanda-Win10-Software/tree/master/Drivers
```

#### 系统安装

