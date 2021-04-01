---
title: "Openwrt - Alsa 声卡应用"
author: "hnhkj@163.com"
date: "2016-7-1"
output: html_document
---


## Alsa until

#### 

## ALSA声卡

* 录音

根据上面的信息，如果我们要用TDM_Capture 录音，可以输入如下命令

`arecord -Dhw:0,0 -d 10 -f cd -r 44100 -c 2 -t wav /tmp/test.wav`

* 参数解析
  * -D 指定了录音设备，0,1 是card 0 device 1的意思，也就是TDM_Capture
  * -d 指定录音的时长，单位时秒
  * -f 指定录音格式，通过上面的信息知道只支持 cd cdr dat 
  * -r 指定了采样率，单位时Hz
  * -c 指定channel 个数
  * -t 指定生成的文件格式



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

```c
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
```

#### how to get channen status

```
root@ess:~# amixer cget numid=54
numid=54,iface=MIXER,name='Left Boost Mixer LINPUT1 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
root@ess:~# amixer cget numid=52
numid=52,iface=MIXER,name='Left Boost Mixer LINPUT2 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
root@ess:~# amixer cget numid=53
numid=53,iface=MIXER,name='Left Boost Mixer LINPUT3 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
```

## Command

#### arecord

```
root@ess:/tmp# arecord --help
Usage: arecord [OPTION]... [FILE]...

-h, --help              help
    --version           print current version
-l, --list-devices      list all soundcards and digital audio devices
-L, --list-pcms         list device names
-D, --device=NAME       select PCM by name
-q, --quiet             quiet mode
-t, --file-type TYPE    file type (voc, wav, raw or au)
-c, --channels=#        channels
-f, --format=FORMAT     sample format (case insensitive)
-r, --rate=#            sample rate
-d, --duration=#        interrupt after # seconds
-M, --mmap              mmap stream
-N, --nonblock          nonblocking mode
-F, --period-time=#     distance between interrupts is # microseconds
-B, --buffer-time=#     buffer duration is # microseconds
    --period-size=#     distance between interrupts is # frames
    --buffer-size=#     buffer duration is # frames
-A, --avail-min=#       min available space for wakeup is # microseconds
-R, --start-delay=#     delay for automatic PCM start is # microseconds 
                        (relative to buffer size if <= 0)
-T, --stop-delay=#      delay for automatic PCM stop is # microseconds from xrun
-v, --verbose           show PCM structure and setup (accumulative)
-V, --vumeter=TYPE      enable VU meter (TYPE: mono or stereo)
-I, --separate-channels one file for each channel
-i, --interactive       allow interactive operation from stdin
-m, --chmap=ch1,ch2,..  Give the channel map to override or follow
    --disable-resample  disable automatic rate resample
    --disable-channels  disable automatic channel conversions
    --disable-format    disable automatic format conversions
    --disable-softvol   disable software volume control (softvol)
    --test-position     test ring buffer position
    --test-coef=#       test coefficient for ring buffer position (default 8)
                        expression for validation is: coef * (buffer_size / 2)
    --test-nowait       do not wait for ring buffer - eats whole CPU
    --max-file-time=#   start another output file when the old file has recorded
                        for this many seconds
    --process-id-file   write the process ID here
    --use-strftime      apply the strftime facility to the output file name
    --dump-hw-params    dump hw_params of the device
    --fatal-errors      treat all errors as fatal
Recognized sample formats are: S8 U8 S16_LE S16_BE U16_LE U16_BE S24_LE S24_BE U24_LE U24_BE S32_LE S32_BE U32_LE U32_BE FLOAT_LE FLOAT_BE FLOAT64_LE FLOAT64_BE IEC958_SUBFRAME_LE IEC958_SUBFRAME_BE MU_LAW A_LAW IMA_ADPCM MPEG GSM SPECIAL S24_3LE S24_3BE U24_3LE U24_3BE S20_3LE S20_3BE U20_3LE U20_3BE S18_3LE S18_3BE U18_3LE U18_3BE G723_24 G723_24_1B G723_40 G723_40_1B DSD_U8 DSD_U16_LE
Some of these may not be available on selected hardware
The available format shortcuts are:
-f cd (16 bit little endian, 44100, stereo)
-f cdr (16 bit big endian, 44100, stereo)
-f dat (16 bit little endian, 48000, stereo)
```

