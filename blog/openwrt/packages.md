# packages

---
title: "OpenWrt-Packages"
author: "hnhkj@163.com"
date: "2016年6月24日"
output: html_document
---

<https://wiki.openwrt.org/doc/devel/packages>
<https://wiki.openwrt.org/zh-tw/doc/devel/packages>
<https://wiki.openwrt.org/zh-cn/doc/devel/packages?s[]=package>

## Creating packages //创建包

One of the things that we've attempted to do with OpenWrt's template system is make it incredibly easy to port software to OpenWrt. If you look at a typical package directory in OpenWrt you'll find three things:

一个事情，那时我们尝试用OpenWrt的模版系统创建一个非常简单的端口软件。如你看到的，在一个OpenWrt的典型包目录中有3个事情。

* package/Makefile
* package/patches
* package/files

The patches directory is optional and typically contains bug fixes or optimizations to reduce the size of the executable. The files directory is optional. It typically includes default config or init files.

`patches`目录是可选的，包含一些bug修正或是减少代码尺寸的方法。`file`目录是可选的。它包含了默认的配置和初始化文件。

The package makefile is the important item because it provides the steps actually needed to download and compile the package.

包的`makefile`是重要的项目，因为它提供下载和编译包的步骤。

Looking at one of the package makefiles, you'd hardly recognize it as a makefile. Through what can only be described as blatant disregard and abuse of the traditional make format, the makefile has been transformed into an object oriented template which simplifies the entire ordeal.

查看一个包的`makefile`,你会觉得它不像一个`makefile`。

Here for example, is package/bridge/Makefile:

这是一个例子，是`package/bridge/Makefile`

```
include $(TOPDIR)/rules.mk  

PKG_NAME:=bridge-utils
PKG_VERSION:=1.0.6
PKG_RELEASE:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/bridge-utils-$(PKG_VERSION)
PKG_SOURCE:=bridge-utils-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=@SF/bridge
PKG_MD5SUM:=9b7dc52656f5cbec846a7ba3299f73bd
PKG_CAT:=zcat

include $(INCLUDE_DIR)/package.mk

define Package/bridge
  SECTION:=base
  CATEGORY:=Network
  TITLE:=Ethernet bridging configuration utility
  #DESCRIPTION:=This variable is obsolete. use the Package/name/description define instead!
  URL:=http://bridge.sourceforge.net/
endef

define Package/bridge/description
 Ethernet bridging configuration utility
 Manage ethernet bridging; a way to connect networks together to
 form a larger network.
endef

define Build/Configure
  $(call Build/Configure/Default,--with-linux-headers=$(LINUX_DIR))
endef

define Package/bridge/install
        $(INSTALL_DIR) $(1)/usr/sbin
        $(INSTALL_BIN) $(PKG_BUILD_DIR)/brctl/brctl $(1)/usr/sbin/
endef

$(eval $(call BuildPackage,bridge))
```

BuildPackage variables

As you can see, there's not much work to be done; everything is hidden in other makefiles and abstracted to the point where you only need to specify a few variables.

* `PKG_NAME` - The name of the package, as seen via menuconfig and ipkg//包的名字,可以在menuconfig和ipkg中看到
* `PKG_VERSION` - The upstream version number that we're downloading//我们下载的软件包的版本号
* `PKG_RELEASE` - The version of this package Makefile//当前Makefile的版本号
* `PKG_LICENSE` - The license(s) the package is available under, SPDX form.
* `PKG_LICENSE_FILE` - file containing the license text
* `PKG_BUILD_DIR` - Where to compile the package//编译软件包的目录
* `PKG_SOURCE` - The filename of the original sources//源代码文件名，一般由`PKG_NAME`和`PKG_VERSION`组成
* `PKG_SOURCE_URL` - Where to download the sources from (directory)//源代码下载地址
* `PKG_MD5SUM` - A checksum to validate the download//下载文件校验和
* `PKG_CAT` - How to decompress the sources (zcat, bzcat, unzip)//如何解码源代码
* `PKG_BUILD_DEPENDS` - Packages that need to be built before this package, but are not required at runtime. Uses the same syntax as DEPENDS below.
* `PKG_INSTALL` - Setting it to "1" will call the package's original "make install" with prefix set to PKG_INSTALL_DIR
* `PKG_INSTALL_DIR` - Where "make install" copies the compiled files
* `PKG_FIXUP` - See below
* `PKG_SOURCE_PROTO` - the protocol to use for fetching the sources (git, svn)
* `PKG_REV` - the svn revision to use, must be specified if proto is "svn"
* `PKG_SOURCE_SUBDIR` - must be specified if proto is "svn" or "git", e.g. "`PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)`"
* `PKG_SOURCE_VERSION` - must be specified if proto is "git", the commit hash to check out
* `PKG_CONFIG_DEPENDS` - specifies which config options depend on this package being selected

