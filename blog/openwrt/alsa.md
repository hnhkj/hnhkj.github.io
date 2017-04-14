---
title: "Openwrt - Alsa 声卡应用"
author: "hnhkj@163.com"
date: "2016年7月1日"
output: html_document
---

## ALSA声卡


#### ALSA API 可以分解为一下几个主要接口

* /dev/snd/congrolCX: - 控制接口，提供管理声卡注册和请求可用设备的通用功能
* /dev/snd/pcmCXDX: - PCM接口，管理数字音频回访和录音的接口
* /dev/snd/midiCXDX: - Raw MIDI接口，支持MIDI标准的电子乐器
* /dev/snd/timer: - 定时器，为同步音频事件提供对声卡上时间处理硬件的访问
* /dev/snd/seq: - 时序器接口
* /dev/snd/mixerCXDX: 混音器（mixer）接口

#### 检查当前声卡状态

当前OpenWRT系统

```
root@ethMOH:/# ls -l /dev
crw-r--r--    1 root     root       14,   4 Jun 28 04:48 audio
crw-r--r--    1 root     root       14,  20 Jun 28 04:48 audio1
...
crw-r--r--    1 root     root       14,   0 Jun 28 04:48 mixer
crw-r--r--    1 root     root       14,  16 Jun 28 04:48 mixer1
...

root@ethMOH:/# ls -l /dev/snd/
crw-r--r--    1 root     root      116,   0 Jun 28 04:48 controlC0
crw-r--r--    1 root     root      116,  32 Jun 28 04:48 controlC1
crw-r--r--    1 root     root      116,  24 Jun 28 04:48 pcmC0D0c
crw-r--r--    1 root     root      116,  16 Jun 28 04:48 pcmC0D0p
crw-r--r--    1 root     root      116,  56 Jun 28 04:48 pcmC1D0c
crw-r--r--    1 root     root      116,  48 Jun 28 04:48 pcmC1D0p
crw-r--r--    1 root     root      116,  33 Jun 28 04:48 timer
```


当前Ubuntu系统

```
ubuntu@ubuntu-System-Name:~$ ll /dev/snd/
总用量 0
drwxr-xr-x   4 root root      340  7月  1 09:27 ./
drwxr-xr-x  15 root root     4200  7月  1 09:27 ../
drwxr-xr-x   2 root root       60  7月  1 09:27 by-id/
drwxr-xr-x   2 root root       80  7月  1 09:27 by-path/
crw-rw---T+  1 root audio 116, 12  7月  1 09:27 controlC0
crw-rw---T+  1 root audio 116, 11  7月  1 09:27 controlC1
crw-rw---T+  1 root audio 116,  8  7月  1 09:27 hwC0D0
crw-rw---T+  1 root audio 116,  2  7月  1 09:27 midiC0D0
crw-rw---T+  1 root audio 116,  7  7月  1 09:28 pcmC0D0c
crw-rw---T+  1 root audio 116,  6  7月  1 09:28 pcmC0D0p
crw-rw---T+  1 root audio 116,  5  7月  1 09:28 pcmC0D1p
crw-rw---T+  1 root audio 116,  4  7月  1 09:28 pcmC0D2c
crw-rw---T+  1 root audio 116,  3  7月  1 09:28 pcmC0D2p
crw-rw---T+  1 root audio 116, 10  7月  1 09:28 pcmC1D0c
crw-rw---T+  1 root audio 116,  9  7月  1 09:28 pcmC1D0p
crw-rw---T+  1 root audio 116,  1  7月  1 09:27 seq
crw-rw---T+  1 root audio 116, 33  7月  1 09:27 timer

```

在当前系统中包含了2个声卡，声卡0和声卡1。

*C0D0代表的是声卡0中的设备0，pcmC0D0c最后一个c代表capture，pcmC0D0p最后一个p代表playback*


#### hw:i,j

硬件名字使用`hw:i,j`这样的格式：其中i是卡号，j是这块声卡上的设备号。

```
root@ethMOH:/# aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: I2S [MTK APSoC I2S], device 0: WMserious PCM wm8960-hifi-0 []
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: Device [USB Audio Device], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

#### plughhw:

插件系统使用另外的唯一名字，例如, plughw：,这个插件不提供对硬件设备的访问，而是提供像采用率转换这样的软件特性，硬件本身并不支持这样的特性。


## OpenWRT MT7688 声卡驱动

* i2c_wm8960.c/h
    * audiohw_set_frequency
    * audiohw_close
    * audiohw_postinit
    * audiohw_preinit
    * audiohw_set_apll
    * audiohw_codec_exlbk
    * audiohw_bypass
    * audiohw_set_lineout_vol
    * audiohw_set_linein_vol
    * audiohw_micin
    * audiohw_mute
    * audiohw_loopback
    * audiohw_micboost


void audiohw_preinit(void);
int audiohw_postinit(int bSlave, int AIn, int AOut, int pll_en, int wordLen24b);
void audiohw_close(void);
void audiohw_set_frequency(int fsel, int pll_en);
void audiohw_mute(bool mute);
void audiohw_micboost(int boostgain);
void audiohw_micin(int enableMic);
void audiohw_set_apll(int srate);
int audiohw_set_lineout_vol(int Aout, int vol_l, int vol_r);
int audiohw_set_linein_vol(int vol_l, int vol_r);
void audiohw_mute( bool mute);
void audiohw_loopback(int fsel);
void audiohw_codec_exlbk(void);
void audiohw_bypass(void);


## 参考文档

* 《Linux高级程序设计》
* <http://blog.csdn.net/droidphone/article/details/6271122>