## FAQ

#### LINPUT Channel

* LINVOL[5:0] & RINVOL[5:0]
* MICB - R25(19h).bit1 - Microphone Bias Enable 0=OFF(high impedance output),1=ON
* MBSEL - R48(30h).bit0 - Micorphone Bias Voltage Control 0=0.9xAVDD, 1=0.65xAVDD

* AINL - R25(19h).bit5 - 0=disable,1=enabled
* LMIC - R47(2Fh).bit5

* LMN1
* LMP2
* LMP3
* LINMUTE

* LINVOL[5:0] -17.25dB to +30dB

* LIN2BOOST
* LIN3BOOST
* LMIC2B
* LINMUTE

* LOMIX(2FH-3bit) - Left Output Mixer
  * LD2LO(22H-8bit) - PCM Playback Switch
    * numid=44,iface=MIXER,name='Left Output Mixer PCM Playback Switch'
  * LI2LO(22H-7bit) - LINPUT3 Switch
    * numid=45,iface=MIXER,name='Left Output Mixer LINPUT3 Switch'
  * LB2LO(2DH-7bit) - Boost Bypass Switch
    * numid=46,iface=MIXER,name='Left Output Mixer Boost Bypass Switch'


numid=7,iface=MIXER,name='Left Input Boost Mixer LINPUT2 Volume'
numid=6,iface=MIXER,name='Left Input Boost Mixer LINPUT3 Volume'
numid=48,iface=MIXER,name='Left Input Mixer Boost Switch'
numid=46,iface=MIXER,name='Left Output Mixer Boost Bypass Switch'
numid=35,iface=MIXER,name='Left Output Mixer Boost Bypass Volume'
numid=45,iface=MIXER,name='Left Output Mixer LINPUT3 Switch'
numid=36,iface=MIXER,name='Left Output Mixer LINPUT3 Volume'
numid=44,iface=MIXER,name='Left Output Mixer PCM Playback Switch'


* ROMIX()
  * RD2RO
    * numid=41,iface=MIXER,name='Right Output Mixer PCM Playback Switch'
  * RI2RO
    * numid=42,iface=MIXER,name='Right Output Mixer RINPUT3 Switch'
  * RB2RO
    * numid=43,iface=MIXER,name='Right Output Mixer Boost Bypass Switch'

numid=43,iface=MIXER,name='Right Output Mixer Boost Bypass Switch'
numid=37,iface=MIXER,name='Right Output Mixer Boost Bypass Volume'
numid=41,iface=MIXER,name='Right Output Mixer PCM Playback Switch'
numid=42,iface=MIXER,name='Right Output Mixer RINPUT3 Switch'
numid=38,iface=MIXER,name='Right Output Mixer RINPUT3 Volume'

```
root@ess:~# amixer cget numid=48
numid=48,iface=MIXER,name='Left Input Mixer Boost Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on

```

#### How to select LINPUT1/LINPUT2/LINPUT3 (Left & Right input PGA)

* LMN1 - numid=54
* LMP3 - numid=53
* LMP2 - numid=52

```
root@ess:~# amixer cget numid=54
numid=54,iface=MIXER,name='Left Boost Mixer LINPUT1 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
root@ess:~# amixer cget numid=52
numid=52,iface=MIXER,name='Left Boost Mixer LINPUT2 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
root@ess:~# amixer cget numid=53
numid=53,iface=MIXER,name='Left Boost Mixer LINPUT3 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
```

* RMN1 - numid=51
* RMP3 - numid=49
* RMP2 - numid=50

```
root@ess:~# amixer cget numid=51
numid=51,iface=MIXER,name='Right Boost Mixer RINPUT1 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
root@ess:~# amixer cget numid=49
numid=49,iface=MIXER,name='Right Boost Mixer RINPUT2 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
root@ess:~# amixer cget numid=50
numid=50,iface=MIXER,name='Right Boost Mixer RINPUT3 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
```

#### how connect to Left & right Boost Mixer

* LMICBOOST - default 0db
* LMIC2B - numid=48

```
root@ess:~# amixer cget numid=48
numid=48,iface=MIXER,name='Left Input Mixer Boost Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
```

* RMICBOOST - default 0db
* RMIC2B - numid=47

```
root@ess:~# amixer cget numid=47
numid=47,iface=MIXER,name='Right Input Mixer Boost Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
```

