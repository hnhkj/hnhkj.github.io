---
title: "OpenWrt - uci/network/switch"
author: "hnhkj@163.com"
date: "2016-6-16"
output: html_document
---

## Switch Documentation //switch文档

*Make sure you can safemode or TTL before changing network/switch settings*

This page assumes you know what this is and why you want it. (Someone please add info or another page and a link).

If your device has more than 1 LAN port, it may contain a special connection between the different ports called switch. Most likely the internals may look like in the following picture

![image001](img\asus-internals-default.png)

If you want to change how these ports are connected to each other you need to configure the switch of your device (see also network.interfaces)

Different routers have different switch layouts, so look at the Wiki for your specific device. The TP-Link Archer C7 has eth0 = WAN, and eth1 = LAN (the 4 switch ports). Port 0 of the switch = eth1 (labelled CPU in Luci), Port 6 = eth0. Port 1 is labelled WAN in Luci. Look at the wiki for your router. Every router is different. The popular TP-Link WDR4300 only has eth0.

## UCI config, swconfig style

#### Known problems

* if a switch interface (for the cpu point of view) is controlling several 'physical interfaces', every time than one physical interface is connected all the switch interface result connected (that means all the ports ethN.Y are UP) and therefore every rule (routing for example) is applied. This could cause serious problem, for example if one relies on the automatic mechanism of routing metric when one route is not available anymore.
    * One way to detect this is: `swconfig dev <switch_interface_name> show | grep link` or see [swconfig](https://wiki.openwrt.org/doc/techref/swconfig)

#### Assumptions //假设

Some of the assumptions, does not see to add up with the provided diagram. Someone familiar with the matter, should either fix them or add a better explanation.

* device is running kernel 2.6 or 3 // 设备运行内核2.6或3以上
* device uses an swconfig type switch configuration // 设备使用一个swconfig类型的switch配置
* The switch is on eth1. (Many are on eth0) [Howto find out: → /proc/switch directory appears to contain the right eth number for the switch. please confirm], and also on chips like rtl8366s // switch在eth1。（许多在eth0）[如何查找：→/proc/switch目录显示包含正确的eth号，请校验]。也有在芯片上，向rtl8366s。
* Five-port switch with 0-3 connected externally, 4 not connected, and 5 connected to the CPU's eth1 interface (which adds up to six ports except that 4 is not counted) // 5口switch，0-3连接外部，4没有接，5连接到CPU's eth1接口（这意味着达到了6个口，端口4除外）
* vlan0 is to be all external ports but the last one //vlan0 是所有外部口，至少有一个。
* vlan1 is only the last external port [Howto find out which Port corresponds:] //vlan1 是最后外部口[Howto find out which Port corresponds:]
* vlan0 is the default vlan, meaning if a packet is untagged, it will be treated a vlan0 packet //vlan0时默认的vlan, 意思是如果一个包没有标键，它将被视为vlan0包。

#### The configuration

**The Switch**

