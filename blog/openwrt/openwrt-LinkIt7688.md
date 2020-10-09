# openwrt-LinkIt7688 config


## how to modify LinkIt7688 config

run `make menuconfig`, select menu follows. and different config select different files.

```
Target System (Ralink RT288x/RT3xxx) ---> chaos_calmer/target/linux/ramips/image/Makefile
Subtarget (MT7688 based boards) --->  chaos_calmer/target/linux/ramips/mt7688/target.mk
Target Profile (LinkIt7688) ---> chaos_calmer/target/linux/ramips/mt7688/profiles/01-mediatek.mk
```


## DTS file select

define in `chaos_calmer/target/linux/ramips/image/Makefile` file. see follow, LinkIt7688 select LINKIT7688.dts file

```
...
#
# MT7688 Profiles
#

Image/Build/Profile/MT7688=$(call BuildFirmware/Default4M/$(1),$(1),mt7628,MT7628)
Image/Build/Profile/LinkIt7688=$(call BuildFirmware/Default32M/$(1),$(1),LinkIt7688,LINKIT7688)

ifeq ($(SUBTARGET),mt7628)
define Image/Build/Profile/Default
	$(call Image/Build/Profile/MT7688,$(1))
	$(call Image/Build/Profile/LinkIt7688,$(1))
endef
endif
...
```