#### how to adjust mic vol (LINVOL & RINVOL)

R0[00h].Bit[5:0] - LINVOL[5:0] - (00H-5:0bit) - Left Input PGA Volume Control
R0[01h].Bit[5:0] - RINVOL[5:0] - (01H-5:0bit) - Right Input PGA Volume Control

* LINVOL & RINVOL - numid=1

```sh
root@ess:~# amixer cget numid=1
numid=1,iface=MIXER,name='Capture Volume'
  ; type=INTEGER,access=rw---R--,values=2,min=0,max=63,step=0
  : values=43,43
  | dBscale-min=-97.00dB,step=0.50dB,mute=0

root@ess:~# amixer cset numid=1 63 63
numid=1,iface=MIXER,name='Capture Volume'
  ; type=INTEGER,access=rw---R--,values=2,min=0,max=63,step=0
  : values=40,40
  | dBscale-min=-97.00dB,step=0.50dB,mute=0
```

#### how to direct mic to speaker (Left & Right Output Mixer)

running

```
amixer cset numid=45 off &&
amixer cset numid=46 on &&
amixer cset numid=44 off &&
amixer cset numid=41 off &&
amixer cset numid=43 on &&
amixer cset numid=42 off 
```

* LI2LO/LINPUT3 - numid=45
* LB2LO/LeftMixer - numid=46
* LD2LO/L DAC - numid=44

```
root@ess:~# amixer cget numid=45
numid=45,iface=MIXER,name='Left Output Mixer LINPUT3 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
root@ess:~# amixer cget numid=46
numid=46,iface=MIXER,name='Left Output Mixer Boost Bypass Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
root@ess:~# amixer cget numid=44
numid=44,iface=MIXER,name='Left Output Mixer PCM Playback Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
```

* RD2RO/RINPUT - numid=41
* RB2RO/RightMixer - numid=43
* RI2RO/R DAC - numid=42

```
root@ess:~# amixer cget numid=41
numid=41,iface=MIXER,name='Right Output Mixer PCM Playback Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
root@ess:~# amixer cget numid=43
numid=43,iface=MIXER,name='Right Output Mixer Boost Bypass Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
root@ess:~# amixer cget numid=42
numid=42,iface=MIXER,name='Right Output Mixer RINPUT3 Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
```

Note: no sound when direct, default MIC or speaker MUTE. when running arecord will hear sound.

#### amixer

* amixer --help

```
root@ess:/# amixer --help
Usage: amixer <options> [command]

Available options:
  -h,--help       this help
  -c,--card N     select the card
  -D,--device N   select the device, default 'default'
  -d,--debug      debug mode
  -n,--nocheck    do not perform range checking
  -v,--version    print version of this program
  -q,--quiet      be quiet
  -i,--inactive   show also inactive controls
  -a,--abstract L select abstraction level (none or basic)
  -s,--stdin      Read and execute commands from stdin sequentially
  -R,--raw-volume Use the raw value (default)
  -M,--mapped-volume Use the mapped volume

Available commands:
  scontrols       show all mixer simple controls
  scontents   show contents of all mixer simple controls (default command)
  sset sID P      set contents for one mixer simple control
  sget sID        get contents for one mixer simple control
  controls        show all controls for given card
  contents        show contents of all controls for given card
  cset cID P      set control contents for one control
  cget cID        get control contents for one control
```


* amixer controls