The PKG_* variables define where to download the package from; @SF is a special keyword for downloading packages from sourceforge. The md5sum is used to verify the package was downloaded correctly and PKG_BUILD_DIR defines where to find the package after the sources are uncompressed into $(BUILD_DIR). PKG_INSTALL_DIR defines where the files will be copied after calling "make install" (set with the PKG_INSTALL variable), and after that you can package them in the install section.

At the bottom of the file is where the real magic happens, "BuildPackage" is a macro setup by the earlier include statements. BuildPackage only takes one argument directly – the name of the package to be built, in this case "bridge". All other information is taken from the define blocks. This is a way of providing a level of verbosity, it's inherently clear what the DESCRIPTION variable in Package/bridge is, which wouldn't be the case if we passed this information directly as the Nth argument to BuildPackage.

## PKG_FIXUP //包修理

Some/many packages that think autotools is a good idea end up needing "fixes" to work around autotools "accidentally" knowing better and using host tools instead of the build environment tools. OpenWrt defines some PKG_FIXUP rules to help work around this.

```
PKG_FIXUP:=autoreconf
PKG_FIXUP:=patch-libtool
PKG_FIXUP:=gettext-version
```

Any variations of this you see in the wild are simply aliases for these.

#### autoreconf

This fixup performs

* autoreconf -f -i
* touch required but maybe missing files
* ensures that openwrt-libtool is linked
* suppresses autopoint/gettext

#### patch-libtool

If the shipped automake recipes are broken beyound repair then simply find instances of libtool, detect their version and apply openwrt fix patches to it

#### gettext-version

This fixup suppress version mismatch errors in automake's gettext support

#### Tips

Packages that are using Autotools should work with simply "PKG_FIXUP:=autoreconf". However there might be issues with required versions.

:!: Instead of patching ./configure one should fix the file from which ./configure is generated in autotools: configure.ac (or configure.in, for very old packages). Another important file is Makefile.am from which Makefiles (with configure output) are generated.

## Package Sourcecode

OpenWrt Buildroot supports many different ways to download external source code.

#### Use packed source code archive

Most packages use a packed .tar.gz, .tar.bz2, .tar.xz or similar source code file.

#### Use repository

PKG_SOURCE_PROTO supports download from various repositories to integrate development versions.

```
PKG_SOURCE_PROTO:=bzr
PKG_SOURCE_PROTO:=cvs
PKG_SOURCE_PROTO:=darcs
PKG_SOURCE_PROTO:=git
PKG_SOURCE_PROTO:=hg
PKG_SOURCE_PROTO:=svn
```

#### Bundle source code with OpenWrt Makefile

It is also possible to have the source code in the package/<packagename> directory. Often a ./src/ subdirectory is used.

```
Examples: px5g , px5g-standalone
```

#### Download override

Bundled source code does not need overriding.

You can download additional data from external sources.

```
USB_IDS_VERSION:=2013-01-16
USB_IDS_MD5SUM:=2a2344907b6344f0935c86efaf9de620
USB_IDS_FILE:=usb.ids.$(USB_IDS_VERSION).gz

[...parts missing...]

define Download/usb_ids
  FILE:=$(USB_IDS_FILE)
  URL:=http://mirror2.openwrt.org/sources
  MD5SUM:=$(USB_IDS_MD5SUM)
endef
$(eval $(call Download,usb_ids))
```

and unpack it or integrate it into the build process

```
define Build/Prepare
        $(Build/Prepare/Default)
        echo '#!/bin/sh' > $(PKG_BUILD_DIR)/update-usbids.sh.in
        echo 'cp $(DL_DIR)/$(USB_IDS_FILE) usb.ids.gz' >> $(PKG_BUILD_DIR)/update-usbids.sh.in
endef
```

