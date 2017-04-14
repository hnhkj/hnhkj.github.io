# linkit eeprom

---
title: "LinkIt Smart 7688 EEPROM 相关问题研究"
author: "hnhkj@163.com"
date: "2016年6月12日"
output: html_document
---

## 前言

参考了MediaTek的官方信息和代码之后，在使用LinkIt Smart 7688的过程中是非常顺利的。网络，wifi，SD卡，U盘等等，都是非常容易实现的，只要参考MediaTek的官方文档就可以了。但是，对于喜欢折腾得我，却发现必要但是却被忽略的一个环节。就是MAC和相关参数的设定。当我，采用了一个完全新的SPI-Flash的时候，烧录了UBoot，Firmware。但是，它却不能正常地工作。关键是有一些参数需要设置，而MediaTek并没有说明这一步分的操作方法。

我查询了很多的资料，都没有清晰和明确的解答。当然，如果采用MT7688原生代码开发的话可能会容易点。因为，原生代码中用用了和很多的命令可以帮我们解决这些问题。例如MAC地址的修改，Wifi参数的设定等等。但是，在新的OpenWrt中，却没有了他们踪迹。因而，我产生疑问。为什么在LinkIt Smart 7688中，他们摒弃了这些命令。没有了这些命令，我们该如何实现我们的目的呢？这些疑问，我一直找到很好的解答。因而，我只能自己摸索。摸索着试图寻找出答案。

## EEPROM

无论是原生的MT7688代码，还是最新的Openwrt代码，他们的核心参数仍然是保存在EEPROM中的。如果你有EEPOM的话，那么他们是有意义的。当然，现在的设计是没有EEPOM的。EEROM中的数据完全保存在了SPI-FLASH。就是MTD2中，它们是完全一致的。我们可以通过`dd/mtd`对MTD2数据进行读写。当然，这也是有前提条件的。默认的Linkit Smart 7688的Openwrt默认是不能写入MTD2的，它是受保护的。

#### 使能MTD2写功能

修改LINKIT7688文件，删除read-only行。重新编译后就可以通过'mtd'命令写数据到MTD2。

```
/target/linux/ramips/dts/LINKIT7688 
line82-86: 
factory: partition@40000 { 
label = "factory"; 
reg = <0x40000 0x10000>; 
read-only; 
}; 
```

#### 获取MTD2内容命令

```
# dd if=/dev/mtd2 of=/tmp/factory.bin
```

#### MTD2数据内容分析

*reference: MT7628/7688 EEPROM Content programming guide/confidential A*

|Name|Offset|Type|Content|
|-----------------|----|----|--------|
|CHIP\_ID         |0000|DC|CHIP_ID[15:0]|
|EEPROM\_REV      |0002|DC|REL\_REV&ENG\_REV|
|WLAN\_MAC\_B1B0|0004|RW|MAC\_ADDR\_L[15:0]|ra0|
|WLAN\_MAC\_B3B2|0006|RW|MAC\_ADDR\_M[15:0]|ra0|
|WLAN\_MAC\_B5B4|0008|RW|MAC\_ADDR\_H[15:0]|ra0|
|MAC0\_B1B0      |0028|RW|MAC0\_ADDR\_L[15:0]|eth0|
|MAC0\_B3B2      |002A|RW|MAC0\_ADDR\_M[15:0]|eth0|
|MAC0\_B5B4      |002C|RW|MAC0\_ADDR\_H[15:0]|eth0|
|MAC1\_B1B0      |002E|RW|MAC1\_ADDR\_L[15:0]|
|MAC1\_B3B2      |0030|RW|MAC1\_ADDR\_M[15:0]|
|MAC1\_B5B4      |0032|RW|MAC1\_ADDR\_H[15:0]|
......


```
[0000H] 28 76 00 02 9c 65 f9 1b 18 55 00 00 00 00 00 00
[0010H] ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  
[0020H] 00 00 00 00 20 00 00 00 9c 65 f9 1b 10 27 9c 65  
[0030H] f9 1b 10 27 00 34 00 20 ff ff 00 01 00 00 00 00  
[0040H] 00 00 22 00 00 00 00 00 30 00 00 00 00 00 00 00  
[0050H] 82 00 00 94 40 b2 c0 ca 1a c2 c2 c2 40 ca 23 00  
[0060H] 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
[0070H] 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
[0080H] 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
[0090H] 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   
[00A0H] c6 c6 c4 c4 c4 c0 c0 c4 c4 c4 c4 c4 c0 c0 00 00  
[00B0H] 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
[00C0H] 00 00 00 00 00 00 00 00 00 1a 22 2a 31 35 01 35  
[00D0H] 39 40 46 4d 7f 7f 7f 00 00 00 00 00 00 00 00 00  
[00E0H] 11 1d 11 1d 1c 35 1c 35 1e 35 1e 35 17 19 17 19  
[00F0H] 02 00 00 00 c0 00 80 88 0a 00 00 00 00 00 00 00  
[0100H] ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  
[0110H] ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  
[0120H] 00 00 00 00 00 00 00 00 00 00 00 00 00 00 77 00  
[0130H] 11 1d 11 1d 15 7f 15 7f 17 7f 17 7f 10 3b 10 3b  
......  
[0400H] 4c 49 4e 4b 49 54 53 37 36 38 38 20 20 20 20 20 ->LINKITS7688     <-
[0410H] 52 45 56 33 20 20 20 20 20 20 20 20 20 20 20 20 ->REV3            <-
```



## LinkIt Smart 7688 OS中寻觅踪迹

#### \lib\functions\network.sh

* \_\_network\_ifstatus()
* network\_get\_ipaddr()
* network\_get\_ipaddr6()
* network\_get\_subnet()
* network\_get\_subnet6()
* network\_get\_prefix6()
* network\_get\_ipaddrs()
* network\_get\_ipaddrs6()
* network\_get\_ipaddrs_all()
* network\_get\_subnets()
* network\_get\_subnets6()
* network\_get\_prefixes6()
* network\_get\_gateway()
* network\_get\_gateway6()
* network\_get\_dnsserver()
* network\_get\_dnssearch()
* \_\_network\_wan()
* network\_find\_wan()
* network\_find\_wan6() 
* network\_is\_up()
* network\_get\_protocol()
* network\_get\_device()
* network\_get\_physdev()
* network\_defer\_device()
* network\_ready\_device()
* network\_flush\_cache()

#### \lib\functions\system.sh

* find\_mtd\_chardev()
* mtd\_get\_mac\_ascii()
* mtd\_get\_mac\_binary()
* mtd\_get\_par\_size()
* macaddr\_add()
* macaddr\_setbit_la()
* macaddr\_2bin()
* macaddr\_canonicalize()

#### /lib/firmware/mt7628.eeprom


#### /etc/config/dhcp

/lib/functions.sh
/lib/ramips.sh
/lib/functions/uci-defaults-new.sh
/lib/functions/system.sh