```
root@ess:/# amixer controls
numid=10,iface=MIXER,name='Headphone Playback ZC Switch'
numid=9,iface=MIXER,name='Headphone Playback Volume'
numid=15,iface=MIXER,name='PCM Playback -6dB Switch'
numid=39,iface=MIXER,name='Mono Output Mixer Left Switch'
numid=40,iface=MIXER,name='Mono Output Mixer Right Switch'
numid=17,iface=MIXER,name='ADC High Pass Filter Switch'
numid=34,iface=MIXER,name='ADC PCM Capture Volume'
numid=16,iface=MIXER,name='ADC Polarity'
numid=2,iface=MIXER,name='Capture Volume ZC Switch'
numid=3,iface=MIXER,name='Capture Switch'
numid=1,iface=MIXER,name='Capture Volume'
numid=8,iface=MIXER,name='Playback Volume'
numid=21,iface=MIXER,name='3D Filter Lower Cut-Off'
numid=20,iface=MIXER,name='3D Filter Upper Cut-Off'
numid=23,iface=MIXER,name='3D Switch'
numid=22,iface=MIXER,name='3D Volume'
numid=31,iface=MIXER,name='ALC Attack'
numid=30,iface=MIXER,name='ALC Decay'
numid=24,iface=MIXER,name='ALC Function'
numid=28,iface=MIXER,name='ALC Hold Time'
numid=25,iface=MIXER,name='ALC Max Gain'
numid=27,iface=MIXER,name='ALC Min Gain'
numid=29,iface=MIXER,name='ALC Mode'
numid=26,iface=MIXER,name='ALC Target'
numid=19,iface=MIXER,name='DAC Deemphasis Switch'
numid=18,iface=MIXER,name='DAC Polarity'
numid=54,iface=MIXER,name='Left Boost Mixer LINPUT1 Switch'
numid=52,iface=MIXER,name='Left Boost Mixer LINPUT2 Switch'
numid=53,iface=MIXER,name='Left Boost Mixer LINPUT3 Switch'
numid=7,iface=MIXER,name='Left Input Boost Mixer LINPUT2 Volume'
numid=6,iface=MIXER,name='Left Input Boost Mixer LINPUT3 Volume'
numid=48,iface=MIXER,name='Left Input Mixer Boost Switch'
numid=46,iface=MIXER,name='Left Output Mixer Boost Bypass Switch'
numid=35,iface=MIXER,name='Left Output Mixer Boost Bypass Volume'
numid=45,iface=MIXER,name='Left Output Mixer LINPUT3 Switch'
numid=36,iface=MIXER,name='Left Output Mixer LINPUT3 Volume'
numid=44,iface=MIXER,name='Left Output Mixer PCM Playback Switch'
numid=33,iface=MIXER,name='Noise Gate Switch'
numid=32,iface=MIXER,name='Noise Gate Threshold'
numid=51,iface=MIXER,name='Right Boost Mixer RINPUT1 Switch'
numid=49,iface=MIXER,name='Right Boost Mixer RINPUT2 Switch'
numid=50,iface=MIXER,name='Right Boost Mixer RINPUT3 Switch'
numid=5,iface=MIXER,name='Right Input Boost Mixer RINPUT2 Volume'
numid=4,iface=MIXER,name='Right Input Boost Mixer RINPUT3 Volume'
numid=47,iface=MIXER,name='Right Input Mixer Boost Switch'
numid=43,iface=MIXER,name='Right Output Mixer Boost Bypass Switch'
numid=37,iface=MIXER,name='Right Output Mixer Boost Bypass Volume'
numid=41,iface=MIXER,name='Right Output Mixer PCM Playback Switch'
numid=42,iface=MIXER,name='Right Output Mixer RINPUT3 Switch'
numid=38,iface=MIXER,name='Right Output Mixer RINPUT3 Volume'
numid=14,iface=MIXER,name='Speaker AC Volume'
numid=13,iface=MIXER,name='Speaker DC Volume'
numid=11,iface=MIXER,name='Speaker Playback Volume'
numid=12,iface=MIXER,name='Speaker Playback ZC Switch'
```

