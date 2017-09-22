# openwrt - tcpdump抓取指定端口的数据包

---
title: "OpenWrt - tcpdump抓取指定端口的数据包"
author: "hnhkj@163.com"
date: "2016-10-10"
output: html_document
---

## 实现目的

当前，我采用的是`DM06-IOT_V22`开发板。该开发板采用的是MT7688芯片，外接了2个网络口。我需要实现的功能是，一个端口port0做WAN，另一个端口port1做LAN。我可以通过tcpdump捕捉任意一端口的网络数据包。其实只要的目的是想实现下面的连接方式，实现对IPPhone的数据捕捉，实现对IPPhone的通话录音。

IPPhone(192.168.1.12smilie --> Port1 --> bridge/switch/or other(I dont know) --> Port0 --> router(192.168.1.1)--> internet 

## 实现方法

我采用了默认的openwrt-15.05.1源码进行编译下载，系统可以正常工作，LAN口连接设备可以自动捕获网络地址并且正常连接到Internet。*注意：默认情况下我们要禁止MMC功能，就我目前的测试情况看，该功能会影响到PORT1的正常工作。虽然，有人对MMC源码进行了修改，但是仍然存在一些问题。<https://github.com/gq213/openwrt-DM06>*

##  1.2.1 Network

The network configuration in Kamikaze is stored in /etc/config/network and is divided into interface configurations. Each interface configuration either refers directly to an ethernet/wifi interface (eth0, wl0, ..) or to a bridge containing multiple interfaces. It looks like this:

网络配置保存在/etc/config/network，分成接口配置。每一个接口配置即可以直接指向一个ethnet/wifi接口（eth0,wl0,...）也可以配置bridge包含多个接口。它像下面：

```
  config interface     "lan" 
      option ifname    "eth0" 
      option proto     "static" 
      option ipaddr    "192.168.1.1" 
      option netmask   "255.255.255.0" 
      option gateway   "192.168.1.254" 
      option dns       "192.168.1.254"
```

ifname specifies the Linux interface name. If you want to use bridging on one or more interfaces, set ifname to a list of interfaces and add:


      option type     "bridge"

It is possible to use VLAN tagging on an interface simply by adding the VLAN IDs to it, e.g. eth0.1. These can be nested as well.
This sets up a simple static configuration for eth0. proto specifies the protocol used for the interface. The default image usually provides ’none’ ’static’, ’dhcp’ and ’pppoe’. Others can be added by installing additional packages.
When using the ’static’ method like in the example, the options ipaddr and netmask are mandatory, while gateway and dns are optional. You can specify more than one DNS server, separated with spaces.
DHCP currently only accepts ipaddr (IP address to request from the server) and hostname (client hostname identify as) - both are optional.
PPP based protocols (pppoe, pptp, ...) accept these options:

username 
The PPP username (usually with PAP authentication)
password 
The PPP password
keepalive 
Ping the PPP server (using LCP). The value of this option specifies the maximum number of failed pings before reconnecting. The ping interval defaults to 5, but can be changed by appending ",<interval>" to the keepalive value
demand 
Use Dial on Demand (value specifies the maximum idle time.
server: (pptp) 
The remote pptp server IP
For all protocol types, you can also specify the MTU by using the mtu option.
Setting up static routes

You can set up static routes for a specific interface that will be brought up after the interface is configured.
Simply add a config section like this:
  config route foo 
   option interface lan 
   option target 1.1.1.0 
   option netmask 255.255.255.0 
   option gateway 192.168.1.1
The name for the route section is optional, the interface, target and gateway options are mandatory. Leaving out the netmask option will turn the route into a host route.

#### Setting up the switch (currently broadcom only)

The switch configuration is set by adding a ’switch’ config section. Example:

```
  config switch       "eth0" 
      option vlan0    "1 2 3 4 5*" 
      option vlan1    "0 5"
```
      
On Broadcom hardware the section name needs to be eth0, as the switch driver does not detect the switch on any other physical device. Every vlan option needs to have the name vlan<n> where <n> is the VLAN number as used in the switch driver. As value it takes a list of ports with these optional suffixes:

在Broadcom硬件上，section name是eth0,由于switch驱动不检测switch在什么物理设备上。每一个vlan控制需要有vlan<n>，<n>是VLAN号由switch驱动使用。它的value携带一个可选后缀的port列表。

* ’*’: Set the default VLAN (PVID) of the Port to the current VLAN //设置port的默认VLAN(PVID)到当前VLAN
* ’u’: Force the port to be untagged //强制port 不标记
* ’t’: Force the port to be tagged //强制port标记

The CPU port defaults to tagged, all other ports to untagged. On Broadcom hardware the CPU port is always 5. The other ports may vary with different hardware.

CPU port默认被标记，所有其它端口不标记。在Broadcom硬件上，CPU port总是5。其它ports可能根据不同的硬件有不同。

For instance, if you wish to have 3 vlans, like one 3-port switch, 1 port in a DMZ, and another one as your WAN interface, use the following configuration :

例如，如果你希望有3个VLAN，一个3端口switch，1端口DMZ，另一个作为你的WAN接口，使用下面的配置：

```
  config switch       "eth0" 
      option vlan0    "1 2 3 5*" 
      option vlan1    "0 5" 
      option vlan2    "4 5"
```

Three interfaces will be automatically created using this switch layout : eth0.0 (vlan0), eth0.1 (vlan1) and eth0.2 (vlan2). You can then assign those interfaces to a custom network configuration name like lan, wan or dmz for instance.

3个接口将自动用switch layout创建：eth0.0(vlan0), eth0.1(vlan1)和eth0.2(vlan2)。然后，你可以这些interface给指定的网络配置名，像lan,wan或dmz。

#### Setting up IPv6 connectivity

OpenWrt supports IPv6 connectivity using PPP, Tunnel brokers or static assignment.
If you use PPP, IPv6 will be setup using IP6CP and there is nothing to configure.
To setup an IPv6 tunnel to a tunnel broker, you can install the 6scripts package and edit the /etc/config/6tunnel file and change the settings accordingly :
  config 6tunnel 
          option tnlifname     ’sixbone’ 
          option remoteip4        ’1.0.0.1’ 
          option localip4         ’1.0.0.2’ 
          option localip6         ’2001::DEAD::BEEF::1’ 
          option prefix           ’/64’
’tnlifname’: Set the interface name of the IPv6 in IPv4 tunnel
’remoteip4’: IP address of the remote end to establish the 6in4 tunnel. This address is given by the tunnel broker
’localip4’: IP address of your router to establish the 6in4 tunnel. It will usually match your WAN IP address.
’localip6’: IPv6 address to setup on your tunnel side This address is given by the tunnel broker
’prefix’: IPv6 prefix to setup on the LAN.
Using the same package you can also setup an IPv6 bridged connection :
  config 6bridge 
   option bridge ’br6’
By default the script bridges the WAN interface with the LAN interface and uses ebtables to filter anything that is not IPv6 on the bridge.
IPv6 static addressing is also supported using a similar setup as IPv4 but with the ip6 prefixing (when applicable).
  config interface     "lan" 
      option ifname    "eth0" 
      option proto     "static" 
      option ip6addr    "fe80::200:ff:fe00:0/64" 
      option ip6gw     "2001::DEAF:BEE:1"
      
      
## tcpdump问题

#### 我采用tcpdump对指定端口不能读取想要的数据包


```
# tcpdump i eth0.1
```


## switch


## bridge网桥

通过分析，默认的网络端口应该采用的是网桥功能。

## 问题分析

对于tcpdump出现的问题，我对openwrt网络进行了一些分析。经过了对网络的询问，有朋友指出应该打开switch功能，如果是没有两个独立的硬件网卡的话。使用switch的VLAN功能。详细的方法仍在进行测试。


## /etc/confing/netware配置分析


#### 原来配置

该配置默认禁止使用switch，port0-6在同一个VLAN0中。

```
root@ipRec:/# cat /etc/config/network

config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fd4b:2190:1952::/48'

config interface 'lan'
        option ifname 'eth0'
        option force_link '1'
        option macaddr '00:0c:43:e1:76:2a'
        option type 'bridge'
        option proto 'static'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option ipaddr '192.168.100.1'

config switch
        option name 'switch0'
        option reset '1'
        option enable_vlan '0'

config interface 'wan'
        option proto 'dhcp'
```


```
root@ipRec:/# swconfig dev rt305x show
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
        recv_good: 987
        tr_bad: 0
        tr_good: 193
        pvid: 0
        link: port:0 link:up speed:100baseT full-duplex
Port 1:
        disable: 0
        doubletag: 1
        untag: 1
        led: 5
        lan: 1
        recv_bad: 0
        recv_good: 154
        tr_bad: 0
        tr_good: 1010
        pvid: 0
        link: port:1 link:up speed:100baseT full-duplex
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


#### 修改了Switch的VLAN配置1

该配置设置了2个VLAN1，VLAN2。Port0 & 6t在VLAN1, Port1 & 6t在VLAN2。设置eth0.1为WAN，eth0.2为LAN。但是IPPhone不能连接出去。

```
root@ipRec:/# cat /etc/config/network

config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fd4b:2190:1952::/48'

config interface 'lan'
        option ifname 'eth0.2'
        option force_link '1'
        option macaddr '00:0c:43:e1:76:2a'
        option type 'bridge'
        option proto 'static'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option ipaddr '192.168.100.1'

config switch
        option name 'switch0'
        option reset '1'
        option enable_vlan '1'

config switch_vlan
        option device 'switch0'
        option vlan '1'
        option ports '0 6t'

config switch_vlan
        option device 'switch0'
        option vlan '2'
        option ports '1 6t'

config interface 'wan'
        option proto 'dhcp'
        option ifname 'eth0.1'
        option force_link '1'
```


```
root@ipRec:/# swconfig dev rt305x show
Global attributes:
        enable_vlan: 1
        alternate_vlan_disable: 0
        bc_storm_protect: 0
        led_frequency: 0
Port 0:
        disable: 0
        doubletag: 0
        untag: 1
        led: 5
        lan: 1
        recv_bad: 0
        recv_good: 621
        tr_bad: 0
        tr_good: 97
        pvid: 1
        link: port:0 link:up speed:100baseT full-duplex
Port 1:
        disable: 0
        doubletag: 0
        untag: 1
        led: 5
        lan: 1
        recv_bad: 0
        recv_good: 21
        tr_bad: 0
        tr_good: 19
        pvid: 2
        link: port:1 link:up speed:100baseT full-duplex
Port 2:
        disable: 0
        doubletag: 0
        untag: 0
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
        doubletag: 0
        untag: 0
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
        doubletag: 0
        untag: 0
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
        doubletag: 0
        untag: 0
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
        doubletag: 0
        untag: 0
        led: ???
        lan: ???
        recv_bad: ???
        recv_good: ???
        tr_bad: ???
        tr_good: ???
        pvid: 0
        link: port:6 link:up speed:1000baseT full-duplex
VLAN 1:
        ports: 0 6t
VLAN 2:
        ports: 1 6t

```
#### 修改了Switch的VLAN配置2

