---
title: "OpenWrt - uHTTPd"
author: "hnhkj@163.com"
date: "2016-6-14"
output: html_document
---

<https://wiki.openwrt.org/doc/howto/http.uhttpd>

## uHTTPd

uHTTPd is a web server written from scratch by OpenWrt/LuCI developers. It is aimed towards being an efficient and stable server, suitable for lightweight tasks commonly used with embedded devices and proper integration with OpenWrt's configuration framework (UCI). In particular, it is configured by default for the [LuCI](https://wiki.openwrt.org/doc/techref/luci) web interface to administer OpenWrt. In addition, it provides all the functionality expected of present day web servers.

uHTTPd是OpenWRt/LuCI开发人员从头开始写的一个web服务器程序。它的宗旨是一个稳定，高效的服务器，适用于嵌入式设备的通用的轻量级任务和非常好地和OpenWrt的配置框架（UCI）整合。特别的，它通过默认的LuCI web接口配置来控制OpenWrt。此外，他提供今天web服务器所有期望的功能。

#### Features//特性

Built as a general purpose HTTP daemon, uHTTPd is not merely intended for running the OpenWrt's web interface but has functionality up to par with any other modern web server. Included is support for TLS (SSL), CGI and Lua. It is single threaded but supports multiple instances (i.e. multiple listen ports, each with its own document root and other features).

To use TLS (HTTPS support), the package uhttpd-mod-tls is required.

In contrast to many other web servers, it also supports [running Lua in-process](https://wiki.openwrt.org/doc/uci/uhttpd#luciwithembeddedluainterpreter), which can speed up Lua CGI scripts. Note that LuCI, which depends on Lua, is not configured in this manner by default.

#### Installation//安装

uHTTPd is the standard HTTP server for OpenWrt, but it is not included by default in the system image for the main OpenWrt releases. The package name is uhttpd. Installation can be done with:

uHTTPd是一个标准的HTTP服务器，但是，默认它没有包含在主要的OpenWrt释放版本中。包的名字是uhttpd。安装它用下面的命令。

```
opkg update
opkg install uhttpd
```

However, it is usually installed automatically as a dependency for the [web interface](https://wiki.openwrt.org/doc/howto/webinterface.overview) LuCI. Installing a web interface for administering an OpenWrt device therefore only consists of installing the web interface, in this case [LuCI](https://wiki.openwrt.org/doc/techref/luci). See the [LuCI on uHTTPd configuration page](https://wiki.openwrt.org/doc/howto/luci.essentials) to get started. Of course, you can use a web interface with [any other web server](https://wiki.openwrt.org/doc/howto/http.overview).

#### Configuration//配置

Configuration of uHTTPd integrates nicely with OpenWrt's user interface system, through standard [UCI](https://wiki.openwrt.org/doc/uci), provided since OpenWrt 10.03 (Backfire). The UCI configuration file is /etc/config/uhttpd. Since uHTTPd depends on this file directly, there is no second configuration file that gets written by UCI when settings are committed (like is the case with many other applications, such as Samba). Since uHTTPd is configured as part of the UCI system, refer to the [uHTTPd UCI configuration page](https://wiki.openwrt.org/doc/uci/uhttpd).

uHTTPd also properly provides an init script /etc/init.d/uhttpd to start or stop the service and enable it at boot time.

#### Using the default installation for publishing files.//使用默认安装公布文件

One could use the default installation for publishing files. A quick example is to disable the redirect to luci scripts.

```
mv /www/index.html /www/index.html.orig
```

Done this anything is shown as list of file and even symbolic links works.