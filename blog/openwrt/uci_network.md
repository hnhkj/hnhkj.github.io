---
title: "OpenWrt - uci/network configuration"
author: "hnhkj@163.com"
date: "2016-6-16"
output: html_document
---

<https://wiki.openwrt.org/doc/uci/network>

## Network configuration

The central network configuration is located in the file /etc/config/network. This configuration file is responsible for defining switch VLANs, interface configurations and network routes. After editing and saving /etc/config/network you need to execute

重要的网络配置位于`/etc/config/network`文件。这个配置文件响应`switch VLAN`规定，接口配置和万网络routes。编辑保存`/etc/config/network`后，你需要执行它。

```
/etc/init.d/network reload
```

to stop and restart the network before any changes take effect. Rebooting the router is not necessary.

任何改变生效前要停止和重启网络。不必要重启路由器。

* <https://dev.openwrt.org/browser/branches/attitude_adjustment/package/base-files/files/etc/config/network>
* <https://dev.openwrt.org/browser/trunk/package/base-files/files/etc/config/network>

Feel free to inform yourself about [netifd](https://wiki.openwrt.org/doc/techref/netifd) (Network Interface Daemon).

你也可以查看netifd(网络接口守护进程)

## Sections //区域

Below is an overview of the section types that may be defined in the network configuration. A minimal network configuration for a router usually consists of at least two interfaces (lan and wan) and a switch section if applicable.

下面是一个节类型的预览，它可以定义到network配置文件中。对于一个路由器来讲，一个最小的networrk配置通常包含至少两个接口（lan和wan)和一个switch部分，如果适用。

#### Global Settings//全局设置

The globals section is available in Barrier Breaker and later releases.

全局区域在Barrier Breaker和之后的版本是有效的。

The globals section contains interface-independent options affecting the network configuration in general.

一般来讲，全局区域包含独立接口控制影响网络配置。

|Name|    Type|    Required|    Default Description|
|----|--------|------------|-----------------------|
|ula_prefix|  IPv6-prefix| no | (none)|  IPv6 ULA-Prefix for this device|

#### Switch

The switch section is responsible for partitioning the switch into several VLANs which appear as independent interfaces in the system although they share the same hardware. Not every OpenWrt supported device (or architecture, like x86) has a programmable switch, therefore this section might not be present on some platforms. Please also note, that some switches only support 4Bit-VLANs.

`switch`区域负责份撇switch到几个VLAN中，作为独立接口在系统出现，尽管他们共享相同的硬件。不是每一个OpenWrt支持设备(or architecture, like x86)有可编程的`switch`，因此，这部分区域不可以出现在一些平台。另外，请注意一些switch仅仅支持4Bit-VLANs.

There are currently two different configuration formats in use, one for the legacy /proc/switch/ API and one for the newer swconfig-based switch configuration.

当前，有两个不同的配置格式适用，一个为遗留的`/pro/switch/API`和另一为最新的`swconfig-based swtich`配置。

#### `/proc/switch`

This variant is actually only found on Broadcom devices like the WRT54GL.

这个变种实际上只在Broadcom设备上发现，像WRT54GL。

A typical configuration for it looks like this:

一个典型的配置如下：

```
config 'switch' 'eth0'
        option 'vlan0' '0 1 2 3 5*'
        option 'vlan1' '4 5'
```

The eth0 identifier specifies the switch the section is belonging to. VLANs are defined by vlan# options with # being the VLAN number. For further information refer to the [switch documentation](https://wiki.openwrt.org/doc/uci/network/switch).

`eth0`标识指定switch区域属于它。VLAN有`vlan#`控制定义，`#`是VLAN号。进一步的信息可以参考switch documentation.

#### `swconfig`

The newer swconfig-framework is intended to replace the legacy switch configuration.

最新的swconfig-framework替代了遗留下来的`switch`配置。

Configuration for swconfig have a slightly different structure with one extra section per VLAN. The example below shows a typical configuration:

`swconfig`配置有一个轻微的不同，在每一个VLAN使用一个额外的区域。下面的显示一个典型的配置。

```
config 'switch' 'eth0'
        option 'reset' '1'
        option 'enable_vlan' '1'

config 'switch_vlan' 'eth0_1'
        option 'device' 'eth0'
        option 'vlan' '1'
        option 'ports' '0 1 2 3 5t'

config 'switch_vlan' 'eth0_2'
        option 'device' 'eth0'
        option 'vlan' '2'
        option 'ports' '4 5t'
```


Common properties are defined within the switch section; vlan specific properties are located in additional switch_vlan sections linked to the switch section through the device option. The complete layout is explained in the [switch documentation](https://wiki.openwrt.org/doc/uci/network/switch).

通用属性定义在`switch`区域内部。

#### Interfaces

Sections of the type interface declare logical networks serving as containers for IP address settings, aliases, routes, physical interface names and firewall rules - they play a central role within the OpenWrt configuration concept.

`interface`部分声明逻辑网路服务作为一个容器，包含IP Address设置，aliases别名，physical interface名称和firware规则 - 他们使用内置在配置概念中的一个重要的规则。

A minimal interface declaration consists of the following lines:

一个最小接口声明内容如下：

```
config 'interface' 'wan'
        option 'proto' 'dhcp'
        option 'ifname' 'eth0.1'
```

* wan is a unique logical interface name//`wan`是唯一的逻辑接口名字
* dhcp specifies the interface protocol, DHCP in this example//`dhcp`指定接口协议
* eth0.1 is the physical interface associated with this section//`eth0.1`是和这部分关联的物理接口

The system limits the physical interface name length to 15 characters including the automatically added prefix that is added for some protocols (e.g. "6in4-", "pppoa-", "pppoe-") or due to bridge status ("br-"). Depending on the protocol type, the logical interface name may thus be limited to only 9 characters. E.g. 'abcde67890' is a valid interface name for a normal interface using dhcp, but not for a pppoe interface where the final name would be 'pppoe-abcde67890', which is >15 chars. Using a too long name may lead into errors, as some of the settings in network, firewall or dhcp config may be left unapplied.

系统显示物理接口程度15字符，包括增加到一些协议(e.g. "6in4-", "pppoa-", "pppoe-")或bridge状态的prefix。依赖协议类型，逻辑接口名限制只有9个字符。E.g. 'abcde67890' 有效的接口

The interface protocol may be one of the following:
