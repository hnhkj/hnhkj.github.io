---
title: "OpenWrt - networking/Linux Network Interfaces"
author: "hnhkj@163.com"
date: "2016-6-13"
output: html_document
---

<https://wiki.openwrt.org/doc/networking/network.interfaces>

## Types of network interfaces //网络接口类型

The Linux kernel universally distinguishes between two types of software network interfaces:

linux kernel通常区分两类网络接口：

#### Physical Network Interfaces //物理网络接口

eth0, eth8, radio0, wlan19, .. always represent an actual network hardware device such as a NIC, WNIC or some other kind of Modem. As soon as the device driver is loaded into the Kernel a corresponding physical network interface becomes present and available.

eth0, eth8, radio0, wlan19, .. 总是代表一个真实的网络硬件设备，例如NIC，WNIC或其它类型的Modem。一旦驱动程序装入kernel，一个对应的物理网络接口变得真实有效。

Any physical network interface is a named software representation by the operating system to the user to enable him to configure the hardware network device and also to integrate it into programs and scripts.

任何物理网络接口是由操作系统的用户名为软件表示，使他能够配置硬件网络设备，也将其集成到程序和脚本。

#### Virtual Network Interfaces //虚拟网络接口

lo, eth0:1, eth0.1, vlan2, br0, pppoe-dsl, gre0, sit0 tun0, imq0, teql0, .. are virtual network interfaces that do NOT represent an existent hardware device but are linked to one (otherwise they would be useless). Virtual network interfaces were invented to give the system administrator maximum flexibility when configuring a Linux-based operating system. A virtual network interface is generally associated with a physical network interface (eth6) or another virtual interface (eth6.9) or be stand alone such as the loopback interface lo.

lo，eth0:1，eth0.1，vlan2，br0，pppoe-dsl，gre0，sit0 tun0，imq0，teql0，...是虚拟网络接口，不表示一个存在的硬件设备，但都与一（否则他们将是无用的）。虚拟网络接口被发明配置一个基于Linux的操作系统时，给系统管理员最大的灵活性。虚拟网络接口通常与物理网络接口（eth6）或另一个虚拟接口（eth6.9）或者被独立诸如相关回环接口 lo。


#### Types of Virtual Network Interfaces