You can modify UNPACK_CMD or call/modify PKG_UNPACK manually in your Build/Prepare section.

```
UNPACK_CMD=ar -p "$(DL_DIR)/$(PKG_SOURCE)" data.tar.xz | xzcat | tar -C $(1) -xf -
```

```
define Build/Prepare
        $(PKG_UNPACK)
#       we have to download additional stuff before patching
        (cd $(PKG_BUILD_DIR) && ./contrib/download_prerequisites)
        $(Build/Patch)
endef
```

```
Examples: px5g, px5g-standalone, usbutils, debootstrap, gcc, 
```

## BuildPackage defines //编译包定义

#### Package/ //包

matches the argument passed to buildroot, this describes the package the menuconfig and ipkg entries. Within Package/ you can define the following variables:

* SECTION - The type of package (currently unused)//软件包类型（当前不使用）
* CATEGORY - Which menu it appears in menuconfig//显示在menuconfig中的菜单项
* TITLE - A short description of the package//软件包标题
* DESCRIPTION - (deprecated) A long description of the package//软件包详细说明
* URL - Where to find the original software//软件原始位置，一般是软件作者主页
* MAINTAINER - (required for new packages) Who to contact concerning the package // 维护人员-（新的包需要）谁维护这个包
* DEPENDS - (optional) Which packages must be built/installed before this package. See below for the syntax.//软件包依赖项，运行软件依赖的其它软件包
* PKGARCH - (optional) Set this to "all" to produce a package with "Architecture: all"//
* USERID - (optional) a username:groupname pair to create at package installation time.

#### Package/conffiles (optional)

A list of config files installed by this package, one file per line.

#### Package/description

A free text description of the package

#### `Build/Prepare` (optional)  //预备（可选）

A set of commands to unpack and patch the sources. You may safely leave this undefined.

解压和打补丁源码的一组命令，你可以安全的离开这个定义。

#### Build/Configure (optional)

You can leave this undefined if the source doesn't use configure or has a normal config script, otherwise you can put your own commands here or use "$(call Build/Configure/Default,)" as above to pass in additional arguments for a standard configure script.

#### Build/Compile (optional) //编译（可选）

How to compile the source; in most cases you should leave this undefined, because then the default is used, which calls make. If you want to pass special arguments to make, use e.g. "$(call Build/Compile/Default,FOO=bar)

如何编译源码，多数情况先你可以不定义这个选项 ，因为默认call make是使用得。 如果你想制定参数make，使用例如："$(call Build/Compile/Default, FOO=bar"

#### Build/Install (optional) // 安装（可选）

How to install the compiled source. The default is to call make install. Again, to pass special arguments or targets, use $(call Build/Install/Default,install install-foo) Note that you need put all the needed make arguments here. If you just need to add something to the "install" argument, don't forget the 'install' itself.

如何安装编译的源码。默认调用 call make install。

Build/InstallDev (optional)

For things needed to compile packages against it (static libs, header files), but that are of no use on the target device.

Package/install

A set of commands to copy files into the ipkg which is represented by the $(1) directory. As source you can use relative paths which will install from the unpacked and compiled source, or $(PKG_INSTALL_DIR) which is where the files in the Build/Install step above end up.

Package/preinst

The actual text of the script which is to be executed before installation. Dont forget to include the #!/bin/sh. If you need to abort installation have the script return false.

Package/postinst

The actual text of the script which is to be executed after installation. Dont forget to include the #!/bin/sh.

Package/prerm

The actual text of the script which is to be executed before removal. Dont forget to include the #!/bin/sh. If you need to abort removal have the script return false.

Package/postrm

The actual text of the script which is to be executed after removal. Dont forget to include the #!/bin/sh.

The reason that some of the defines are prefixed by "Package/" and others are simply "Build" is because of the possibility of generating multiple packages from a single source. OpenWrt works under the assumption of one source per package makefile, but you can split that source into as many packages as desired. Since you only need to compile the sources once, there's one global set of "Build" defines, but you can add as many "Package/" defines as you want by adding extra calls to BuildPackage – see the dropbear package for an example.