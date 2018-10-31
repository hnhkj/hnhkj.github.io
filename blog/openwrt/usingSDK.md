---
title: "OpenWrt-Using the SDK"
author: "hnhkj@163.com"
date: "2016-6-24"
output: html_document
---


## Using the SDK //使用SDK

The SDK is a relocatable, precompiled OpenWrt toolchain suitable to cross compile single userspace packages for a specific target without compiling the whole system from scratch.

Reasons for using the SDK are:

* Compile custom software for a specific release while ensuring binary and feature compatibility
* Compile newer versions of certain packages
* Recompile existing packages with custom patches or different features

## Obtain SDK //获取SDK

You can either download an already compiled SDK, or compile it yourself by using the "make menuconfig" command.

#### Prerequisites //先决条件

Please see the OpenWRT Buildroot page to install the needed software to build the packages on the SDK.

Note: On some hosts it is needed to install the ccache package

#### Download

You should find bz2-archives ready for download in the corresponding download directory:

* Trunk SDK → Platform → OpenWrt-SDK-<Platform>-for-linux-x86_64-gcc-<version>-linaro-uClibc-<version>.tar.bz2
* Stable SDK "Chaos Calmer (15.05.1)" → Platform → OpenWrt-SDK-<Platform>-for-linux-x86_64-gcc-<version>-linaro-uClibc-<version>.tar.bz2
* Legacy SDKs (Historic Releases), → Platform → OpenWrt-SDK-<Platform>-for-linux-x86_64-gcc-<version>-linaro-uClibc-<version>.tar.bz2

#### Package Feeds

After decompressing the SDK archive, edit the feeds.conf.default file to download the needed package definitions

#### Usage //习惯用法

By default the SDK ships with no package definitions. Makefiles for packages to compile must be checked out from the OpenWrt repository and placed into the package/ directory first.

#### Obtain Definitions

* Use the ./scripts/feeds update -a command to obtain package definitions.
* After the definitions have been updates, execute ./scripts/feeds install <packagename> to prepare the package and its dependencies.

#### Compile Packages

After the Makefile is in place, the usual buildroot commands apply:

* make package/example/download - download the soures of example //下载example源代码
* make package/example/prepare - extract the sources, apply patches and download if necessary//下载（如果是必须的），解压源码，打补丁
* make package/example/compile - compile example, prepare and download if necessary//下载（如果是必须的）,预处理，编译
* make package/example/clean - clean the sourcecode//清除源代码
* make package/index - build a repository index to make the output directory usable as local opkg source//编译一个repository索引，创建一个输出目录用来作为本地opkg源。

Some packages are built on host:

```
$ make package/example/host/{clean,compile} V=99
```

The common command to recompile a package example and enable verbose output is:

```
$ make package/example/{clean,compile} V=99
```

After the compilation is finished, the generated .ipk files are placed in the bin directory.

The output of make might contain WARNING: your configuration is out of sync. Please run make menuconfig, oldconfig or defconfig!. That warning is misleading and wrong in the SDK case. Since everything is precompiled you cannot run oldconfig (see Why is the SDK configuration out of sync?).

#### Example: existing package

The example below rebuilds tmux.

```
$ ./scripts/feeds install tmux
Installing package 'tmux'
Installing package 'toolchain'
Installing package 'ncurses'
Installing package 'libevent2'
Installing package 'openssl'
Installing package 'zlib'
Installing package 'ocf-crypto-headers'
$ make package/tmux/download
Collecting package info: done
tmp/.config-package.in:36:warning: ignoring type redefinition of 'PACKAGE_libc' from 'boolean' to 'tristate'
tmp/.config-package.in:64:warning: ignoring type redefinition of 'PACKAGE_libgcc' from 'boolean' to 'tristate'
#
# configuration written to .config
#
 make[1] package/tmux/download
 make[2] -C feeds/packages/utils/tmux download
$ make package/tmux/prepare
tmp/.config-package.in:36:warning: ignoring type redefinition of 'PACKAGE_libc' from 'boolean' to 'tristate'
tmp/.config-package.in:64:warning: ignoring type redefinition of 'PACKAGE_libgcc' from 'boolean' to 'tristate'
#
# configuration written to .config
#
 make[1] package/tmux/prepare
 make[2] -C feeds/packages/utils/tmux prepare
$ make package/tmux/compile
tmp/.config-package.in:36:warning: ignoring type redefinition of 'PACKAGE_libc' from 'boolean' to 'tristate'
tmp/.config-package.in:64:warning: ignoring type redefinition of 'PACKAGE_libgcc' from 'boolean' to 'tristate'
#
# configuration written to .config
#
 make[1] package/tmux/compile
 make[2] -C feeds/base/package/libs/toolchain compile
 make[2] -C feeds/base/package/libs/ocf-crypto-headers compile
 make[2] -C feeds/base/package/libs/zlib compile
 make[2] -C feeds/base/package/libs/openssl compile
 make[2] -C feeds/base/package/libs/libevent2 compile
 make[2] -C feeds/base/package/libs/ncurses host-compile
 make[2] -C feeds/base/package/libs/ncurses compile
 make[2] -C feeds/base/package/libs/ncurses compile
 make[2] -C feeds/base/package/libs/ncurses compile
 make[2] -C feeds/packages/utils/tmux compile

 make[1] package/index

 make[1] package/index
$ ls bin/ar71xx/packages/packages
tmux_1.9a-1_ar71xx.ipk
```


#### Build your own packages

See Creating packages

## Troubleshooting

:!: Some SDK versions have bugs.

Bug: BB SDK for BRCM2708: wants to compile with "ccache_cc" see https://dev.openwrt.org/ticket/13949

Bug: BB SDK for BRCM2708: static compilation broken

## Links

Archive : obtain.firmware.sdk.old