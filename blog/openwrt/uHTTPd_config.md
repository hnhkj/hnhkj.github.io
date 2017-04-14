---
title: "Web Server Configuration (uHTTPd) //Web服务器配置"
author: "hnhkj@163.com"
date: "2017年2月21日"
output: html_document
---

<http://wiki.openwrt.org/doc/uci/uhttpd>

The `/etc/config/uhttpd` configuration is provided by the uhttpd web server package used since OpenWrt 10.03 (Backfire). This file defines the behaviour of the server and default values for certificates generated for SSL operation. uhttpd supports multiple instances (i.e. multiple listen ports, each with its own document root and other features) as well as cgi, php5, perl and lua.
