---
title: "一点一滴分析LinkIt Smart 7688 问题汇总"
author: "hnhkj@163.com"
date: "2016-6-28"
output: html_document
---

[TOC]

* [1.系统编译](#1)
    * [1.1 .config文件](#1.1)
        * [1.1.1 如何创建config diff文件](#1.1.1)
        * [1.1.2 如何使用config diff文件](#1.1.2)
    * [1.2 编译](#1.2)
        * [1.2.1 linux编译](#1.2.1)
        * [1.2.2 package编译（以Madplay为例）](#1.2.2)        
    * [1.3 dts文件修改](#1.3)
        * [1.3.1 按钮相关修改](#1.3.1)
        * [1.3.2 SD卡检测引脚电平修改](#1.3.2)
    * [1.4 package创建](#1.4)    
* [2.系统配置](#2)
    * [2.1 MTD操作](#2.1)
    * [2.2 用SD卡扩展空间](#2.2)
    * [2.3 ifconfig 相关参数](#2.3)
    * [2.4 network 配置](#2.4)
    * [2.5 eeprom配置](#2.5)
    * [2.6 稍描wifi热点](#2.6)
* [3. openwrt application](#3)
    * [3.1 opkg](#3.1)
* [4. linux application](#4)
    * [4.1 openssh-service](#4.1)
    * [4.2 vnc server](#4.2)
    * [4.3 git](#4.3)
    * [4.4 串口通讯](#4.4)
    * [4.5 scp命令](#4.6)
* [5. Uboot](#5)    


* [附录](#附录)


## 定义

**$ - 指定Ubuntu系统下命令**

**# - Openwrt下命令**



<h4 id="1"></h4>
## 1. 系统编译

<h4 id="1.1"></h4>
#### 1.1 .config文件

openwrt中`make menuconfig`生成.config文件后，我们如何对.config中自定义的差异内容进行进行备份，方便移植到其它的系统中，这是一个问题。当然，有人说有很多简单的方法。但是这些都不是Openwrt开发着所希望看到的。对于Openwrt，开发团队创建了简单的工具`scripts/diffconfig.sh`。我们可以采用这个工具进行配置保存工作。

有一个简单的方法，生成diff文件，然后通过git进行操作，这样我们可以对我们自己的openwrt进行定制备份了。

<h4 id="1.1.1"></h4>
**1.1.1 如何创建config diff文件**

```
  $./scripts/diffconfig.sh > config.diff
```

<h4 id="1.1.2"></h4>
**1.1.2 如何使用config diff文件**

```
  $cp config.diff .config
  $make defconfig
```

或者

```sh
$cat config.diff >> .config
$make defconfig
```

**1.1.3 make meunconfig**

```sh
$ make prereq
$ make menuconfig
```

**1.1.3 make kernel_menuconfig**

```sh
$ make kernel_menuconfig
```

<h4 id="1.2"></h4>  
#### 1.2 编译

系统编译很简单，直接在系统目录下运行`make`命令就可以了，如果想查看输出信息，可以在`make`后面增加`V=s/V=99`。如果是多核系统，可以在后面再增加`j=2`或其它数字，这代表同时有多个线程同时运行。这样可以提高编译速度。我的系统是单核的，我验证了一下没有任何改善。多核系统可以测试一下。
```
 $ make V=99
```

<h4 id="1.2.1"></h4>  
#### 1.2.1 linux核心编译

如果我们仅仅是想对linux核心包进行编译，可以采用下面的命令来进行。

```
 $ make target/linux/{clean,prepare} V=s QUILT=1
```


<h4 id="1.2.2"></h4>  
#### 1.2.2 package编译（以Madplay为例）

<http://jphome.github.io/blog/2014/03/29/openwrt_sdk.html>

**全新编译madplay**
```
  $ make package/feeds/packages/madplay/{clean,compile,install} V=s
  $ make package/feeds/packages/madplay/{compile,install} V=s
```

**重新编译madplay**

```
$ make package/feeds/packages/madplay/compile V=s
```

<h4 id="1.2.3"></h4>  
#### 1.2.3 package安装(以Madplay为例)

**madplay安装**

复制ipk文件到openwrt系统(采用scp命令)，然后通过opkg进行安装。

```sh
$ scp bin/ramips/packages/packages/madplay-alsa_0.15.2b-4_ramips_24kec.ipk root@192.168.1.168:/tmp
```

安装madplay到openwrt

```sh
$ root@mylinkit:/tmp# opkg install madplay-alsa_0.15.2b-4_ramips_24kec.ipk
Installing madplay-alsa (0.15.2b-4) to root...
Configuring madplay-alsa.
```

**MadPay源码修改**

```sh
$ wget ftp://ftp.mars.org/pub/mpeg/madplay-0.15.2b.tar.gz
```

得到madplay源码，修改player.c，采用FIFO控制方式，然后编译madplay.

```
$ make package/feeds/packages/madplay/{compile,install} V=s
$ scp bin/ramips/packages/packages/madplay-alsa_0.15.2b-4_ramips_24kec.ipk root@192.168.1.168:/tmp

# opkg install madplay-alsa_0.15.2b-4_ramips_24kec.ipk
# madplay /Media/USB-A1/*.mp3
# echo f > /tmp/.madplayFIFO
```

创建madplayCon.c文件

```sh
root@ethMOH# make package/feeds/packages/gogoo/ethMOH/{clean,compile,install} V=s
```

```sh
  KEY_PAUSE    = 'p', 
  KEY_STOP     = 's',
  KEY_FORWARD  = 'f',
  KEY_BACK     = 'b',
  KEY_TIME     = 't',
  KEY_QUIT     = 'q',
  KEY_INFO     = 'i',
  KEY_GAINDECR = '-',
  KEY_GAININCR = '+',
  KEY_GAINZERO = '_',
  KEY_GAININFO = '='
```

<h4 id="1.3"></h4>  
#### 1.3 dts文件修改

<h4 id="1.3.1"></h4>
**1.3.1 按钮相关修改**

MT7688按钮一组为32个，所以GPIO0组对应GPIO0-PIO32，GPIO1组对应GPIO32以上的引脚。

参考连接<https://wiki.openwrt.org/doc/howto/hardware.button?s[]=button&s[]=hotplug>

```
	gpio-keys-polled {
		compatible = "gpio-keys-polled";
		#address-cells = <1>;
		#size-cells = <0>;
		poll-interval = <20>;
		wps {
			label = "wps";
			gpios = <&gpio1 6 1>;     // GPIO38 wifi button
			linux,code = <0x211>;
		};
		key_playpause {
			label = "key_playpause";
			gpios = <&gpio0 15 1>;		// GPIO15-KEY_PLAY/Pause(S5)
			linux,code = <164>;
		};
		key_volumeup {
			label = "key_volumeup";
			gpios = <&gpio0 17 1>;		//GPIO17-KEY_VOL+(S7)
			linux,code = <115>;
		};
		key_volumedown {
			label = "key_volumedown";
			gpios = <&gpio0 18 1>;		//GPIO18-KEY_VOL-(S8)
			linux,code = <114>;
		};
		key_next {
			label = "key_next";
			gpios = <&gpio0 16 1>;		//GPIO16-KEY_NEXT(S6)
			linux,code = <0x197>;
		};
		key_previous {
			label = "key_previous";
			gpios = <&gpio0 14 1>;		//GPIO14-KEY_PRE(S4)
			linux,code = <0x19c>;
		};
	};

```

**尖括号中的内容为标准按键代码，可以在linux/include/uapi/linux/input.h中查询到。**

<h4 id="1.3.2">1.3.2 SD卡检测引脚电平修改</h4>  

LinkIt smart7688的SD卡检测，默认是高电平。但是普通的SD卡是低电平。所以，要对dts进行修改。使用下面命令：

```sh
$ vi target/linux/ramips/dts/LINKIT7688.dts
```

修改前：
```
   	sdhci@10130000 {
		status = "okay";
		mediatek,cd-high;
//		mediatek,cd-poll;
	};
```
修改后：
```
		sdhci@10130000 {
		status = "okay";
		mediatek,cd-low;
//		mediatek,cd-poll;
	};
```

<h4 id="1.4"></h4>  
#### 1.4 package创建

**package的Makefile**

* PKG\_NAME - The name of the package, as seen via menuconfig and ipkg
* PKG\_VERSION - The upstream version number that we're downloading
* PKG\_RELEASE - The version of this package Makefile
* PKG\_LICENSE - The license(s) the package is available under, SPDX form.
* PKG\_LICENSE\_FILE- file containing the license text
* PKG\_BUILD\_DIR - Where to compile the package
* PKG\_SOURCE - The filename of the original sources
* PKG\_SOURCE\_URL - Where to download the sources from (directory)
* PKG\_MD5SUM - A checksum to validate the download
* PKG\_CAT - How to decompress the sources (zcat, bzcat, unzip)
* PKG\_BUILD\_DEPENDS - Packages that need to be built before this package, but are not required at runtime. Uses the same syntax as DEPENDS below.
* PKG\_INSTALL - Setting it to "1" will call the package's original "make install" with prefix set to PKG\_INSTALL\_DIR
* PKG\_INSTALL\_DIR - Where "make install" copies the compiled files
* PKG\_FIXUP - See below
* PKG\_SOURCE\_PROTO - the protocol to use for fetching the sources (git, svn)
* PKG\_REV - the svn revision to use, must be specified if proto is "svn"
* PKG\_SOURCE\_SUBDIR - must be specified if proto is "svn" or "git", e.g. "PKG\_SOURCE\_SUBDIR:=$(PKG\_NAME)-$(PKG\_VERSION)"
* PKG\_SOURCE\_VERSION - must be specified if proto is "git", the commit hash to check out
* PKG\_CONFIG\_DEPENDS - specifies which config options depend on this package being selected
* SECTION - The type of package (currently unused) //包的类型
* CATEGORY - Which menu it appears in menuconfig //menuconfig的哪一个菜单显示
* TITLE - A short description of the package  //包的简短描述
* DESCRIPTION - (deprecated) A long description of the package  //报的一个长描述
* URL - Where to find the original software  // 那里找到源软件
* MAINTAINER - (required for new packages) Who to contact concerning the package  // 包的联系
* DEPENDS - (optional) Which packages must be built/installed before this package. See below for the syntax. // 哪些包需要必须先编译/安装
* PKGARCH - (optional) Set this to "all" to produce a package with "Architecture: all"
* USERID - (optional) a username:groupname pair to create at package installation time.

<h4 id="1.5"></h4>  
#### 1.5 JFFS2-Journalling Flash File System Version2

JFFS2全名是 Journalling Flash File System Version2，是Redhat公司开发的闪存文件系统[1]，其前身是JFFS, 最早只支持NOR Flash, 自2.6版以后开始支持NAND Flash, 极适合使用于嵌入式系统。<https://zh.wikipedia.org/wiki/JFFS2>

<h4 id="1.5"></h4>  
#### 1.5 如何创建自己的板子支持

**增加mt7688支持**
参考CC代码：(CC-2015/10/19 18:19:18-SHA-1: 3d98b29a3d0aa854fad712a92ace42ad06e92d6b)
* 修改`target\linux\ramips\Makefile`文件，增加`mt7688`支持
* 修改`target\linux\ramips\modules.mk`文件，增加`TARGET_ramips_mt7688`支持
* 创建`target\linux\ramips\mt7688\config-3.18`文件
* 创建`target\linux\ramips\mt7688\profiles\00-default.mk`文件
* 创建`target\linux\ramips\mt7688\target.mk`文件

**增加LinkIt Smart7688支持**
参考CC代码：(CC-2015/10/19 18:19:22-SHA-1: 8829dee8871e45c675a4bd4036c188c6d0f5645b)
* 在`package\boot\uboot-envtools\files\ramips`添加`linkits7688`板名支持
* 在`target\linux\ramips\base-files\etc\board.d\02_network`添加`linkits7688`支持
* 在`target\linux\ramips\base-files\lib\ramips.sh`中添加`linkits7688`支持
* 在`target\linux\ramips\base-files\lib\upgrade\platform.sh`中添加`linkits7688`支持
* 增加`target\linux\ramips\dts\LINKIT7688.dts`文件
* 修改`target\linux\ramips\image\Makefile`文件
* 增加`target\linux\ramips\mt7688\profiles\01-mediatek.mk`文件


参考windora

* 修改`target\linux\ramips\base-files\etc\board.d\02_network`
* 修改`target\linux\ramips\base-files\lib\ramips.sh`
* 修改`target\linux\ramips\base-files\lib\upgrade\platform.sh`
* 修改`target\linux\ramips\image\Makefile`
* 创建一个\*.mk文件到`target\linux\ramips\mt7688\profiles\`目录下
* 创建一个example.dts文件到`target\linux\ramips\dts\`目录


```sh
root@mylinkit:~# cat /proc/cpuinfo
system type             : MediaTek MT7688 ver:1 eco:2
machine                 : MediaTek LinkIt Smart 7688
processor               : 0
cpu model               : MIPS 24KEc V5.5
BogoMIPS                : 385.84
wait instruction        : yes
microsecond timers      : yes
tlb_entries             : 32
extra interrupt vector  : yes
hardware watchpoint     : yes, count: 4, address/irw mask: [0x0ffc, 0x0ffc, 0x0ffb, 0x0ffb]
isa                     : mips1 mips2 mips32r1 mips32r2
ASEs implemented        : mips16 dsp
shadow register sets    : 1
kscratch registers      : 0
package                 : 0
core                    : 0
VCED exceptions         : not available
VCEI exceptions         : not available
```


<h4 id="1.6">1.6 rpcd -  (OpenWrt ubus RPC backend server)</h4>

<h4 id="1.7">1.7 patch</h4>

<http://blog.csdn.net/wwx0715/article/details/25160361>

```sh
$ quilt --help
用法：quilt [--trace[=verbose]] [--quiltrc=XX] command [-h] ...
       quilt --version
命令是：
        add       fold    new       remove    top
        annotate  fork    next      rename    unapplied
        applied   graph   patches   revert    upgrade
        delete    grep    pop       series
        diff      header  previous  setup
        edit      import  push      shell
        files     mail    refresh   snapshot

Global options:

--trace
        Runs the command in bash trace mode (-x). For internal debugging.

--quiltrc file
        Use the specified configuration file instead of ~/.quiltrc (or
        /etc/quilt.quiltrc if ~/.quiltrc does not exist).  See the pdf
        documentation for details about its possible contents.  The
        special value "-" causes  

--version
        Print the version number and exit immediately.
```

**1.7.1 准备quilt配置**

```
$ cd ~
```

```
cat > ~/.quiltrc <<EOF
QUILT_DIFF_ARGS="--no-timestamps --no-index -p ab --color=auto"
QUILT_REFRESH_ARGS="--no-timestamps --no-index -p ab"
QUILT_PATCH_OPTS="--unified"
QUILT_DIFF_OPTS="-p"
EDITOR="nano"
EOF
```

**1.7.2**

```sh
$ cd ~/openwrt/openwrt_ethMOH/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/linux-ramips_mt7688/linux-3.18.29/
$ quilt series
$ quilt new platform/502-alsa.patch
$ quilt edit sound/soc/codecs/wm8960.c
$ quilt diff
$ quilt refresh
$ cd ../../../../
$ make target/linux/update package/index V=s
```

修改存在的patch

```sh
$ quilt push platform/502-alsa.patch
$ quilt edit sound/soc/codecs/wm8960.c
$ quilt diff
$ quilt refresh
$ cd ../../../../

$ make target/linux/update V=s  //民间更新patch到target/linux/ramips/patches-xxx方法
/$ make target/linux/update package/index V=s //官方更新patch到target/linux/ramips/patches-xxx方法

$ make target/linux/{clean,prepare} V=s QUILT=1
```


#### 1.8 Makefile

```
$? - 当前目标所依赖的文件列表中比当前目标文件还要新的文件
$@ - 当前目标名字
$< - 当前依赖文件的名字
$* - 不包括后缀名的当前依赖文件的名字
```


#### 1.9 WM8960声卡

Linkit的默认的声卡是WM8960，该声卡为I2S接口。Linkit默认WM8960使用的为外部晶振提供时钟频率。所以如果我们希望采用MT7688的REF_CLK0信号作为WM8960的时钟信号的话，我们就需要对MTK的配置文件进行一些修改。
具体的修改可以参考https://github.com/hnhkj/CC15.05.git的代码。

#### 1.10 USB声卡CM108

https://wiki.openwrt.org/doc/howto/usb.audio?s[]=sound

#### 1.11 wps 功能 (don't support)

<http://labs.mediatek.com/forums/posts/list/4850.page>

#### 1.12 init.d/rc.d开机自动运行

参考文档：<https://wiki.openwrt.org/doc/techref/initscripts>

```
启动方法：
  1，创建脚本到 init.d(具体参考原来文档)。
  2，开机自动运行./etc/init.d/example enable。
  3, 使能自动运行可以在/etc/rc.d/ 下查找到连接
```

#### 1.13 eclipse

<http://downloads.openwrt.org/docs/eclipse.pdf>

<h2 id="2">2. 系统配置</h2>

<h4 id="2.1">2.1 MTD</h4>

参考连接：<https://wiki.openwrt.org/doc/techref/mtd>

```sh
root@mylinkit:~# mtd
Usage: mtd [<options> ...] <command> [<arguments> ...] <device>[:<device>...]

The device is in the format of mtdX (eg: mtd4) or its label.
mtd recognizes these commands:
        unlock                  unlock the device
        refresh                 refresh mtd partition
        erase                   erase all data on device
        verify <imagefile>|-    verify <imagefile> (use - for stdin) to device
        write <imagefile>|-     write <imagefile> (use - for stdin) to device
        jffs2write <file>       append <file> to the jffs2 partition on the device
        fixseama                fix the checksum in a seama header on first boot
Following options are available:
        -q                      quiet mode (once: no [w] on writing,
                                           twice: no status messages)
        -n                      write without first erasing the blocks
        -r                      reboot after successful command
        -f                      force write without trx checks
        -e <device>             erase <device> before executing the command
        -d <name>               directory for jffs2write, defaults to "tmp"
        -j <name>               integrate <file> into jffs2 data when writing an image
        -s <number>             skip the first n bytes when appending data to the jffs2 partiton, defaults to "0"
        -p                      write beginning at partition offset
        -l <length>             the length of data that we want to dump

Example: To write linux.trx to mtd4 labeled as linux and reboot afterwards
         mtd -r write linux.trx linux
```

<h4 id="2.1.1">2.1.1 如何显示MTD状态</h4>

**mylinkit-7688**

```sh
root@mylinkit:/tmp# cat /proc/mtd
dev:    size   erasesize  name
mtd0: 00030000 00010000 "u-boot"
mtd1: 00010000 00010000 "u-boot-env"
mtd2: 00010000 00010000 "factory"
mtd3: 00fb0000 00010000 "firmware"
mtd4: 0011a791 00010000 "kernel"
mtd5: 00e9586f 00010000 "rootfs"
mtd6: 00100000 00010000 "rootfs_data"
```

**openwrt-MT7621**

```sh
root@ipRec:~# cat /proc/mtd 
dev:    size   erasesize  name
mtd0: 00030000 00010000 "u-boot"
mtd1: 00010000 00010000 "u-boot-env"
mtd2: 00010000 00010000 "factory"
mtd3: 00fb0000 00010000 "firmware"
mtd4: 00125169 00010000 "kernel"
mtd5: 00e8ae97 00010000 "rootfs"
mtd6: 00bb0000 00010000 "rootfs_data"

```

<h4 id="2.1.2">2.1.2 dd命令：/bin/dd</h4>  

读取mtd2内的数据内容，mac地址

```
# dd bs=1 skip=3 count=6 if=/dev/mtd2 2>/dev/null | hexdump
```

参考文档：linkit-smart-7688-feed\mtk-linkit\files\etc\uci-defaults\51_linkit_config - Line30:

```
MAC=$(dd bs=1 skip=7 count=3 if=/dev/mtd2 2>/dev/null | hexdump -v -n 3 -e '3/1 "%02X"'
```

<h4 id="2.1.3">2.1.3 入如何写firmware到flash.</h4>

```
   # cd /tmp
   # wget http://www.example.org/original_firmware.bin
   # mtd -r write /tmp/original_firmware.bin firmware
```

<h4 id="2.1.4">2.1.4 备份MTD2/factory信息</h4>

```
  # dd if=/dev/mtd2 of=/tmp/factory.bin
```

<h4 id="2.1.5">2.1.5 写factory.bin到mtd2</h4>

```
  # mtd -r write /tmp/factory.bin factory
```

***注意：***如果命令返回不能写入MTD2，可能是由于你的系统设定了禁止写该区域的权限。我们可以通过修改`target/linux/ramips/dts/LINKIT7688.dts`，注销禁止代码。这样就可以将数据写入到MTD2区域了。

```
I know that we can modify LINKIT7688 file to allow mtd command modify mtd2's data.

/target/linux/ramips/dts/LINKIT7688
line82-86:
factory: partition@40000 {
label = "factory";
reg = <0x40000 0x10000>;
read-only;
};
delete read-only.
```


* 如何安装fw\_printenv,fw_setenv命令 

```
opkg install uboot-envtools
```

* fw\_printenv命令：/usr/sbin/fw_printenv

参考文档：linkit-smart-7688-feed\mtk-linkit\files\etc\init.d\linkit

Line15:

>  SEQ=`fw_printenv -n wifi_seq`


<h4 id="2.2">2.2 用SD卡扩展空间(未验证)</h4>

参考: <http://labs.mediatek.com/forums/posts/list/4121.page>

```
Yes!you can mount tf card as overlay
1.install block-mount e2fsprogs kmod-fs-ext4
2.mkfs.ext4 /dev/mmcblk0
3.block detect > /etc/config/fstab
4.vi etc/config/fstab
modify option 'target' '/overlay'
modify option 'enable' '1'
5.reboot,use df -h check
```


<h4 id="2.3">2.3 ifconfig 相关参数</h4>

**状态：Ethernet0 连接PC，Wifi连接Router (状态非常好)**

```sh
ifconfig
apcli0    Link encap:Ethernet  HWaddr 9E:65:F9:0B:18:55
          inet addr:192.168.1.104  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::9c65:f9ff:fe0b:1855/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

br-lan    Link encap:Ethernet  HWaddr 9C:65:F9:1B:10:27
          inet addr:192.168.100.1  Bcast:192.168.100.255  Mask:255.255.255.0
          inet6 addr: fe80::9e65:f9ff:fe1b:1027/64 Scope:Link
          inet6 addr: fdef:dd3c:f1e1::1/60 Scope:Global
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:478 errors:0 dropped:0 overruns:0 frame:0
          TX packets:340 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:45279 (44.2 KiB)  TX bytes:46562 (45.4 KiB)

eth0      Link encap:Ethernet  HWaddr 9C:65:F9:1B:10:27
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:525 errors:0 dropped:0 overruns:0 frame:0
          TX packets:307 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:70666 (69.0 KiB)  TX bytes:46599 (45.5 KiB)
          Interrupt:5

eth0.1    Link encap:Ethernet  HWaddr 9C:65:F9:1B:10:27
          inet6 addr: fe80::9e65:f9ff:fe1b:1027/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:26 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 B)  TX bytes:5911 (5.7 KiB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:1269 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1269 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:88230 (86.1 KiB)  TX bytes:88230 (86.1 KiB)

ra0       Link encap:Ethernet  HWaddr 9C:65:F9:1B:18:55
          inet6 addr: fe80::9e65:f9ff:fe1b:1855/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:11176 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2457 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2248608 (2.1 MiB)  TX bytes:42335 (41.3 KiB)
          Interrupt:6
```


<h4 id="2.4">2.4 network 配置</h4>

```sh
config interface 'loopback'
    option ifname 'lo'
    option proto 'static'
    option ipaddr '127.0.0.1'
    option netmask '255.0.0.0'

config globals 'globals'
    option ula_prefix 'fd01:80d1:1f98::/48'

config interface 'lan'
    option ifname 'eth0'
    option force_link '1'
    option type 'bridge'
    option proto 'static'
    option netmask '255.255.255.0'
    option ip6assign '60'
    option ipaddr '192.168.100.1'
    option macaddr '00:0c:43:e1:76:2a'

config switch
    option name 'switch0'
    option reset '1'
    option enable_vlan '0'

config interface 'wan'
    option proto 'dhcp'
```

<h4 id="2.5">2.5 eeprom配置</h4>

eepromn内容可以在`/lib/firmware/mt7628.eeprom`中。

```
* 2.1 - Chip ID(0x00h)
* 2.2 - Layout Revision ID(0x02)
* 2.3 - WIFI MAC Address (0x04)
* 2.4 - WIFI MAC Address (0x28)
* 2.5 - WIFI MAC Address (0x2E)
* 2.6 - NIC Configuration 0 (0x34)
* 2.7 - NIC Configuration 1 (0x36)
* 2.8 - Country Region Code for 2.4G band (0x39)
* 2.9 - LED Mode (0x3B)
* 2.10 - NIC COnfiguration 2 (0x42)
* 2.11 - RSSI Offset for 2.4G band (0x46)
* 2.12 - 20M/40M BW Power Delta for 2.4G band (0x50)
* 2.13 - Temp. Sensor Calibation (0x55)
* 2.14 - 2.4G Tx0 Power Slope/offset (0x56~0x57)
* 2.15 - 2.4G Tx0 Target Power (0x58)
* 2.16 - 2.4G Tx0 Power Low/Middle/High Channel (0x59)
* 2.17 - 2.4G Tx1 Power Slope/Offset (0x5C~0x5D)
* 2.18 - 2.4G Tx1 Target Power (0x5E)
* 2.19 - 2.4G Tx1 Power Offset Low/Middle/High Channel (0x5F~0x61)
* 2.20 - 2.4G Tx rate power configuration (0xA0~0xBF)
* 2.21 - External LNA (0xC0)
* 2.22 - 2.4GHz Step Number (0xC6)
* 2.23 - Frequency offset (0xF4~0xF6)
* 2.24 - Reserved for Customer (0x140~0x1EF)
```

<h4 id="2.6">2.6 Sysupgrade</h4>

<https://wiki.openwrt.org/doc/howto/generic.sysupgrade>

与opkg, mtd 等相比, sysupgrade 仅仅是个shell脚本: /sbin/sysupgrade 诣在更容易的实现系统更新.

#### timezone //时区

时区设置保存在/tmp/TZ中。

```sh
root@mylinkit:/tmp# cat TZ
UTC
```

<h4 id="2.7">2.7 uhttpd</h4>

<https://wiki.openwrt.org/doc/howto/http.uhttpd>

<h4 id="2.8">2.8 修改Linkit\_Smart\_7688默认的SSID</h4>

在`mtk-linkit\files\etc\uci-defaults\51\_linkit\_config`文件中，默认是读取wifi MAC address的后3个字节，加载到字符串`LiniIt_Smart_7688_`的后面，根据MAC地址的唯一性，形成一个唯一的SSID地址。如果我们想修改该SSID的话，可以在这个文件中进行修改。又或者配置SSID到uboot环境变量中，形成自己的SSID。这部分实现可以参考`lks7688.cfg`部分。如果系统在uboot中设置了SSID的话，系统将显示该SSID。如果没有设置的话，系统将设置该文件配置的SSID。

```sh
[ -n "${SSID}" ] || { \
  MAC=$(dd bs=1 skip=7 count=3 if=/dev/mtd2 2>/dev/null | hexdump -v -n 3 -e '3/1 "%02X"')
  SSID=LinkIt_Smart_7688_${MAC}
}
```

<h4 id="2.9">2.9 修改Linkit\_Smart\_7688的版本号</h4>

Linkit\_Smart\_7688默认拥有自己的版本号,该版本号定义在`mtk-linkit\files\etc\uci-defaults\54_linkit_version`文件中。

```
uci set system.@system[0].firmware_version=v0.9.3
```

<h4 id="2.10">2.10 修改Linkit\_Smart\_7688的主机名</h4>

Linkit\_Smart\_7688的主机名在`mtk-linkit\files\etc\uci-defaults\51_linkit_config`文件中定义，你可以通过修改这个文件来修改主机名。

```
uci set system.@system[-1].hostname="mylinkIt"
```

<h4 id="2.11">2.11 获得Linkit\_Smart\_7688的MAC地址</h4>

对于如何获取MAC地址，OpenWrt提供了详细的程序接口。可以参考`\openwrt\package\base-files\files\lib\functions\system.sh`文件，该文件包含的mac的操作函数。在很多的sh文档中都包含的对该文件的引用。

例如：`mtk-linkit\files\etc\uci-defaults\51_linkit_config`文件中的`wan_mac`获取。

```
wan_mac=$(mtd_get_mac_binary factory 4)
```


获取ap_mac地址（源自：mtk-linkit\files\etc\rc.button\wps
文件）：

```
iwinfo ra0 info | grep Access | awk '{print $3}')
```


<h4 id="2.12">2.12 Linkit\_Smart\_7688默认模组的添加和删除</h4>

`Linkit_Smart_7688`默认添加了很多的模组，例如：python，node.js等。我们如何对默认的模组进行修改，添加或删除想要的模组呢。其实很简单，我们就是需要修改`mtk-linkit\Makefile`，在该文件中修改想要或不想要的模组。然后feed install它就可以了。当然，你会发现已经安装过的模组，在文件中删除后，编译后仍然会在firmware中，这是怎么回事呢？这是因为，在编译默认的Makefile的时候，系统通过feed已经将原来默认的模组添加进了.config。如果要删除它们，需要同feed来删除它们。然后，编译以后就不会出现了。

```text
define Package/mtk-linkit
  TITLE:=MTK LinkIt Smart board support package
  HIDDEN:=1
  DEPENDS:=@TARGET_ramips_mt7688_LinkIt7688 \
    +gdbserver +curl +strace +coreutils +coreutils-stty \
    +avahi-nodbus-daemon +mountd +mjpg-streamer \
  +uhttpd +rpcd +rpcd-mod-iwinfo +git +git-http +samba36-server \
  +python +python-pyserial +python-pip +hidapi \
  +libmraa +libupm +node +node-hid +node-cylon-firmata +yunbridge \
  +luci +luci-theme-openwrt +luci-app-mjpg-streamer +luci-app-samba +luci-lib-json \
  +rpcd-mod-rpcsys +cgi-io +avrdude +spi-tools +uboot-envtools \
  +kmod-fs-vfat +kmod-fs-exfat +kmod-i2c-core +kmod-i2c-ralink \
  +kmod-nls-base +kmod-nls-cp437 +kmod-nls-iso8859-1 \
  +kmod-nls-iso8859-15 +kmod-nls-iso8859-2 +kmod-nls-utf8 \
  +kmod-sdhci-mt7620 +kmod-usb-storage \
  +kmod-video-core +kmod-video-uvc \
  +kmod-sound-core +kmod-sound-mtk +madplay-alsa +alsa-utils \
  +mtk-linkit-webui +mtk-sdk-wifi +tcpdump-mini

  CATEGORY:=Base system
  DEFAULT:=y
endef
```

<h4 id="2.13">2.13 如果修改`Linkit_Smart_7688`默认opkg连接地址</h4>

```
ubuntu@ubuntu-System-Name:~/openwrt/openwrt_ethMOH$ cat feeds.conf.default
src-git packages https://github.com/openwrt/packages.git;for-15.05
src-git luci https://github.com/openwrt/luci.git;for-15.05
src-git routing https://github.com/openwrt-routing/packages.git;for-15.05
src-git telephony https://github.com/openwrt/telephony.git;for-15.05
src-git management https://github.com/openwrt-management/packages.git;for-15.05
#src-git targets https://github.com/openwrt/targets.git
#src-git oldpackages http://git.openwrt.org/packages.git
#src-svn xwrt http://x-wrt.googlecode.com/svn/trunk/package
#src-svn phone svn://svn.openwrt.org/openwrt/feeds/phone
#src-svn efl svn://svn.openwrt.org/openwrt/feeds/efl
#src-svn xorg svn://svn.openwrt.org/openwrt/feeds/xorg
#src-svn desktop svn://svn.openwrt.org/openwrt/feeds/desktop
#src-svn xfce svn://svn.openwrt.org/openwrt/feeds/xfce
#src-svn lxde svn://svn.openwrt.org/openwrt/feeds/lxde
#src-link custom /usr/src/openwrt/custom-feed

```

该连接地址的修改主要修改一个文件`mtk-linkit\files\etc\opkg\linkit.conf`。我们可以通过修改这个文件中的文件指向你的opkg连接地址，然后编译。编译后出的OS就可以通过`opkg install`连接你的服务器了。当然，如果你不想使用`Linkit_Smart_7688`默认的opkg连接地址的话，你可以在`mtk-linkit\files\etc\init.d\linkit`文件中删除对`linkit.conf`的初始化部分，这样在`OpenWrt`启动的时候就不会讲`linit.conf`添加`/etc/opkg/distfeeds.conf`。

当然，如果你不想修改系统，只是在OpenWrt中修改opkg连接地址的话，那也很简单。你只要用vi命令修改`/etc/opkg/distfeeds.conf`文件就可以了。这也很简单。

<h4 id="2.14">2.14 如何修改Linkit_Smart_7688默认lan ipaddress.</h4>

默认的`Lan IPAddress`在OpenWrt的`/etc/config/network`文件中设置，如果想修改ip地址。可以在`linkit-smart-7688-feed\mtk-linkit\files\etc\uci-defaults\51_linkit_config`文件中进行修改。linkit-smart-7688-feed默认的`Lan ipaddr` 是 `192.168.100.1`。

<h4 id="2.15">2.15 如果修改Linkit_Smart_7688的GPIO11.</h4>

Linkit\_Smart\_7688的GPIO11引脚，默认用来锁定`CL245A`芯片的。该芯片是一个锁存器，用来提升MT7688的引脚输出功率和保护MT7688的引脚功能。但是，如果你不需要这个功能，希望将GPIO11引脚释放出来的话。你可以对源代码进行一些修改或者删除。MTK在linux中增加了一个驱动，用来驱动GPIO11引脚的。详细内容可以参考`target\linux\ramips\patches-3.18\0200-linkit_bootstrap.patch`文件。

<h4 id="2.16">2.16 aplay多声卡的使用</h4>

Linkit默认使用WM8960声卡，现在增加了一个USB声卡，声卡检测发现了该USB声卡。<http://alsa.opensrc.org/MultipleCards>


命令运行结果:

```
root@mylinkit:/# aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: I2S [MTK APSoC I2S], device 0: WMserious PCM wm8960-hifi-0 []
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: Set [C-Media USB Headphone Set], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0

arecord -Dhw:0 -f cd -t wav -M /tmp/rec.wav
aplay -Dhw:0 /tmp/rec.wav

arecord -Dhw:1 -f cd -t wav -M /tmp/rec1.wav
aplay -Dhw:1 /tmp/rec1.wav
```


#### 2.16.1 aplay查询有效声卡 aplay -l

```
root@mylinkit:/# aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: I2S [MTK APSoC I2S], device 0: WMserious PCM wm8960-hifi-0 []
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: Set [C-Media USB Headphone Set], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

#### 2.16.2 用指定的声卡播放 aplay -Dhw:x (x=0,x=1)

```
root@mylinkit:/# aplay -Dhw:1 /Media/SD-P1/*.wav
Playing WAVE '/Media/SD-P1/1.wav' : Signed 16 bit Little Endian, Rate 44100 Hz, Stereo
```

#### 2.16.3 aplay -h

```sh
root@mylinkit:/# aplay -h
Usage: aplay [OPTION]... [FILE]...

-h, --help              help
    --version           print current version
-l, --list-devices      list all soundcards and digital audio devices
-L, --list-pcms         list device names
-D, --device=NAME       select PCM by name
-q, --quiet             quiet mode
-t, --file-type TYPE    file type (voc, wav, raw or au)
-c, --channels=#        channels
-f, --format=FORMAT     sample format (case insensitive)
-r, --rate=#            sample rate
-d, --duration=#        interrupt after # seconds
-M, --mmap              mmap stream
-N, --nonblock          nonblocking mode
-F, --period-time=#     distance between interrupts is # microseconds
-B, --buffer-time=#     buffer duration is # microseconds
    --period-size=#     distance between interrupts is # frames
    --buffer-size=#     buffer duration is # frames
-A, --avail-min=#       min available space for wakeup is # microseconds
-R, --start-delay=#     delay for automatic PCM start is # microseconds
                        (relative to buffer size if <= 0)
-T, --stop-delay=#      delay for automatic PCM stop is # microseconds from xrun
-v, --verbose           show PCM structure and setup (accumulative)
-V, --vumeter=TYPE      enable VU meter (TYPE: mono or stereo)
-I, --separate-channels one file for each channel
-i, --interactive       allow interactive operation from stdin
-m, --chmap=ch1,ch2,..  Give the channel map to override or follow
    --disable-resample  disable automatic rate resample
    --disable-channels  disable automatic channel conversions
    --disable-format    disable automatic format conversions
    --disable-softvol   disable software volume control (softvol)
    --test-position     test ring buffer position
    --test-coef=#       test coefficient for ring buffer position (default 8)
                        expression for validation is: coef * (buffer_size / 2)
    --test-nowait       do not wait for ring buffer - eats whole CPU
    --max-file-time=#   start another output file when the old file has recorded
                        for this many seconds
    --process-id-file   write the process ID here
    --use-strftime      apply the strftime facility to the output file name
    --dump-hw-params    dump hw_params of the device
    --fatal-errors      treat all errors as fatal
Recognized sample formats are: S8 U8 S16_LE S16_BE U16_LE U16_BE S24_LE S24_BE U24_LE U24_BE S32_LE S32_BE U32_LE U32_BE FLOAT_LE FLOAT_BE FLOAT64_LE FLOAT64_BE IEC958_SUBFRAME_LE IEC958_SUBFRAME_BE MU_LAW A_LAW IMA_ADPCM MPEG GSM SPECIAL S24_3LE S24_3BE U24_3LE U24_3BE S20_3LE S20_3BE U20_3LE U20_3BE S18_3LE S18_3BE U18_3LE U18_3BE G723_24 G723_24_1B G723_40 G723_40_1B DSD_U8 DSD_U16_LE
Some of these may not be available on selected hardware
The available format shortcuts are:
-f cd (16 bit little endian, 44100, stereo)
-f cdr (16 bit big endian, 44100, stereo)
-f dat (16 bit little endian, 48000, stereo)
```

#### 2.16.4 arecord & aplay test

```sh
arecord -f cd -t wav -M /Media/USB-A1/my_recording.wav
aplay -M my_recording.wav
```

#### 2.16.5 参考连接：

<http://alsa.opensrc.org/>

<http://www.cnblogs.com/cslunatic/p/3227655.html>
<http://blog.chinaunix.net/uid-26588712-id-3054726.html>
<https://wiki.archlinux.org/index.php/Advanced_Linux_Sound_Architecture_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29#.E8.AE.BE.E7.BD.AE.E9.BB.98.E8.AE.A4.E5.A3.B0.E5.8D.A1>


2.17 madplay

<http://www.crystalradio.cn/thread-466848-1-1.html>
<http://blog.csdn.net/fengliang191/article/details/19763579>

madplay默认按键

```
The keyboard controls are documented in the man page for madplay:
    P  Pause; press any key to resume.
    S  Stop; press any key to replay the  current  file
       from the beginning.
    F  Forward; advance to the next file.
    B  Back;  replay  the  current  file, unless it has
       been playing for less than 4 seconds,  in  which
       case replay the previous file.
    T  Time display; change the time display mode. This
       only works with  -v  (--verbose).   The  display
       mode alternates among overall playing time, cur-
       rent time remaining, and current playing time.
    +  Increase gain; increase the audio output gain by
       0.5 dB.
    -  Decrease gain; decrease the audio output gain by
       0.5 dB.
    Q  Quit; stop decoding and exit.
```


```

root@mylinkit:~# madplay -h
Usage: madplay [OPTIONS] FILE [...]
Decode and play MPEG audio FILE(s).

Verbosity:
  -v, --verbose                show status while decoding
  -q, --quiet                  be quiet but show warnings
  -Q, --very-quiet             be quiet and do not show warnings
      --display-time=MODE      use default verbose time display MODE
                                 (remaining, current, overall)

Decoding:
      --downsample             reduce sample rate 2:1
  -i, --ignore-crc             ignore CRC errors
      --ancillary-output=PATH  write ancillary data to PATH

Audio output:
  -o, --output=[TYPE:]PATH     write output to PATH with format TYPE (below)
  -b, --bit-depth=DEPTH        request DEPTH bits per sample
  -R, --sample-rate=HERTZ      request HERTZ samples per second
  -d, --no-dither              do not dither output PCM samples
      --fade-in[=DURATION]     fade-in songs over DURATION (default 0:05)
  -a, --attenuate=DECIBELS     attenuate signal by DECIBELS (-)
  -a, --amplify=DECIBELS       amplify signal by DECIBELS (+)
  -A, --adjust-volume=DECIBELS override per-file volume adjustments
  -G, --replay-gain[=PROFILE]  enable Replay Gain volume adjustments using
                                 PROFILE (radio, audiophile)

Channel selection:
  -1, --left                   output first (left) channel only
  -2, --right                  output second (right) channel only
  -m, --mono                   mix left and right channels for monaural output
  -S, --stereo                 force stereo output

Playback:
  -s, --start=TIME             skip to begin at TIME (HH:MM:SS.DDD)
  -t, --time=DURATION          play only for DURATION (HH:MM:SS.DDD)
  -z, --shuffle                randomize file list
  -r, --repeat[=MAX]           play files MAX times, or indefinitely
      --tty-control            enable keyboard controls
      --no-tty-control         disable keyboard controls

Miscellaneous:
  -T, --show-tags-only         show ID3/encoder tags only (do not decode)
  -V, --version                display version number and exit
      --license                show copyright/license message and exit
  -h, --help                   display this help and exit

Supported output formats:
  cdda    CD audio, 16-bit big-endian 44100 Hz stereo PCM (*.cdr, *.cda)
  aiff    Audio IFF, [16-bit] PCM (*.aif, *.aiff)
  wave    Microsoft RIFF/WAVE, [16-bit] PCM (*.wav)
  snd     Sun/NeXT audio, 8-bit ISDN mu-law (*.au, *.snd)
  raw     binary [16-bit] host-endian linear PCM
  hex     ASCII hexadecimal [24-bit] linear PCM
  null    no output (decode only)
```

2.17.1  C调用函数

参考文档：<http://blog.csdn.net/zouzuohuo/article/details/7895917>

```
system("madplay north.mp3 &");//利用system函数调用madplay播放器播放*.mp3音乐
system("madplay north.mp3 -r &");//循环播放：参数-r
system("killall -9 madplay");//利用system函数调用killall命令将madplay终止掉 
system("killall -STOP madplay &");//利用system函数调用killall命令将madplay暂停
system("killall -CONT madplay &");//利用system函数调用killall命令恢复madplay的播放

killall -19 madplay"使进程挂起以暂停
killall -18 madplay"使进程恢复运行
killall -9 madplay"终止进程以停止。
```

2.17.2 madplay 播放网路音频

参考文档：<https://wiki.openwrt.org/toh/freecom/fsg-3?s[]=madplay>

```
cd /tmp
wget http://icy-e-bz-05-cr.sharp-stream.com/magic1054.mp3 -O - | madplay 

wget -O - http://u11aw.di.fm:80/di_goapsy | madplay -
wget -O - http://mp3stream1.apasf.apa.at:8000/ | madplay -
wget -O - http://hirschmilch.de:7000/psytrance.mp3 | madplay -

wget -O - http://64.236.34.97:80/stream/1014 | madplay -

$ wget–q–O– http://my_music.com/mystream | madplay -Q --no-tty-control- $ wget–q–O– http://my_music.com/mystream | madplay -Q --no-tty-control- $ wget–q–O– http://my_music.com/mystream | madplay -Q --no-tty-control- $ wget–q–O– http://my_music.com/mystream | madplay -Q --no-tty-control- 
```

2.17.3 显示剩余时间

```
madplay -v --display-time=remaining 001.mp3
madplay -v --display-time=current /tmp/mounts/USB-A1/music/*.mp3
madplay -v /tmp/mounts/USB-A1/music/*.mp3
```

2.17.4 -o 将mp3转换成一个wav文件

```
$madplay /Media/SD-P1/moh/*.mp3 -o /tmp/tmp.wav
$madplay /Media/SD-P1/moh/*.mp3 -o /tmp/tmp.wav |aplay

| madplay - &
```

2.17.5 根据文件进行音乐播放

```
  http://www.epdoc.cn/qrs/18100.html

  root@mylinkit:/tmp# find /Media/SD-P1/moh/ -name "*.mp3" | sort >mp3.list
  root@mylinkit:/tmp# more mp3.list
  /Media/SD-P1/moh/001.mp3
  /Media/SD-P1/moh/002.mp3
  /Media/SD-P1/moh/003.mp3
  /Media/SD-P1/moh/001.mp3
  /Media/SD-P1/moh/002.mp3
  /Media/SD-P1/moh/003.mp3
  /Media/SD-P1/moh/004.mp3
  /Media/SD-P1/moh/005.mp3
  /Media/SD-P1/moh/006.mp3
  root@mylinkit:/tmp# madplay $(more mp3.list )
  
  example code:
    echo "sort the mp3 file and generate a mp3 list"
  find ./ -name "*.mp3"|sort>mp3.list
  /bin/echo "mp3.list:"
  more/mp3.list
  /bin/echo
  /bin/echo
  /bin/madplay$(more /mp3.list)
```

2.17.6 madplay管道控制方式 FIFO

参考文档：https://www.cs.sfu.ca/CourseCentral/433/bfraser/other/2011-student-howtos/MadplayControlViaButtons.pdf


2.17.7 madplay音量控制

获得喇叭的最大音量/最小音量/当前音量，运算后保存回去。

```
  MIN_VOLUME=$(amixer cget numid=11,iface=MIXER,name='Speaker Playback Volume' | grep '; type' |sed 's/^.*min=//g' | sed 's/,.*$//g')
  MAX_VOLUME=$(amixer cget numid=11,iface=MIXER,name='Speaker Playback Volume' | grep '; type' |sed 's/^.*max=//g' | sed 's/,.*$//g')
  ALSA_VOLUME=$(amixer cget numid=11,iface=MIXER,name='Speaker Playback Volume' | grep ': values' |sed 's/^.*values=//g' | sed 's/,.*$//g')
  let MAX_VOLUME-=8
  if [ "$ALSA_VOLUME" -le "$MAX_VOLUME" ]
  then
    let ALSA_VOLUME+=8
  fi
  #echo $ALSA_VOLUME >/dev/console; echo $MAX_VOLUME >/dev/console
  amixer cset numid=11,iface=MIXER,name='Speaker Playback Volume' $ALSA_VOLUME
```


2.17.8 参考文档：

讲解如何用按键控制madplay
<http://wenku.baidu.com/link?url=aSt7QMxrFFHZngf7rN2lZu2R8tldvJdXnRSl93SCf1xldTvVFfQ_77dsWF3-nb9gYa3_LBDmJXTNLQQR09rCuUT-ycWgL-Z7V-BG3EH7fKW>

Openwrt下 mplayer的配置详解
<http://www.crystalradio.cn/thread-466848-1-1.html>
  
mp3播放器控制程序  
<http://blog.sina.com.cn/s/blog_95268f5001016gnf.html>
  
<http://blog.csdn.net/zouzuohuo/article/details/7895917>
  

#### 2.18 swconfig

**列出当前系统的存在的switch**

```
root@ipRec:/etc/config# swconfig list
Found: switch0 - rt305x
```

**展示当前switch端口配置**

```
root@ipRec:/etc/config# swconfig dev rt305x show
Global attributes:
        enable_vlan: 0
        alternate_vlan_disable: 0
        bc_storm_protect: 0
        led_frequency: 0
Port 0:
        disable: 0
        doubletag: 1
        untag: 1
        led: 5
        lan: 1
        recv_bad: 0
        recv_good: 2479
        tr_bad: 0
        tr_good: 104
        pvid: 0
        link: port:0 link:up speed:100baseT full-duplex
Port 1:
        disable: 0
        doubletag: 1
        untag: 1
        led: 5
        lan: 1
        recv_bad: 0
        recv_good: 0
        tr_bad: 0
        tr_good: 0
        pvid: 0
        link: port:1 link:down
Port 2:
        disable: 0
        doubletag: 1
        untag: 1
        led: 5
        lan: 1
        recv_bad: 0
        recv_good: 0
        tr_bad: 0
        tr_good: 0
        pvid: 0
        link: port:2 link:down
Port 3:
        disable: 0
        doubletag: 1
        untag: 1
        led: 5
        lan: 1
        recv_bad: 0
        recv_good: 0
        tr_bad: 0
        tr_good: 0
        pvid: 0
        link: port:3 link:down
Port 4:
        disable: 0
        doubletag: 1
        untag: 1
        led: 5
        lan: 1
        recv_bad: 0
        recv_good: 0
        tr_bad: 0
        tr_good: 0
        pvid: 0
        link: port:4 link:down
Port 5:
        disable: 1
        doubletag: 1
        untag: 1
        led: ???
        lan: 1
        recv_bad: 0
        recv_good: 0
        tr_bad: 0
        tr_good: 0
        pvid: 0
        link: port:5 link:down
Port 6:
        disable: 0
        doubletag: 1
        untag: 1
        led: ???
        lan: ???
        recv_bad: ???
        recv_good: ???
        tr_bad: ???
        tr_good: ???
        pvid: 0
        link: port:6 link:up speed:1000baseT full-duplex
VLAN 0:
        ports: 0 1 2 3 4 5 6
```

```
root@ipRec:/etc/config# ifconfig
br-lan    Link encap:Ethernet  HWaddr 00:0C:43:E1:76:2A
          inet addr:192.168.100.1  Bcast:192.168.100.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:43ff:fee1:762a/64 Scope:Link
          inet6 addr: fd83:d6c:cf85::1/60 Scope:Global
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:903 errors:0 dropped:0 overruns:0 frame:0
          TX packets:306 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:63543 (62.0 KiB)  TX bytes:27616 (26.9 KiB)

eth0      Link encap:Ethernet  HWaddr 00:0C:43:E1:76:29
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1130 errors:0 dropped:0 overruns:0 frame:0
          TX packets:132 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:124403 (121.4 KiB)  TX bytes:11965 (11.6 KiB)
          Interrupt:5

eth0.1    Link encap:Ethernet  HWaddr 00:0C:43:E1:76:29
          inet6 addr: fe80::20c:43ff:fee1:7629/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:9 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 B)  TX bytes:1931 (1.8 KiB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:8736 errors:0 dropped:0 overruns:0 frame:0
          TX packets:8736 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:594720 (580.7 KiB)  TX bytes:594720 (580.7 KiB)

```


### 2.19 网络配置

<http://labs.mediatek.com/site/global/developer_tools/mediatek_linkit_smart_7688/training_docs/network/setup_wireless_router/index.gsp>


#### 2.20 gpio debug

可以通过#cat /sys/kernel/debug/gpio命令查询当前按钮状态。lo,低电平，hi为高电平。

```
root@mylinkit:/# cat /sys/kernel/debug/gpio
GPIOs 0-31, platform/10000600.gpio, 10000600.gpio:
 gpio-11  (bootstrap           ) out lo
 gpio-14  (BTN_0               ) in  hi
 gpio-15  (BTN_1               ) in  hi
 gpio-16  (BTN_2               ) in  hi
 gpio-17  (BNT_3               ) in  hi
 gpio-18  (BTN_4               ) in  hi
 gpio-19  (S9                  ) in  hi

 GPIOs 32-63, platform/10000600.gpio, 10000600.gpio:
  gpio-38  (reset               ) in  hi

 GPIOs 64-95, platform/10000600.gpio, 10000600.gpio:

 GPIOs 127-127, platform/gpio-wifi, gpio-wifi:
  gpio-127 (mediatek:orange:wifi) out ?
 
```


#### 2.21 环境变量显示
  环境变量的相关信息，参考linux鸟歌私房菜上，P304。

#### 2.22 稍描wifi热点

  # iwinfo ra0 scan
  # time iwlist wlan0 scan | grep ESSID

```
 root@mylinkit:/tmp# iwinfo ra0 scan
  Cell 01 - Address: 6C:E8:73:AB:0C:96
            ESSID: "FAST_AB0C96"
            Mode: Master  Channel: 1
            Signal: -256 dBm  Quality: 10/100
            Encryption: WPA2 PSK (AES-OCB)

  Cell 02 - Address: C8:3A:35:29:F5:54
            ESSID: "wanghf"
            Mode: Master  Channel: 1
            Signal: -256 dBm  Quality: 7/100
            Encryption: WPA PSK (AES-OCB)

  Cell 03 - Address: 00:06:25:00:6E:72
            ESSID: "home"
            Mode: Master  Channel: 6
            Signal: -256 dBm  Quality: 81/100
            Encryption: WPA2 PSK (TKIP, AES-OCB)

  Cell 04 - Address: 8C:21:0A:41:D3:64
            ESSID: "wf"
            Mode: Master  Channel: 11
            Signal: -256 dBm  Quality: 0/100
            Encryption: WPA2 PSK (AES-OCB)
```


#### 2.23 du 查询磁盘空间使用情况
  du /

#### 2.24 PID查询命令ps, pidof, pgrep

#### 2.25 date

  http://www.runoob.com/linux/linux-comm-date.html
  http://www.2cto.com/os/201310/248416.html
  http://www.firefoxbug.com/index.php/archives/2799/
  http://www.cnblogs.com/peida/archive/2012/12/13/2815687.html
  
  date +%Y/%m/%d
  date +%H:%M
  
  +%Y - year年
  +%m - month月
  +%d - day天
  +%H - Hour时
  +%M - Minute分
  +%S - Second秒
  +%s - 从 1970 年 1 月 1 日 00:00:00 UTC 到目前为止的秒数
%H : 小时(00..23)
%I : 小时(01..12)
%k : 小时(0..23)
%l : 小时(1..12)
%M : 分钟(00..59)
%p : 显示本地 AM 或 PM
%r : 直接显示时间 (12 小时制，格式为 hh:mm:ss [AP]M)
%s : 从 1970 年 1 月 1 日 00:00:00 UTC 到目前为止的秒数
%S : 秒(00..61)
%T : 直接显示时间 (24 小时制)
%X : 相当于 %H:%M:%S
%Z : 显示时区 %a : 星期几 (Sun..Sat)
%A : 星期几 (Sunday..Saturday)
%b : 月份 (Jan..Dec)
%B : 月份 (January..December)
%c : 直接显示日期与时间
%d : 日 (01..31)
%D : 直接显示日期 (mm/dd/yy)
%h : 同 %b
%j : 一年中的第几天 (001..366)
%m : 月份 (01..12)
%U : 一年中的第几周 (00..53) (以 Sunday 为一周的第一天的情形)
%w : 一周中的第几天 (0..6)
%W : 一年中的第几周 (00..53) (以 Monday 为一周的第一天的情形)
%x : 直接显示日期 (mm/dd/yy)
%y : 年份的最后两位数字 (00.99)
%Y : 完整年份 (0000..9999)

  date指定格式输出
    $ date +"%Y-%m-%d %H:%M:%S"
    2014-11-21 23:59:37
  
  转换指定日期为Unix时间戳：
    $ date -d "2008-01-01 00:00:00" +%s
    1199116800

    $ date -d 2008-01-01 +%s
    1199116800

    $ date -d 20080101 +%s
    1199116800
    
    例外: 有一种场景date格式是连续的(没找到date命令怎么转换成unix)

    $ echo 20080101010101 | awk '{print substr($0,1,4)"-"substr($0,5,2)"-"substr($0,7,2)" "substr($0,9,2)":"substr($0,11,2)":"substr($0,13,2) }'
    2008-01-01 01:01:01

    $ date -d "2008-01-01 01:01:01" +%s
    1199120461
    
    
  root@mylinkit:/etc/config# date -help
  date: invalid option -- h
  BusyBox v1.23.2 (2016-01-20 23:54:03 CST) multi-call binary.

  Usage: date [OPTIONS] [+FMT] [TIME]

  Display time (using +FMT), or set time

        [-s,--set] TIME Set time to TIME
        -u,--utc        Work in UTC (don't convert to local time)
        -R,--rfc-2822   Output RFC-2822 compliant date string
        -I[SPEC]        Output ISO-8601 compliant date string
                        SPEC='date' (default) for date only,
                        'hours', 'minutes', or 'seconds' for date and
                        time to the indicated precision
        -r,--reference FILE     Display last modification time of FILE
        -d,--date TIME  Display TIME, not 'now'
        -D FMT          Use FMT for -d TIME conversion
        -k              Set Kernel timezone from localtime and exit

  Recognized TIME formats:
        hh:mm[:ss]
        [YYYY.]MM.DD-hh:mm[:ss]
        YYYY-MM-DD hh:mm[:ss]
        [[[[[YY]YY]MM]DD]hh]mm[.ss]


#### 2.26 grep,sed,cut

参考moh-scheduler文档

```
  # SD10022016&ED31012016&DW1111100&TS1015TE1730
  start_day=$(cat $config_file |sed 's/^.*SD//g'|cut -c -8)
  cut切除指定的字符
  cut -c 1-8 //切除第1到第8字符
```


#### 2.27 wait

<https://zh.wikipedia.org/wiki/Wait_(%E5%91%BD%E4%BB%A4)>


#### 2.28  ubus

```
  root@mylinkit:~# ubus --help
  ubus: invalid option -- -
  Usage: ubus [<options>] <command> [arguments...]
  Options:
   -s <socket>:           Set the unix domain socket to connect to
   -t <timeout>:          Set the timeout (in seconds) for a command to complete
   -S:                    Use simplified output (for scripts)
   -v:                    More verbose output

  Commands:
   - list [<path>]                        List objects
   - call <path> <method> [<message>]     Call an object method
   - listen [<path>...]                   Listen for events
   - send <type> [<message>]              Send an event
   - wait_for <object> [<object>...]      Wait for multiple objects to appear on ubus
  
 
  root@mylinkit:~# ubus list
  dhcp
  iwinfo
  log
  network
  network.device
  network.interface
  network.interface.lan
  network.interface.loopback
  network.interface.wan
  network.wireless
  rpc-sys
  service
  session
  system
  uci

  root@mylinkit:~# ubus list -v
  'dhcp' @d5aa2d81
        "ipv4leases":{}
        "ipv6leases":{}
  'iwinfo' @804494b9
        "devices":{}
        "info":{"device":"String"}
        "scan":{"device":"String"}
        "assoclist":{"device":"String","mac":"String"}
        "freqlist":{"device":"String"}
        "txpowerlist":{"device":"String"}
        "countrylist":{"device":"String"}
        "phyname":{"section":"String"}
  'log' @e7e97da6
        "read":{"lines":"Integer"}
        "write":{"event":"String"}
  'network' @06d3dcba
        "restart":{}
        "reload":{}
        "add_host_route":{"target":"String","v6":"Boolean","interface":"String"}
        "get_proto_handlers":{}
        "add_dynamic":{"name":"String"}
  'network.device' @9b47eb98
        "status":{"name":"String"}
        "set_alias":{"alias":"Array","device":"String"}
        "set_state":{"name":"String","defer":"Boolean"}
  'network.interface' @c806e5d7
        "up":{}
        "down":{}
        "status":{}
        "prepare":{}
        "dump":{}
        "add_device":{"name":"String","link-ext":"Boolean"}
        "remove_device":{"name":"String","link-ext":"Boolean"}
        "notify_proto":{}
        "remove":{}
        "set_data":{}
  'network.interface.lan' @da711a3b
        "up":{}
        "down":{}
        "status":{}
        "prepare":{}
        "dump":{}
        "add_device":{"name":"String","link-ext":"Boolean"}
        "remove_device":{"name":"String","link-ext":"Boolean"}
        "notify_proto":{}
        "remove":{}
        "set_data":{}
  'network.interface.loopback' @eaf974e8
        "up":{}
        "down":{}
        "status":{}
        "prepare":{}
        "dump":{}
        "add_device":{"name":"String","link-ext":"Boolean"}
        "remove_device":{"name":"String","link-ext":"Boolean"}
        "notify_proto":{}
        "remove":{}
        "set_data":{}
  'network.interface.wan' @eff0e141
        "up":{}
        "down":{}
        "status":{}
        "prepare":{}
        "dump":{}
        "add_device":{"name":"String","link-ext":"Boolean"}
        "remove_device":{"name":"String","link-ext":"Boolean"}
        "notify_proto":{}
        "remove":{}
        "set_data":{}
  'network.wireless' @a8161143
        "up":{}
        "down":{}
        "status":{}
        "notify":{}
        "get_validate":{}
  'rpc-sys' @dd844b8f
        "password_set":{"user":"String","password":"String"}
        "upgrade_test":{}
        "upgrade_start":{"keep":"Boolean"}
        "upgrade_clean":{}
        "factory":{}
        "reboot":{}
  'service' @1312b281
        "set":{"name":"String","script":"String","instances":"Table","triggers":"Array","validate":"Array"}
        "add":{"name":"String","script":"String","instances":"Table","triggers":"Array","validate":"Array"}
        "list":{"name":"String","verbose":"Boolean"}
        "delete":{"name":"String","instance":"String"}
        "update_start":{"name":"String"}
        "update_complete":{"name":"String"}
        "event":{"type":"String","data":"Table"}
        "validate":{"package":"String","type":"String","service":"String"}
        "get_data":{"name":"String","instance":"String","type":"String"}
  'session' @4417ac78
        "create":{"timeout":"Integer"}
        "list":{"ubus_rpc_session":"String"}
        "grant":{"ubus_rpc_session":"String","scope":"String","objects":"Array"}
        "revoke":{"ubus_rpc_session":"String","scope":"String","objects":"Array"}
        "access":{"ubus_rpc_session":"String","scope":"String","object":"String","function":"String"}
        "set":{"ubus_rpc_session":"String","values":"Table"}
        "get":{"ubus_rpc_session":"String","keys":"Array"}
        "unset":{"ubus_rpc_session":"String","keys":"Array"}
        "destroy":{"ubus_rpc_session":"String"}
        "login":{"username":"String","password":"String","timeout":"Integer"}
  'system' @5bc7fd25
        "board":{}
        "info":{}
        "upgrade":{}
        "watchdog":{"frequency":"Integer","timeout":"Integer","stop":"Boolean"}
        "signal":{"pid":"Integer","signum":"Integer"}
        "nandupgrade":{"path":"String"}
  'uci' @2fd66254
        "configs":{}
        "get":{"config":"String","section":"String","option":"String","type":"String","match":"Table","ubus_rpc_session":"String"}
        "state":{"config":"String","section":"String","option":"String","type":"String","match":"Table","ubus_rpc_session":"String"}
        "add":{"config":"String","type":"String","name":"String","values":"Table","ubus_rpc_session":"String"}
        "set":{"config":"String","section":"String","type":"String","match":"Table","values":"Table","ubus_rpc_session":"String"}
        "delete":{"config":"String","section":"String","type":"String","match":"Table","option":"String","options":"Array","ubus_rpc_session":"String"}
        "rename":{"config":"String","section":"String","option":"String","name":"String","ubus_rpc_session":"String"}
        "order":{"config":"String","sections":"Array","ubus_rpc_session":"String"}
        "changes":{"config":"String","ubus_rpc_session":"String"}
        "revert":{"config":"String","ubus_rpc_session":"String"}
        "commit":{"config":"String","ubus_rpc_session":"String"}
        "apply":{"rollback":"Boolean","timeout":"Integer","ubus_rpc_session":"String"}
        "confirm":{"ubus_rpc_session":"String"}
        "rollback":{"ubus_rpc_session":"String"}
        "reload_config":{}
```


#### 2.29 rsync

<h4 id="3"></h4>    
## 3. openwrt application

<h4 id="3.1"></h4>
#### 3.1 opkg

<h4 id="3.2"></h4>    
#### 3.2 date

```
  root@mylinkit:/etc/config# date -help
  date: invalid option -- h
  BusyBox v1.23.2 (2016-01-20 23:54:03 CST) multi-call binary.

  Usage: date [OPTIONS] [+FMT] [TIME]

  Display time (using +FMT), or set time

        [-s,--set] TIME Set time to TIME
        -u,--utc        Work in UTC (don't convert to local time)
        -R,--rfc-2822   Output RFC-2822 compliant date string
        -I[SPEC]        Output ISO-8601 compliant date string
                        SPEC='date' (default) for date only,
                        'hours', 'minutes', or 'seconds' for date and
                        time to the indicated precision
        -r,--reference FILE     Display last modification time of FILE
        -d,--date TIME  Display TIME, not 'now'
        -D FMT          Use FMT for -d TIME conversion
        -k              Set Kernel timezone from localtime and exit

  Recognized TIME formats:
        hh:mm[:ss]
        [YYYY.]MM.DD-hh:mm[:ss]
        YYYY-MM-DD hh:mm[:ss]
        [[[[[YY]YY]MM]DD]hh]mm[.ss]
```

<h4 id="3.3"></h4>
#### 3.3 稍描wifi热点

```
  # iwinfo ra0 scan
  # time iwlist wlan0 scan | grep ESSID

  root@mylinkit:/tmp# iwinfo ra0 scan
  Cell 01 - Address: 6C:E8:73:AB:0C:96
            ESSID: "FAST_AB0C96"
            Mode: Master  Channel: 1
            Signal: -256 dBm  Quality: 10/100
            Encryption: WPA2 PSK (AES-OCB)

  Cell 02 - Address: C8:3A:35:29:F5:54
            ESSID: "wanghf"
            Mode: Master  Channel: 1
            Signal: -256 dBm  Quality: 7/100
            Encryption: WPA PSK (AES-OCB)

  Cell 03 - Address: 00:06:25:00:6E:72
            ESSID: "home"
            Mode: Master  Channel: 6
            Signal: -256 dBm  Quality: 81/100
            Encryption: WPA2 PSK (TKIP, AES-OCB)

  Cell 04 - Address: 8C:21:0A:41:D3:64
            ESSID: "wf"
            Mode: Master  Channel: 11
            Signal: -256 dBm  Quality: 0/100
            Encryption: WPA2 PSK (AES-OCB)
```

<h4 id="3.4"></h4>
#### 3.4 alsamixer,amixer 音量调节

* alsamixer是文本方式下的图形命令
* amixer是文本方式下的文本命令

**amixer**

```
Simple mixer control 'Headphone',0
  Capabilities: pvolume
  Playback channels: Front Left - Front Right
  Limits: Playback 0 - 127
  Mono:
  Front Left: Playback 121 [95%] [0.00dB]
  Front Right: Playback 121 [95%] [0.00dB]
Simple mixer control 'Headphone Playback ZC',0
  Capabilities: pswitch
  Playback channels: Front Left - Front Right
  Mono:
  Front Left: Playback [on]
  Front Right: Playback [on]
Simple mixer control 'Speaker',0
  Capabilities: pvolume
  Playback channels: Front Left - Front Right
  Limits: Playback 0 - 127
  Mono:
  Front Left: Playback 121 [95%] [0.00dB]
  Front Right: Playback 121 [95%] [0.00dB]
Simple mixer control 'Speaker AC',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 5
  Mono: 4 [80%]
Simple mixer control 'Speaker DC',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 5
  Mono: 5 [100%]
Simple mixer control 'Speaker Playback ZC',0
  Capabilities: pswitch
  Playback channels: Front Left - Front Right
  Mono:
  Front Left: Playback [off]
  Front Right: Playback [off]
Simple mixer control 'PCM Playback -6dB',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Mono Output Mixer Left',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Mono Output Mixer Right',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Playback',0
  Capabilities: volume
  Playback channels: Front Left - Front Right
  Capture channels: Front Left - Front Right
  Limits: 0 - 255
  Front Left: 243 [95%] [-5.50dB]
  Front Right: 243 [95%] [-5.50dB]
Simple mixer control 'Capture',0
  Capabilities: cvolume cswitch
  Capture channels: Front Left - Front Right
  Limits: Capture 0 - 63
  Front Left: Capture 43 [68%] [-75.50dB] [off]
  Front Right: Capture 43 [68%] [-75.50dB] [off]
Simple mixer control '3D',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 0 [0%] Playback [off]
Simple mixer control '3D Filter Lower Cut-Off',0
  Capabilities: enum
  Items: 'Low' 'High'
  Item0: 'Low'
Simple mixer control '3D Filter Upper Cut-Off',0
  Capabilities: enum
  Items: 'High' 'Low'
  Item0: 'High'
Simple mixer control 'ADC High Pass Filter',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'ADC PCM',0
  Capabilities: cvolume
  Capture channels: Front Left - Front Right
  Limits: Capture 0 - 255
  Front Left: Capture 206 [81%] [6.00dB]
  Front Right: Capture 206 [81%] [6.00dB]
Simple mixer control 'ADC Polarity',0
  Capabilities: enum
  Items: 'No Inversion' 'Left Inverted' 'Right Inverted' 'Stereo Inversion'
  Item0: 'No Inversion'
Simple mixer control 'ALC Attack',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 2 [13%]
Simple mixer control 'ALC Decay',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 3 [20%]
Simple mixer control 'ALC Function',0
  Capabilities: enum
  Items: 'Off' 'Right' 'Left' 'Stereo'
  Item0: 'Off'
Simple mixer control 'ALC Hold Time',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 0 [0%]
Simple mixer control 'ALC Max Gain',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 7 [100%]
Simple mixer control 'ALC Min Gain',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%]
Simple mixer control 'ALC Mode',0
  Capabilities: enum
  Items: 'ALC' 'Limiter'
  Item0: 'ALC'
Simple mixer control 'ALC Target',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 4 [27%]
Simple mixer control 'DAC Deemphasis',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'DAC Polarity',0
  Capabilities: enum
  Items: 'No Inversion' 'Left Inverted' 'Right Inverted' 'Stereo Inversion'
  Item0: 'No Inversion'
Simple mixer control 'Left Boost Mixer LINPUT1',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Left Boost Mixer LINPUT2',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Left Boost Mixer LINPUT3',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Left Input Boost Mixer LINPUT2',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%] [-99999.99dB]
Simple mixer control 'Left Input Boost Mixer LINPUT3',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%] [-99999.99dB]
Simple mixer control 'Left Input Mixer Boost',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Left Output Mixer Boost Bypass',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 2 [29%] [-15.00dB] Playback [off]
Simple mixer control 'Left Output Mixer LINPUT3',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 7 [100%] [0.00dB] Playback [off]
Simple mixer control 'Left Output Mixer PCM',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Noise Gate',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Noise Gate Threshold',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 31
  Mono: 0 [0%]
Simple mixer control 'Right Boost Mixer RINPUT1',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Right Boost Mixer RINPUT2',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Right Boost Mixer RINPUT3',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Right Input Boost Mixer RINPUT2',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%] [-99999.99dB]
Simple mixer control 'Right Input Boost Mixer RINPUT3',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%] [-99999.99dB]
Simple mixer control 'Right Input Mixer Boost',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Right Output Mixer Boost Bypass',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 2 [29%] [-15.00dB] Playback [off]
Simple mixer control 'Right Output Mixer PCM',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Right Output Mixer RINPUT3',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 7 [100%] [0.00dB] Playback [off]
```


**amixer controls**

```
numid=10,iface=MIXER,name='Headphone Playback ZC Switch'
numid=9,iface=MIXER,name='Headphone Playback Volume'
numid=15,iface=MIXER,name='PCM Playback -6dB Switch'
numid=39,iface=MIXER,name='Mono Output Mixer Left Switch'
numid=40,iface=MIXER,name='Mono Output Mixer Right Switch'
numid=17,iface=MIXER,name='ADC High Pass Filter Switch'
numid=34,iface=MIXER,name='ADC PCM Capture Volume'
numid=16,iface=MIXER,name='ADC Polarity'
numid=2,iface=MIXER,name='Capture Volume ZC Switch'
numid=3,iface=MIXER,name='Capture Switch'
numid=1,iface=MIXER,name='Capture Volume'
numid=8,iface=MIXER,name='Playback Volume'
numid=21,iface=MIXER,name='3D Filter Lower Cut-Off'
numid=20,iface=MIXER,name='3D Filter Upper Cut-Off'
numid=23,iface=MIXER,name='3D Switch'
numid=22,iface=MIXER,name='3D Volume'
numid=31,iface=MIXER,name='ALC Attack'
numid=30,iface=MIXER,name='ALC Decay'
numid=24,iface=MIXER,name='ALC Function'
numid=28,iface=MIXER,name='ALC Hold Time'
numid=25,iface=MIXER,name='ALC Max Gain'
numid=27,iface=MIXER,name='ALC Min Gain'
numid=29,iface=MIXER,name='ALC Mode'
numid=26,iface=MIXER,name='ALC Target'
numid=19,iface=MIXER,name='DAC Deemphasis Switch'
numid=18,iface=MIXER,name='DAC Polarity'
numid=54,iface=MIXER,name='Left Boost Mixer LINPUT1 Switch'
numid=52,iface=MIXER,name='Left Boost Mixer LINPUT2 Switch'
numid=53,iface=MIXER,name='Left Boost Mixer LINPUT3 Switch'
numid=7,iface=MIXER,name='Left Input Boost Mixer LINPUT2 Volume'
numid=6,iface=MIXER,name='Left Input Boost Mixer LINPUT3 Volume'
numid=48,iface=MIXER,name='Left Input Mixer Boost Switch'
numid=46,iface=MIXER,name='Left Output Mixer Boost Bypass Switch'
numid=35,iface=MIXER,name='Left Output Mixer Boost Bypass Volume'
numid=45,iface=MIXER,name='Left Output Mixer LINPUT3 Switch'
numid=36,iface=MIXER,name='Left Output Mixer LINPUT3 Volume'
numid=44,iface=MIXER,name='Left Output Mixer PCM Playback Switch'
numid=33,iface=MIXER,name='Noise Gate Switch'
numid=32,iface=MIXER,name='Noise Gate Threshold'
numid=51,iface=MIXER,name='Right Boost Mixer RINPUT1 Switch'
numid=49,iface=MIXER,name='Right Boost Mixer RINPUT2 Switch'
numid=50,iface=MIXER,name='Right Boost Mixer RINPUT3 Switch'
numid=5,iface=MIXER,name='Right Input Boost Mixer RINPUT2 Volume'
numid=4,iface=MIXER,name='Right Input Boost Mixer RINPUT3 Volume'
numid=47,iface=MIXER,name='Right Input Mixer Boost Switch'
numid=43,iface=MIXER,name='Right Output Mixer Boost Bypass Switch'
numid=37,iface=MIXER,name='Right Output Mixer Boost Bypass Volume'
numid=41,iface=MIXER,name='Right Output Mixer PCM Playback Switch'
numid=42,iface=MIXER,name='Right Output Mixer RINPUT3 Switch'
numid=38,iface=MIXER,name='Right Output Mixer RINPUT3 Volume'
numid=14,iface=MIXER,name='Speaker AC Volume'
numid=13,iface=MIXER,name='Speaker DC Volume'
numid=11,iface=MIXER,name='Speaker Playback Volume'
numid=12,iface=MIXER,name='Speaker Playback ZC Switch'
```


```
numid=8,iface=MIXER,name='Playback Volume'
numid=9,iface=MIXER,name='Headphone Playback Volume'
numid=11,iface=MIXER,name='Speaker Playback Volume'
```


`sed`参考文档：Linux鸟歌的私房菜  P359

**获得当前喇叭音量值**

```
# amixer cget numid=11,iface=MIXER,name='Speaker Playback Volume' | grep ': values' |sed 's/^.*values=//g' | sed 's/,.*$//g'
```

**获得耳机音量值**

```
# amixer cget numid=9,iface=MIXER,name='Headphone Playback Volume' | grep ': values' |sed 's/^.*values=//g' | sed 's/,.*$//g'
```

**获得播放音量值**

```
# amixer cget numid=8,iface=MIXER,name='Playback Volume' | grep ': values' |sed 's/^.*values=//g' | sed 's/,.*$//g'
```

<h4 id="3.5"></h4>
#### 3.5 top

<h4 id="3.6"></h4>
#### 3.6 ps

```
Mem: 41608K used, 84840K free, 320K shrd, 4332K buff, 11960K cached
CPU:   0% usr   0% sys   0% nic  99% idle   0% io   0% irq   0% sirq
Load average: 0.00 0.01 0.05 2/43 1864
  PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND
 1581  1191 root     S     1240   1%   0% /usr/sbin/dropbear -F -P /var/run/dro
 1331     1 root     S     1016   1%   0% /sbin/mountd -f
 1299     1 root     S     3188   3%   0% /usr/sbin/nmbd -F
 1298     1 root     S     3096   2%   0% /usr/sbin/smbd -F
 1088     1 root     S     1824   1%   0% /sbin/rpcd
 1306     1 nobody   S     1768   1%   0% avahi-daemon: running [mylinkit.local
 1267     1 root     S     1640   1%   0% /usr/sbin/uhttpd -f -h /www -r mylink
 1122     1 root     S     1600   1%   0% /sbin/netifd
 1582  1581 root     S     1496   1%   0% -ash
 1401     1 root     S     1488   1%   0% /usr/sbin/ntpd -n -S /usr/sbin/ntpd-h
 1602  1601 root     S     1488   1%   0% -ash
 1864  1582 root     R     1488   1%   0% top
 1504  1122 root     S     1484   1%   0% udhcpc -p /var/run/udhcpc-apcli0.pid
    1     0 root     S     1436   1%   0% /sbin/procd
 1601  1191 root     S     1240   1%   0% /usr/sbin/dropbear -F -P /var/run/dro
 1160     1 root     S     1192   1%   0% /usr/sbin/odhcpd
 1191     1 root     S     1148   1%   0% /usr/sbin/dropbear -F -P /var/run/dro
 1079     1 root     S     1056   1%   0% /sbin/logd -S 16
 1572     1 nobody   S      992   1%   0% /usr/sbin/dnsmasq -C /var/etc/dnsmasq
  430     1 root     S      904   1%   0% /sbin/ubusd
```

<h4 id="3.7"></h4>
#### 3.7 iwpriv

```
root@mylinkit:/tmp# iwpriv -help
Usage: iwpriv interface [private-command [private-arguments]]
```

```
root@mylinkit:/tmp# iwpriv
eth0.1    no private ioctls.

lo        no private ioctls.

ra0       Available private ioctls :
          set              (8BE2) : set 1536 char  & get   0
          show             (8BF1) : set 1024 char  & get   0
          get_site_survey  (8BED) : set   0       & get 1024 char
          set_wsc_oob      (8BF9) : set 1024 char  & get 1024 char
          get_mac_table    (8BEF) : set 1024 char  & get 1024 char
          e2p              (8BE7) : set 1024 char  & get 1024 char
          bbp              (8BE3) : set 1024 char  & get 1024 char
          mac              (8BE5) : set 1024 char  & get 1024 char
          rf               (8BF3) : set 1024 char  & get 1024 char
          get_wsc_profile  (8BF2) : set 1024 char  & get 1024 char
          get_ba_table     (8BF6) : set 1024 char  & get 1024 char
          stat             (8BE9) : set 1024 char  & get 1024 char

apcli1    Available private ioctls :
          set              (8BE2) : set 1536 char  & get   0
          show             (8BF1) : set 1024 char  & get   0
          get_site_survey  (8BED) : set   0       & get 1024 char
          set_wsc_oob      (8BF9) : set 1024 char  & get 1024 char
          get_mac_table    (8BEF) : set 1024 char  & get 1024 char
          e2p              (8BE7) : set 1024 char  & get 1024 char
          bbp              (8BE3) : set 1024 char  & get 1024 char
          mac              (8BE5) : set 1024 char  & get 1024 char
          rf               (8BF3) : set 1024 char  & get 1024 char
          get_wsc_profile  (8BF2) : set 1024 char  & get 1024 char
          get_ba_table     (8BF6) : set 1024 char  & get 1024 char
          stat             (8BE9) : set 1024 char  & get 1024 char

eth0      no private ioctls.

apcli0    Available private ioctls :
          set              (8BE2) : set 1536 char  & get   0
          show             (8BF1) : set 1024 char  & get   0
          get_site_survey  (8BED) : set   0       & get 1024 char
          set_wsc_oob      (8BF9) : set 1024 char  & get 1024 char
          get_mac_table    (8BEF) : set 1024 char  & get 1024 char
          e2p              (8BE7) : set 1024 char  & get 1024 char
          bbp              (8BE3) : set 1024 char  & get 1024 char
          mac              (8BE5) : set 1024 char  & get 1024 char
          rf               (8BF3) : set 1024 char  & get 1024 char
          get_wsc_profile  (8BF2) : set 1024 char  & get 1024 char
          get_ba_table     (8BF6) : set 1024 char  & get 1024 char
          stat             (8BE9) : set 1024 char  & get 1024 char

br-lan    no private ioctls.
```

/lib/netifd/wireless/ralink.sh
* iwpriv ra0
    * set
        * Channel
        * NoForwarding
        *  NoForwardingBTNBSSID
        *  NoForwardingMBCast
        *  HideSSID
        *  RADIUS_Server
        *  RADIUS_Port
        *  RADIUS_Key
        *  own_ip_addr
        *  EAPifname
        *  PreAuthIfname
        *  AuthMode
        *  EncrypType
        *  IEEE8021X
        *  "SSID=${ssid}"
        *  "WPAPSK=${key}
        *  DefaultKeyID=2
        *  AuthMode=WEPAUTO
        *  EncrypType=WEP
        *  IEEE8021X=0
        *  DefaultKeyID=${key}
        *  WscConfMode=$wsc_mode
        *  WscConfStatus=2
        *  WscMode=2
        *  ACLClearAll=1
        *  ACLAddEntry="$m"
        *  AccessPolicy=0,1,2


```
# iwpriv ra0 set Channel=6
```

<h4 id="3.8"></h4>
#### 3.8 miniDLNA

<https://wiki.openwrt.org/doc/uci/minidlna>
<https://github.com/xiongyihui/LinkIt_Smart_7688>

**Openwrt下安装miniDLNA**

```
root@OpenWrt:~# opkg update
root@OpenWrt:~# opkg install minidlna
```


```
root@mylinkit:~# opkg install minidlna
Installing minidlna (1.1.4-2) to root...
Downloading http://downloads.openwrt.org/chaos_calmer/15.05.1/ramips/mt7688/packages/packages/minidlna_1.1.4-2_ramips_24kec.ipk.
Installing libexif (0.6.21-1) to root...
Downloading http://downloads.openwrt.org/chaos_calmer/15.05.1/ramips/mt7688/packages/packages/libexif_0.6.21-1_ramips_24kec.ipk.
Installing libffmpeg-mini (2.6.2-1) to root...
Downloading http://downloads.openwrt.org/chaos_calmer/15.05.1/ramips/mt7688/packages/packages/libffmpeg-mini_2.6.2-1_ramips_24kec.ipk.
Installing libflac (1.3.1-1) to root...
Downloading http://downloads.openwrt.org/chaos_calmer/15.05.1/ramips/mt7688/packages/packages/libflac_1.3.1-1_ramips_24kec.ipk.
Installing libvorbis (1.3.5-1) to root...
Downloading http://downloads.openwrt.org/chaos_calmer/15.05.1/ramips/mt7688/packages/packages/libvorbis_1.3.5-1_ramips_24kec.ipk.
Installing libogg (1.3.2-2) to root...
Downloading http://downloads.openwrt.org/chaos_calmer/15.05.1/ramips/mt7688/packages/packages/libogg_1.3.2-2_ramips_24kec.ipk.
Installing libuuid (2.25.2-4) to root...
Downloading http://downloads.openwrt.org/chaos_calmer/15.05.1/ramips/mt7688/packages/base/libuuid_2.25.2-4_ramips_24kec.ipk.
Configuring libogg.
Configuring libexif.
Configuring libflac.
Configuring libvorbis.
Configuring libuuid.
Configuring libffmpeg-mini.
Configuring minidlna.
```


**Ubuntu下安装miniDLNA**<http://blog.csdn.net/wanruirui/article/details/14222283>

1，安装

```
$ sudo apt-get install minidlna
```

2，修改配置

```
$ sudo vi /etc/minidlna.conf
```

3、你可以选择让minidlna随机启动

```
$ sudo update-rc.d minidlna defaults
```

4、启动minidlna服务

```
$ sudo service minidlna start
```

5、当你修改配置文件及媒体资源更新时，需要强制刷新，以便minidlna将最新的媒体文件进行索引

```
sudo service minidlna force-reload
```

6、查看资源个数

```
http://192.168.1.106:8200/
```

7、取消minidlna的开机自动启动

```
sudo update-rc.d -f minidlna remove
```

8、停止minidlna服务

```
sudo service minidlna stop
```

9、停止minidlna所有进程

```
sudo killall minidlna
```

10、卸载minidlna

```
sudo apt-get remove --purge minidlna
```


<h4 id="3.9"></h4>
#### 3.9 which/find

`which`用来查找可执行文件，`find`用来查找文件。另外，还有whereis,locate，不过在OpenWrt中默认没有这两个命令。

```
root@mylinkit:/# which which
/usr/bin/which
```


#### 3.10 网络工具 //net-tools, iproute2, vlan, bridge-utils, wireless-tools

#### 3.11 huawei 4g

```
kmod-usb-serial
kmod-usb-serial-option
kmod-usb-serial-wwan

usb-modeswitch
kmod-mii
kmod-usb-net
kmod-usb-wdm
kmod-usb-net-qmi-wwan
usb-modeswitch-data
uqmi

kmod-usb-net-huawei-cdc-ncm
```


#### uart

```
开两个shell,一端:cat /dev/ttyS0,另一端: echo 1111 >/dev/ttyS0
```



<h2 id="4"></h2>    
## 4. linux application

在ubuntu环境下，需要安装下面的安装包用来编译openwrt。

```
sudo apt-get install git g++ make libncurses5-dev subversion libssl-dev gawk libxml-parser-perl unzip wget python xz-utils
```

<h4 id="4.1"></h4>    
#### 4.1 openssh-service

SSH服务是一个非常好的服务，采用加密方式进行文件传输。该服务可以替代telnet的明文连接，并且可以实现文件加密传输(scp)。

<h4 id="4.2"></h4>    
#### 4.2 vnc server

*在ubuntu12系统环境下*

**vnc server安装**

```
$ sudo apt-get install vnc4server
```

**设置vnc Password**

**启动VNC Server**

```
$ vncserver
```

**关闭一个VNC窗口**

```
$ vncserver -kill :1
```


**安装gnome**

```
apt-get install gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal
```

<h4 id="4.3"></h4>    
#### 4.3 git     

<https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%92%A4%E6%B6%88%E6%93%8D%E4%BD%9C>

* git command**

* git clone
* git reset HEAD^
* git reset HEAD^^
* git reset HEAD~3/1
* git reset -hard origin/master // 回退到远程最新版本
* git remote add github https://github.com/xxxx/openwrt.git
* git remote -v
* git checkout -b huang
* git branch
* git pull github huang:master    // 从远程github拉出huang分支到本地的master
* git push github huang:master    // 上传本地的huang分支到github远程的master
* git merge



**基础命令：撤销没有提交修改**

```
$ git checkout -- config-3.18
```


**分支操作：合并master到分支**

```
  git pull origon master    // 从远程服务器更新master分支
  git checkout IR077H       // 切换到IR077H分支
  git merge Master          // 合并Master分支到当前分支
```


**库：创建本地 git 库 example.git**

```
$ cd /opt/git
$ mkdir example.git
$ cd example.git
$ git --bare init
```

**库：clone本地git库**

```
$ git clone /opt/git/example.git
```


**库：clone ssh远程库**

```
$ git clone git@192.168.1.1:/opt/git/example.git
```


**库；clone github库**

```
# git clone https://github.com/MediaTek-Labs/linkit-smart-7688-feed.git
```

**4.3.7 从服务器更新本地文档**

```
$ git pull origin master:master
$ git pull origin IR077H:IR077H
```

### 分支管理

#### 创建分支并切换到分支

```
$ git checkout -b moh
```

#### 分支切换

```
$ git checkout moh
```

#### 分支合并 //合并master到moh分支

```  
$ git checkout moh  
$ git merge master  
```

####


#### 图形界面GitK

```
$ gitk
```

gitk是linux下git自带的一个图形显示工具。可以使用这个工具在图形模式下察看详细的log文件等信息。

<h4 id="4.4"></h4>
#### 4.4 串口通讯

**screen**

ubuntu系统中使用如何使用RS232通讯，在系统中，可以采用screen命令。

```
$ ls /dev/ttyUSB*
$ sudo apt-get install screen
$ sudo screen /dev/ttyUSB0 57600
```

<h4 id="4.5"></h4>



<h4 id="4.6"></h4>
#### 4.6 scp(secure copy)安全文件传输

Secure copy or SCP is a means of securely transferring computer files between a local host and a remote host or between two remote hosts. It is based on the Secure Shell (SSH) protocol.[1]

```
 scp ~/.ssh/id_dsa.pub root@192.168.1.1:/tmp
```

<h4 id="4.7"></h4>
#### 4.7 重定向 0，1，2   >&1,>&2

0 是 < 的默认值，因此 < 与 0<是一样的;同理，> 与 1> 是一样的

  1. 标准输入(stdin):代码为0，使用<或<<；
  2. 标准输入(stdout):代码为1，使用>或>>；
  3. 标准错误输出(stderr)：代码为2，使用2>或2>>。
  0:Standard Input(STDIN)
  1:Standard Output(STDOUT)
  2:Standard Error Output(STDERR)  
  http://blog.csdn.net/thirstyblue/article/details/7974300
  http://www.cnblogs.com/Centaurus/archive/2013/05/25/3098256.html

```  
 $ls /dev 1>filename         //把命令的标准输出重新定向到一个文件filename  
 $ls /dev >>filename         //把输出追加到filename文件的末尾  
 $ls -qw  /dev  2>filename   //把标准错误重新定向到文件  
 $ls /dev &>filename         //把标准输出和错误都定向到文件  
```

<h4 id="4.7"></h4>
#### 4.8垃圾黑洞 /dev/null

```
$find /home -name .basehrc 2> /dev/null
```

<h4 id="4.8"></h4>    
#### 4.8 yes 命令

<h4 id="4.9"></h4>
#### 4.9 du 查询磁盘空间使用情况

```
# du /
```

<h4 id="4.10"></h4>    
#### 4.10 PID查询命令ps, pidof, pgrep

<h4 id="4.11"></h4>    
#### 4.11 串口通讯 uart

**得到串口数据**
```
# cat /dev/ttyUSB0
```

```
$screen /dev/cu.usbserial-XXXXXXXX 57600
```

<h4 id="4.12"></h4>    
#### 4.12 screen 串口命令

```
ubuntu@ubuntu-System-Name:~$ sudo screen /dev/ttyUSB1 57600
```

```
ubuntu@ubuntu-System-Name:~$ screen
-D -RR        Do whatever is needed to get a screen session.
-e xy         Change command characters.
-f            Flow control on, -fn = off, -fa = auto.
-h lines      Set the size of the scrollback history buffer.
-i            Interrupt output sooner when flow control is on.
-l            Login mode on (update /var/run/utmp), -ln = off.
-list         or -ls. Do nothing, just list our SockDir.
-L            Turn on output logging.
-m            ignore $STY variable, do create a new screen session.
-O            Choose optimal output rather than exact vt100 emulation.
-p window     Preselect the named window if it exists.
-q            Quiet startup. Exits with non-zero return code if unsuccessful.
-r            Reattach to a detached screen process.
-R            Reattach if possible, otherwise start a new session.
-s shell      Shell to execute rather than $SHELL.
-S sockname   Name this session <pid>.sockname instead of <pid>.<tty>.<host>.
-t title      Set title. (window's name).
-T term       Use term as $TERM for windows, rather than "screen".
-U            Tell screen to use UTF-8 encoding.
-v            Print "Screen version 4.00.03jw4 (FAU) 2-May-06".
-wipe         Do nothing, just clean up SockDir.
-x            Attach to a not detached screen. (Multi display mode).
-X            Execute <cmd> as a screen command in the specified session.
```


<h4 id="4.13"></h4> 
#### 4.13 后台运行命令

在linux中，让程序在后台运行的话，其实很简单。只要在运行程序的时候，在后面增加`&`符号就可以了。



<h2 id="5"></h2>
## 5. Uboot

<h4 id="5.1"></h4>
#### 5.1 编译Uboot

```
  $git clone https://github.com/MediaTek-Labs/linkit-smart-7688-uboot.git
  $cd linkit-smart-7688-uboot
  $sudo tar jxf buildroot-gcc342.tar.bz2 -C /opt/
  $make
```

<h4 id="5.2"></h4>
#### 5.2 修改uboot串口号
修改文件linkit-smart-7688-uboot\board\rt2880\serial.h

> line21: #define CFG\_RT2880\_CONSOLE  RT2880\_UART3

<h4 id="5.3"></h4>
#### 5.3 uboot环境变量打印/修改(printenv,setenv,saveenv)

```
MT7628 # printenv
bootcmd=tftp
bootdelay=1
baudrate=57600
ethaddr="00:AA:BB:CC:DD:10" //
ipaddr=10.10.10.123   // 设备IP
serverip=10.10.10.3   // 服务器IP
stdin=serial
stdout=serial
stderr=serial

Environment size: 149/4092 bytes
```


```
  MT7628 # setenv ipaddr 192.168.1.120
  MT7628 # setenv serverip 192.168.1.116
  MT7628 # saveenv
```

<h4 id="5.4"></h4>
#### 5.4 linkit u-boot特有烧录功能

**5.4.1 uboot更新uboot-env环境变量**

a. 在U盘中创建`lks7688.cfg`文件

```
  $cat lks7688.cfg
  wifi_ssid=moh_app
  wifi_key=12345678
```

b. 插入u盘到开发板usb端口，按下wifi按钮，复位板子。等待wifi指示灯亮后，松开wifi按钮。uboot烧录配置到uboot-env

**5.4.2 uboot更新固件**

a.烧录lks7688.img到U盘

b.插入u盘，按下wifi按钮，复位板子，等待大约5秒（wifi指示灯亮然后灭掉），松开wifi按钮，uboot开始烧录firmware

**5.4.3 uboot更新uboot**

a. 复制lks7688.ldr到U盘

b. u盘，按下wifi按钮，复位板子，等待大约20秒，松开wifi按钮，uboot更新uboot。

<h4 id="5.5"></h4>
## 5.5 Uboot源代码分析

**5.5.1 lib_mips/board.c**

linkit-smart-7688-uboot的主要代码内容在这个文件当中，如果想对该uboot进行一些功能修改的话，可以在这个文件中进行进行。


## 6 shell command

#### 6.1 pidof madplay



## 附录

#### alsa声卡驱动讲解

<https://wiki.archlinux.org/index.php/Advanced_Linux_Sound_Architecture_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)>