```
root@ess:~# amixer
Simple mixer control 'Headphone',0
  Capabilities: pvolume
  Playback channels: Front Left - Front Right
  Limits: Playback 0 - 127
  Mono:
  Front Left: Playback 120 [94%] [-1.00dB]
  Front Right: Playback 120 [94%] [-1.00dB]
Simple mixer control 'Headphone Playback ZC',0
  Capabilities: pswitch
  Playback channels: Front Left - Front Right
  Mono:
  Front Left: Playback [on]
  Front Right: Playback [on]
Simple mixer control 'Speaker',0
  Capabilities: pvolume
  Playback channels: Front Left - Front Right
  Limits: Playback 0 - 127
  Mono:
  Front Left: Playback 127 [100%] [6.00dB]
  Front Right: Playback 127 [100%] [6.00dB]
Simple mixer control 'Speaker AC',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 5
  Mono: 5 [100%]
Simple mixer control 'Speaker DC',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 5
  Mono: 5 [100%]
Simple mixer control 'Speaker Playback ZC',0
  Capabilities: pswitch
  Playback channels: Front Left - Front Right
  Mono:
  Front Left: Playback [on]
  Front Right: Playback [on]
Simple mixer control 'PCM Playback -6dB',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Mono Output Mixer Left',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Mono Output Mixer Right',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Playback',0
  Capabilities: volume
  Playback channels: Front Left - Front Right
  Capture channels: Front Left - Front Right
  Limits: 0 - 255
  Front Left: 255 [100%] [0.50dB]
  Front Right: 255 [100%] [0.50dB]
Simple mixer control 'Capture',0
  Capabilities: cvolume cswitch
  Capture channels: Front Left - Front Right
  Limits: Capture 0 - 63
  Front Left: Capture 40 [63%] [-77.00dB] [off]
  Front Right: Capture 40 [63%] [-77.00dB] [off]
Simple mixer control '3D',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 0 [0%] Playback [off]
Simple mixer control '3D Filter Lower Cut-Off',0
  Capabilities: enum
  Items: 'Low' 'High'
  Item0: 'Low'
Simple mixer control '3D Filter Upper Cut-Off',0
  Capabilities: enum
  Items: 'High' 'Low'
  Item0: 'High'
Simple mixer control 'ADC High Pass Filter',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'ADC PCM',0
  Capabilities: cvolume
  Capture channels: Front Left - Front Right
  Limits: Capture 0 - 255
  Front Left: Capture 206 [81%] [6.00dB]
  Front Right: Capture 206 [81%] [6.00dB]
Simple mixer control 'ADC Polarity',0
  Capabilities: enum
  Items: 'No Inversion' 'Left Inverted' 'Right Inverted' 'Stereo Inversion'
  Item0: 'No Inversion'
Simple mixer control 'ALC Attack',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 2 [13%]
Simple mixer control 'ALC Decay',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 3 [20%]
Simple mixer control 'ALC Function',0
  Capabilities: enum
  Items: 'Off' 'Right' 'Left' 'Stereo'
  Item0: 'Off'
Simple mixer control 'ALC Hold Time',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 0 [0%]
Simple mixer control 'ALC Max Gain',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 7 [100%]
Simple mixer control 'ALC Min Gain',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%]
Simple mixer control 'ALC Mode',0
  Capabilities: enum
  Items: 'ALC' 'Limiter'
  Item0: 'ALC'
Simple mixer control 'ALC Target',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 15
  Mono: 4 [27%]
Simple mixer control 'DAC Deemphasis',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'DAC Polarity',0
  Capabilities: enum
  Items: 'No Inversion' 'Left Inverted' 'Right Inverted' 'Stereo Inversion'
  Item0: 'No Inversion'
Simple mixer control 'Left Boost Mixer LINPUT1',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Left Boost Mixer LINPUT2',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Left Boost Mixer LINPUT3',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Left Input Boost Mixer LINPUT2',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%] [-99999.99dB]
Simple mixer control 'Left Input Boost Mixer LINPUT3',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%] [-99999.99dB]
Simple mixer control 'Left Input Mixer Boost',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Left Output Mixer Boost Bypass',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 2 [29%] [-15.00dB] Playback [on]
Simple mixer control 'Left Output Mixer LINPUT3',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 7 [100%] [0.00dB] Playback [off]
Simple mixer control 'Left Output Mixer PCM',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Noise Gate',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Noise Gate Threshold',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 31
  Mono: 0 [0%]
Simple mixer control 'Right Boost Mixer RINPUT1',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Right Boost Mixer RINPUT2',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Right Boost Mixer RINPUT3',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Right Input Boost Mixer RINPUT2',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 0 [0%] [-99999.99dB]
Simple mixer control 'Right Input Boost Mixer RINPUT3',0
  Capabilities: volume volume-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 7 [100%] [9.00dB]
Simple mixer control 'Right Input Mixer Boost',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
Simple mixer control 'Right Output Mixer Boost Bypass',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 2 [29%] [-15.00dB] Playback [on]
Simple mixer control 'Right Output Mixer PCM',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Right Output Mixer RINPUT3',0
  Capabilities: volume volume-joined pswitch pswitch-joined
  Playback channels: Mono
  Capture channels: Mono
  Limits: 0 - 7
  Mono: 7 [100%] [0.00dB] Playback [off]
```

## 参考文档

* 《Linux高级程序设计》
* <http://blog.csdn.net/droidphone/article/details/6271122>
