# add your device to OpenWrt

---
title: "Openwrt-增加你的板子到Openwrt"
author: "huang,hnhkj@163.com"
date: "2017年2月6日"
output: html_document
---

## 增加你的板子到Openwrt


```
project\customfeed\mtk-sdk-wifi\files\etc\uci-defaults\51_linkit_config
target\linux\ramips\base-files\etc\board.d\02_network
target\linux\ramips\base-files\lib\ramips.sh
target\linux\ramips\base-files\lib\upgrade\platform.sh
target\linux\ramips\dts\WIDORA-DTS.dts
target\linux\ramips\image\Makefile
target\linux\ramips\mt7688\profiles\widora.mk
```

1. 增加 `dts` 文件，修改`model`内容为指定内容，例如`model = "ethMOH-IR077H-7688"`;该内容会在`ramips.sh`文件中调用。
2. 增加`profiles`文件
3. 修改`image/Makefile`，增减`profiles`文件支持
4. 修改`ramips.sh`，增加对`dts\model`支持
5. 修改`platform.sh`，增加对`dts\model`支持
6. 修改`02_network`，增加对网络的支持

以7688为例：

`~/openwrt/ethMOH/openwrt_ethMOH/target/linux/ramips$ git show 8829dee`

* package/boot/uboot-envtools/files/ramips\

```
   +linkits7688 | \
```
* target/linux/ramips/base-files/etc/board.d/02_network

```
+       linkits7688 | \
+       linkits7688d | \
......
+       linkits7688 | \
+       linkits7688d)
+               wan_mac=$(mtd_get_mac_binary factory 4)
+               lan_mac=$(mtd_get_mac_binary factory 46)
+               ;;
```

* target/linux/ramips/base-files/lib/ramips.sh

```
+       *"MediaTek LinkIt Smart7688")
+               linkit="$(dd bs=1 skip=1024 count=12 if=/dev/mtd2 2> /dev/null)"
+               if [ "${linkit}" = "LINKITS7688D" ]; then
+                       name="linkits7688d"
+                       RAMIPS_MODEL="${machine} DUO"
+               else
+                       name="linkits7688"
+               fi
+               ;;
```

* target/linux/ramips/base-file/lib/upgrade/platform.sh

```
+       linkits7688 | \
+       linkits7688d | \
```

* target/linux/ramips/dts/LINKIT7688.dts
* target/linux/ramips/image/Makefile

```
+ralink_default_fw_size_32M=33226752
+BuildFirmware/Default32M/squashfs=$(call BuildFirmware/OF,$(1),$(2),$(3),$(ralink_default_fw_size_32M),$(4))
+BuildFirmware/Default32M/initramfs=$(call BuildFirmware/OF/initramfs,$(1),$(2),$(3),$(4))
+

......

+
+#
+# MT7688 Profiles
+#
+
+Image/Build/Profile/MT7688=$(call BuildFirmware/Default4M/$(1),$(1),mt7628,MT7628)
+Image/Build/Profile/LinkIt7688=$(call BuildFirmware/Default32M/$(1),$(1),LinkIt7688,LINKIT7688)
+
+ifeq ($(SUBTARGET),mt7628)
+define Image/Build/Profile/Default
+       $(call Image/Build/Profile/MT7688,$(1))
+       $(call Image/Build/Profile/LinkIt7688,$(1))
+endef
+endif
+

```

* target/linux/ramips/mt7688/profiles/01-mediatek.mk

```

```

* target/linux/ramips/patches-3.18/0200-linkit_bootstrap.patch



openwrt/target/linux/ramips/

1. 创建`profiles/02-ipRec-DM06.mk`文件

`openwrt\target/linux/ramips/mt7688/profiles/02-iprec-dm06.mk`

最简单的方法是直接复制01-mediatek.mk文件到02-iprec-dm06.mk,然后修改相关设置

```
#
# Copyright (C) 2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

define Profile/ipRec-DM06
        NAME:=ipRec-DM06
        PACKAGES:=\
                kmod-usb-core kmod-usb2 kmod-usb-ohci \
                uboot-envtools kmod-ledtrig-netdev
endef

define Profile/ipRec-DM06/Description
        Default package set compatible with most boards.
endef
$(eval $(call Profile,ipRec-DM06))
~
```

该文件中包含了Profile的项目名称，和依赖的工具。

2. 创建`dts/IPREC-DM06.dts`文件

`openwrt\target/linux/ramips/mt7688/dts/IPREC-DM06.dts`

也采用和prfiles类似的方式，复制现有的dts文件，然后修改参数。

```
$ cp LINKIT7688.dts IPREC-DM06.dts
```

3. 修改image/Makefile文件

`openwrt_1505/target/linux/ramips/image/Makefile`

```
#
# MT7688 Profiles
#

Image/Build/Profile/MT7688=$(call BuildFirmware/Default4M/$(1),$(1),mt7628,MT7628)
Image/Build/Profile/LinkIt7688=$(call BuildFirmware/Default32M/$(1),$(1),LinkIt7688,LINKIT7688)
Image/Build/Profile/ipRec-DM06=$(call BuildFireware/Default16M/$(1),$(1),ipRec-DM06,IPREC-DM06)
ifeq ($(SUBTARGET),mt7628)
define Image/Build/Profile/Default
        $(call Image/Build/Profile/MT7688,$(1))
        $(call Image/Build/Profile/LinkIt7688,$(1))
        $(call Image/Build/Profile/ipRec-DM06,$(1))
endef
endif

```

4. 修改相关的配置文件

* base-files/lib/ramips.sh
* base-files/etc/board.d/01_leds
* base-files/etc/board.d/02_network

5. make menuconfig

```
$ cd openwrt
$ rm -rf tmp
$ make menuconfig
```

欢迎修正