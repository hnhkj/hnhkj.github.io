# Eclipse IDE

---
title: "Openwrt building and debugging C/C++ Programs with Eclipse"
author: "hnhkj@163.com"
date: "2016-12-16"
output: word_document
---

<http://labs.mediatek.com/site/global/developer_tools/mediatek_linkit_smart_7688/training_docs/c_programming/cross_compilation_eclipse/index.gsp>

This article describes how to setup Eclipse IDE for C/C++ Developers and OpenWrt SDK to build and debug programs running on LinkIt Smart 7688.

这篇文章描述如何设置Eclipse IDE和Openwrt SDK，在LinkIt Smart 7688上进行编译和调试程序。

After proper setup, Eclipse will be able to build, upload and attach debugger to your C/C++ program that runs on LinkIt Smart 7688. This can be pretty helpful if you are writing your own C/C++ programs.

## Requirements//需求

* Linkit Smart 7688或Linkit Smart 7688 Duo
* Mac OS X或Ubuntu Linux机子使用LinkitSmart 7688 OPenWrt SDK。 Follow the cross compilation instructions to make sure you have a working SDK.
* [Eclipse IDE for C/C++ Developers](http://www.eclipse.org/downloads/packages/eclipse-ide-cc-developers/mars1)
* Eclipse IDE requires JVM. Follow the IDE instructions to install proper JVM version.

## Quick Steps//简单步骤

* Install and setup SFTP on LinkIt Smart 7688 with opkg
* Install Eclipse C/C++ GCC Cross Compiler Support and Remote System explorer End-User Runtime. Newer versions of Eclipse may have these packages installed already.
* Create an Eclipse C/C++ Cross GCC project
* Setup STAGING_DIR environment var, tool command prefix, tool path and GDB tool path in the project setting.
* Build and setup the log-in password to LinkIt Smart 7688.
* The IDE will automatically build, upload, and execute the resulting binary.

## Step-by-step

#### Download and setup OpenWrtSDK

#### install `OpenWrtSDK` to Eclipse IDE.

-->Properties for xxx-->C/C++ Build-->Settings-->Tool Settings-->Cross Settings-->Prefix-->`mipsel-openwrt-linux-uclibc-`

-->Properties for xxx-->C/C++ Build-->Settings-->Tool Settings-->Cross Settings-->Prefix-->`/home/ubuntu/openwrt/OpenWrt-SDK-ramips-mt7621_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-i686/staging_dir/toolchain-mipsel_1004kc+dsp_gcc-4.8-linaro_uClibc-0.9.33.2/bin`


## Problem

#### add -llibpcap

-->Properties for xxx-->C/C++ Build-->Settings-->Tool Settings-->Cross G++ Linker-->Libraries-->Libraries-->`pcap`


