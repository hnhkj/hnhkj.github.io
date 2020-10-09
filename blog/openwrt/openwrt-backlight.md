# backlight

## dts configure

reference <LINUX>/arch/arm/boot/dts/imx6qdl-gw54xx.dtsi

```
	backlight {
		compatible = "pwm-backlight";
		pwms = <&pwm 3 5000000>;
		brightness-levels = <0 4 8 16 32 64 128 255>;
		default-brightness-level = <6>;
	};
```

## source code

<LINUX>/drivers/video/backlight/pwm_bl.c

#### CONFIG_OF 1

This config define in <LINUX>/include/generated/autoconfig.h default 1.




## reference

#### Backlight背光子系统

<https://blog.csdn.net/JerryGou/article/details/80219479?utm_medium=distribute.pc_relevant.none-task-blog-utm_term-3&spm=1001.2101.3001.4242>

#### 认识一下MT7688的PWM <https://widora.io/pwm>

```
认识一下MT7688的PWM

Widora默认提供了2路硬件PWM，与GPIO模拟成的PWM相比最大区别就是运行时不占用CPU，可输出高频率、高精度PWM。最多可提供4路（另外两路与UART2复用IO，启用的话需要修改DTS）。
参数
最高频率 	调节精度
40MHz 	1-bit
20MHz 	2-bit
10MHz 	3-bit
5MHz 	4-bit
2.5MHz 	5-bit
1.25MHz 	6-bit
625Khz 	7-bit
操作

用户空间操作方法如下：

1、进入/sys/class/pwm/pwmchip0/目录

# cd /sys/class/pwm/pwmchip0 

2、导出pwm0和pwm1

# echo 0 > export 
# echo 1 > export 

3、再看此目录，会发现pwm0和pwm1目录，拿操作pwm0为例

# cd pwm0 
# ls 
duty_cycle enable period polarity uevent 

4、duty_cycle是高电平时间，单位ns；period是PWM波形周期，单位ns。例如产生一个20K，50% 占空比波形，操作如下：

# echo 1 > enable 
# echo 50000 > period 
# echo 25000 > duty_cycle 

支持4路硬件PWM的dts更改方法

此修改是OpenWrt源码
进入/target/linux/ramips/dts/ 打开Widora.dts （根据你的平台选择Widora.dts还是Widora32m.dts）
在pinctrl段增加uart2方法 ，例如在36行开始增加如下：

uart2 { 
ralink,group = “uart2”; 
ralink,function = “pwm”; 
}; 

保存完重新编译即可

```

<https://blog.csdn.net/u014769372/article/details/103072547>


#### Openwrt：基于MT7628/MT7688的PWM驱动

<https://blog.csdn.net/u010632165/article/details/75196071>