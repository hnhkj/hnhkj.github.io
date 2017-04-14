---
title: "OpenWrt - netifd (Network Interface Daemon) – Technical Reference"
author: "hnhkj@163.com"
date: "2016年6月13日"
output: html_document
---

<https://wiki.openwrt.org/doc/techref/netifd>

## netifd (Network Interface Daemon) – Technical Reference//网络接口进程-技术参考

* [Project's git](http://git.openwrt.org/?p=project/netifd.git;a=summary)
* netifd is available in OpenWrt since [R28499 (trunk)](https://dev.openwrt.org/changeset/28499) and published under the GPLv2.
* [commits to OpenWrt trunk regarding netifd](https://dev.openwrt.org/search?changeset=on&milestone=on&wiki=on&q=netifd&page=1&noquickjump=1)
* [Design of netifd](http://git.openwrt.org/?p=project/netifd.git;a=blob;f=DESIGN)

#### What is netifd?//什么是netifd?

netifd is an RPC-capable daemon written in C for better access to kernel APIs with the ability to listen on netlink events. Netifd has replaced the old OpenWrt-network configuration scripts, the actual scripts that configured the network, e.g.

netifd是一个用C写的RPC-capable守护进程，能够很好地访问kernel的API，有侦听netlink事件的能力。Netifd重置了旧的OpenWrt-network配置脚本，配置网络的实际脚本，例如：

* /lib/network/*.sh,
* /sbin/ifup
* some scripts in /etc/hotplug.d.)

netifd is intended to stay compatible with the existing format of /etc/config/network, the only exceptions being rare special cases like aliases or the overlay variables in /var/state (though even most of those can be easily emulated).

netifd打算保持兼容`/etc/config/network`存在的格式，仅有的例外是特殊的情况


#### Help with the development of netifd

1. test what has been ported
2. review of the code
3. help porting more of our protocol handler scripts (so far, static, ppp, pppoe, pppoa and dhcp are supported)

#### Why do we want netifd?

One thing that netifd does much better then old OpenWrt-network configuration scripts is handling configuration changes. With netfid, when the file /etc/config/network changes, you no longer have to restart all interfaces. Simply run /etc/init.d/network reload. This will issue an ubus-call to netifd, telling it to figure out the difference between runtime state and the new config and apply only that. This works on a per-interface level, even with protocol handlers written as shell scripts.

It boils down to the fact that the current network and interface setup mechanisms (via network configuration scripts) are rather constrained and inflexible:

* lack of statefulness
* tendency for raceconditions
* inability to properly nest protocols
* limited featureset of the ash shell which will not allow for complex interface operations like e.g. calculating ULAs
* you name it

Netifd will be able to manage even complex interface configurations with a mix of bonding, vlans, bridges, etc. and handle the dependencies between interfaces properly - and of course all that without adding unnecessary bloat. AFAIK there are no alternatives to netifd, e.g. connman seems to be centered around one specifific use case only: having a mobile device access the internet through multiple connections.

#### OpenWrt – operating system architecture

Whereas desktop distributions use glib and dbus (and udev) OpenWrt uses libubox and ubus (and hotplug2). This provides some pretty awesome functionality without requiring huge libraries with huge dependencies (*cough* glib).


#### Dependencies of LuCI2

The idea for LuCI2 and its different components started a long time ago: http://luci.subsignal.org/trac/browser/luci2; Code is being developed here;

* libubox (~ 12KiB) is a general purpose library which provides things like an event loop, binary blob message formatting and handling, the Linux linked list implementation, and some JSON helpers. The functions in libubox are used to write the other software in LuCI2
* ubus (~ 13KiB) is an RPC daemon similar to D-Bus but with a much friendlier C API

* ubox
* mountd
* rpcd

* PulseAudio does not depend on GLib and does not seem to depends on D-Bus neither: LFS
* FOSDEM2013: Can Linux network configuration suck less?

#### What's the difference between ubus and dbus?

dbus is bloated, its C API is very annoying to use and requires writing large amounts of boilerplate code. In fact, the pure C API is so annoying that its own API documentation states: "If you use this low-level API directly, you're signing up for some pain."

ubus is tiny and has the advantage of being easy to use from regular C code, as well as automatically making all exported API functionality also available to shell scripts with no extra effort.

"Of course, NetworkManager should be renamed to "unetwork", dbus to "ubus", PulseAudio to "usound", and X.Org-Server/Wayland-Compositor to "udisplay"; and then indescribable happiness would come down to all people of this world." – Lennart Poettering

