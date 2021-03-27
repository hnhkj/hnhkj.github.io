# mt76xx_driver

## Files

* /linux/sound/soc/codecs/wm8960.c
* /linux/sound/soc/codecs/wm8960.h
* /linux/sound/soc/mtk/i2c_wm8960.c
* /linux/sound/soc/mtk/i2c_wm8960.h
* /linux/sound/soc/mtk/i2s_ctrl.c
* /linux/sound/soc/mtk/i2s_ctrl.h
* /linux/sound/soc/mtk/i2s_debug.c
* /linux/sound/soc/mtk/Kconfig
* /linux/sound/soc/mtk/Makefile
* /linux/sound/soc/mtk/mt76xx_i2s.c
* /linux/sound/soc/mtk/mt76xx_i2s.h
* /linux/sound/soc/mtk/mt76xx_machine.c
* /linux/sound/soc/mtk/mt76xx_machine.h
* /linux/sound/soc/mtk/mt76xxx_pcm.c
* /linux/sound/soc/mtk/ralink_gdma.c
* /linux/sound/soc/mtk/ralink_gdma.h


## i2c_wm8960.c/h

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


```c
EXPORT_SYMBOL(audiohw_set_frequency);
EXPORT_SYMBOL(audiohw_close);
EXPORT_SYMBOL(audiohw_postinit);
EXPORT_SYMBOL(audiohw_preinit);
EXPORT_SYMBOL(audiohw_set_apll);
EXPORT_SYMBOL(audiohw_codec_exlbk);
EXPORT_SYMBOL(audiohw_bypass);
EXPORT_SYMBOL(audiohw_set_lineout_vol);
EXPORT_SYMBOL(audiohw_set_linein_vol);
EXPORT_SYMBOL(audiohw_micin);
EXPORT_SYMBOL(audiohw_mute);
EXPORT_SYMBOL(audiohw_loopback);
EXPORT_SYMBOL(audiohw_micboost);
```

## install local

* /lib/modules/3.18.44/*.ko

```
root@ess:/# ls -al /lib/modules/3.18.44/
drwxr-xr-x    2 root     root          2466 Mar 26 03:17 .
drwxr-xr-x    3 root     root            30 Mar 26 03:14 ..
-rw-r--r--    1 root     root         29392 Mar 25 09:59 autofs4.ko
-rw-r--r--    1 root     root         21652 Mar 25 09:59 cdc-acm.ko
-rw-r--r--    1 root     root          2180 Mar 25 09:59 crc-ccitt.ko
-rw-r--r--    1 root     root          2148 Mar 25 09:59 crc16.ko
-rw-r--r--    1 root     root        137016 Mar 26 03:14 dahdi.ko
-rw-r--r--    1 root     root          4868 Mar 26 03:14 dahdi_dummy.ko
-rw-r--r--    1 root     root          5080 Mar 25 09:59 echo.ko
-rw-r--r--    1 root     root         41836 Mar 25 09:59 ehci-hcd.ko
-rw-r--r--    1 root     root          7132 Mar 25 09:59 ehci-platform.ko
-rw-r--r--    1 root     root         14416 Mar 25 09:59 evdev.ko
-rw-r--r--    1 root     root         95452 Mar 26 03:14 exfat.ko
-rw-r--r--    1 root     root         67784 Mar 25 09:59 fat.ko
-rw-r--r--    1 root     root         11376 Mar 26 03:17 gpio-button-hotplug.ko
-rw-r--r--    1 root     root          1988 Mar 25 09:59 hid-generic.ko
-rw-r--r--    1 root     root        110624 Mar 25 09:59 hid.ko
-rw-r--r--    1 root     root          5084 Mar 25 09:59 hwmon.ko
-rw-r--r--    1 root     root          8360 Mar 25 09:59 i2c-dev.ko
-rw-r--r--    1 root     root          4708 Mar 25 09:59 i2c-ralink.ko
-rw-r--r--    1 root     root          7956 Mar 25 09:59 i2c_wm8960.ko
-rw-r--r--    1 root     root         15292 Mar 25 09:59 ip6_tables.ko
-rw-r--r--    1 root     root          3260 Mar 25 09:59 ip6t_REJECT.ko
-rw-r--r--    1 root     root          3092 Mar 25 09:59 ip6table_filter.ko
-rw-r--r--    1 root     root          3420 Mar 25 09:59 ip6table_mangle.ko
-rw-r--r--    1 root     root          2832 Mar 25 09:59 ip6table_raw.ko
-rw-r--r--    1 root     root         15620 Mar 25 09:59 ip_tables.ko
-rw-r--r--    1 root     root          2300 Mar 25 09:59 ipt_MASQUERADE.ko
-rw-r--r--    1 root     root          2788 Mar 25 09:59 ipt_REJECT.ko
-rw-r--r--    1 root     root          3160 Mar 25 09:59 iptable_filter.ko
-rw-r--r--    1 root     root          3264 Mar 25 09:59 iptable_mangle.ko
-rw-r--r--    1 root     root          3284 Mar 25 09:59 iptable_nat.ko
-rw-r--r--    1 root     root          2900 Mar 25 09:59 iptable_raw.ko
-rw-r--r--    1 root     root        358044 Mar 25 09:59 ipv6.ko
-rw-r--r--    1 root     root          5740 Mar 25 09:59 leds-gpio.ko
-rw-r--r--    1 root     root          6416 Mar 25 09:59 lm75.ko
-rw-r--r--    1 root     root          3260 Mar 25 09:59 lzo_compress.ko
-rw-r--r--    1 root     root          2512 Mar 25 09:59 lzo_decompress.ko
-rw-r--r--    1 root     root         33064 Mar 25 09:59 mmc_block.ko
-rw-r--r--    1 root     root        106568 Mar 25 09:59 mmc_core.ko
-rw-r--r--    1 root     root       1861128 Mar 23 11:04 mt_wifi.ko
-rw-r--r--    1 root     root         22580 Mar 25 09:59 mtk_sd.ko
-rw-r--r--    1 root     root         71900 Mar 25 09:59 nf_conntrack.ko
-rw-r--r--    1 root     root          9316 Mar 25 09:59 nf_conntrack_ftp.ko
-rw-r--r--    1 root     root         10336 Mar 25 09:59 nf_conntrack_ipv4.ko
-rw-r--r--    1 root     root         10740 Mar 25 09:59 nf_conntrack_ipv6.ko
-rw-r--r--    1 root     root          5480 Mar 25 09:59 nf_conntrack_rtcache.ko
-rw-r--r--    1 root     root          2468 Mar 25 09:59 nf_defrag_ipv4.ko
-rw-r--r--    1 root     root          9376 Mar 25 09:59 nf_defrag_ipv6.ko
-rw-r--r--    1 root     root          5320 Mar 25 09:59 nf_log_common.ko
-rw-r--r--    1 root     root          7316 Mar 25 09:59 nf_log_ipv4.ko
-rw-r--r--    1 root     root          7464 Mar 25 09:59 nf_log_ipv6.ko
-rw-r--r--    1 root     root         15668 Mar 25 09:59 nf_nat.ko
-rw-r--r--    1 root     root          3488 Mar 25 09:59 nf_nat_ftp.ko
-rw-r--r--    1 root     root          6728 Mar 25 09:59 nf_nat_ipv4.ko
-rw-r--r--    1 root     root          3296 Mar 25 09:59 nf_nat_masquerade_ipv4.ko
-rw-r--r--    1 root     root          3448 Mar 25 09:59 nf_reject_ipv4.ko
-rw-r--r--    1 root     root          3892 Mar 25 09:59 nf_reject_ipv6.ko
-rw-r--r--    1 root     root          7192 Mar 25 09:59 nls_base.ko
-rw-r--r--    1 root     root          5996 Mar 25 09:59 nls_cp437.ko
-rw-r--r--    1 root     root          4412 Mar 25 09:59 nls_iso8859-1.ko
-rw-r--r--    1 root     root          4940 Mar 25 09:59 nls_iso8859-15.ko
-rw-r--r--    1 root     root          4940 Mar 25 09:59 nls_iso8859-2.ko
-rw-r--r--    1 root     root          2116 Mar 25 09:59 nls_utf8.ko
-rw-r--r--    1 root     root         34296 Mar 25 09:59 option.ko
-rw-r--r--    1 root     root         11000 Mar 25 09:59 ppp_async.ko
-rw-r--r--    1 root     root         33644 Mar 25 09:59 ppp_generic.ko
-rw-r--r--    1 root     root         14828 Mar 25 09:59 pppoe.ko
-rw-r--r--    1 root     root          3800 Mar 25 09:59 pppox.ko
-rw-r--r--    1 root     root          5636 Mar 25 09:59 ralink_gdma.ko
-rw-r--r--    1 root     root         45576 Mar 25 09:59 regmap-core.ko
-rw-r--r--    1 root     root          3324 Mar 25 09:59 regmap-i2c.ko
-rw-r--r--    1 root     root          2680 Mar 25 09:59 regmap-spi.ko
-rw-r--r--    1 root     root        136500 Mar 25 09:59 scsi_mod.ko
-rw-r--r--    1 root     root         43064 Mar 25 09:59 sd_mod.ko
-rw-r--r--    1 root     root          6632 Mar 25 09:59 sdhci-pltfm.ko
-rw-r--r--    1 root     root         26444 Mar 25 09:59 sdhci.ko
-rw-r--r--    1 root     root          6640 Mar 25 09:59 slhc.ko
-rw-r--r--    1 root     root         10684 Mar 25 09:59 snd-compress.ko
-rw-r--r--    1 root     root          9712 Mar 25 09:59 snd-hwdep.ko
-rw-r--r--    1 root     root         20012 Mar 25 09:59 snd-mixer-oss.ko
-rw-r--r--    1 root     root         46420 Mar 25 09:59 snd-pcm-oss.ko
-rw-r--r--    1 root     root         88668 Mar 25 09:59 snd-pcm.ko
-rw-r--r--    1 root     root         25108 Mar 25 09:59 snd-rawmidi.ko
-rw-r--r--    1 root     root          8784 Mar 25 09:59 snd-seq-device.ko
-rw-r--r--    1 root     root        142120 Mar 25 09:59 snd-soc-core.ko
-rw-r--r--    1 root     root         35012 Mar 25 09:59 snd-soc-mt76xx-i2s-ctl.ko
-rw-r--r--    1 root     root          3836 Mar 25 09:59 snd-soc-mt76xx-i2s.ko
-rw-r--r--    1 root     root          4584 Mar 25 09:59 snd-soc-mt76xx-machine.ko
-rw-r--r--    1 root     root          6084 Mar 25 09:59 snd-soc-mt76xx-pcm.ko
-rw-r--r--    1 root     root         24556 Mar 25 09:59 snd-soc-wm8960.ko
-rw-r--r--    1 root     root         24728 Mar 25 09:59 snd-timer.ko
-rw-r--r--    1 root     root        129064 Mar 25 09:59 snd-usb-audio.ko
-rw-r--r--    1 root     root         22888 Mar 25 09:59 snd-usbmidi-lib.ko
-rw-r--r--    1 root     root         60444 Mar 25 09:59 snd.ko
-rw-r--r--    1 root     root          8812 Mar 25 09:59 soundcore.ko
-rw-r--r--    1 root     root          3848 Mar 25 09:59 usb-common.ko
-rw-r--r--    1 root     root         55324 Mar 25 09:59 usb-storage.ko
-rw-r--r--    1 root     root          7960 Mar 25 09:59 usb_wwan.ko
-rw-r--r--    1 root     root        172864 Mar 25 09:59 usbcore.ko
-rw-r--r--    1 root     root         30964 Mar 25 09:59 usbserial.ko
-rw-r--r--    1 root     root         12448 Mar 25 09:59 vfat.ko
-rw-r--r--    1 root     root         20672 Mar 25 09:59 x_tables.ko
-rw-r--r--    1 root     root          4904 Mar 25 09:59 xt_CT.ko
-rw-r--r--    1 root     root          2416 Mar 25 09:59 xt_LOG.ko
-rw-r--r--    1 root     root          2636 Mar 25 09:59 xt_REDIRECT.ko
-rw-r--r--    1 root     root          5020 Mar 25 09:59 xt_TCPMSS.ko
-rw-r--r--    1 root     root          1772 Mar 25 09:59 xt_comment.ko
-rw-r--r--    1 root     root          3964 Mar 25 09:59 xt_conntrack.ko
-rw-r--r--    1 root     root          1772 Mar 25 09:59 xt_id.ko
-rw-r--r--    1 root     root          2788 Mar 25 09:59 xt_limit.ko
-rw-r--r--    1 root     root          1916 Mar 25 09:59 xt_mac.ko
-rw-r--r--    1 root     root          2180 Mar 25 09:59 xt_mark.ko
-rw-r--r--    1 root     root          2656 Mar 25 09:59 xt_multiport.ko
-rw-r--r--    1 root     root          2720 Mar 25 09:59 xt_nat.ko
-rw-r--r--    1 root     root          2312 Mar 25 09:59 xt_state.ko
-rw-r--r--    1 root     root          3336 Mar 25 09:59 xt_tcpudp.ko
-rw-r--r--    1 root     root          3672 Mar 25 09:59 xt_time.ko

```

* lsmod

```
root@ess:/# lsmod
autofs4                18603  1
cdc_acm                13840  0
crc16                    999  0
crc_ccitt               1003  2 ppp_async
dahdi                 194149  1 dahdi_dummy
dahdi_dummy             2076  0
echo                    3250  0
ehci_hcd               31420  1 ehci_platform
ehci_platform           3728  0
evdev                   8464  0
exfat                  91323  0
fat                    46879  1 vfat
gpio_button_hotplug     5984  0
hid                    80114  1 hid_generic
hid_generic              576  0
hwmon                   1972  1 lm75
i2c_dev                 3888  0
i2c_ralink              2192  0
i2c_wm8960              4150  1 snd_soc_mt76xx_i2s_ctl
ip6_tables              9185  3 ip6table_raw
ip6t_REJECT             1184  2
ip6table_filter          608  1
ip6table_mangle         1040  1
ip6table_raw             576  1
ip_tables               9405  4 iptable_nat
ipt_MASQUERADE           624  1
ipt_REJECT               912  2
iptable_filter           672  1
iptable_mangle           928  1
iptable_nat              752  1
iptable_raw              640  1
ipv6                  257432 28 nf_conntrack_ipv6
leds_gpio               2880  0
lm75                    3344  0
lzo_compress            2066  1 regmap_core
lzo_decompress          1399  1 regmap_core
mmc_block              21553  0
mmc_core               73702  3 mmc_block
mt_wifi              1336123  2
mtk_sd                 154242147483647
nf_conntrack           47739 11 nf_nat_ipv4
nf_conntrack_ftp        5264  1 nf_nat_ftp
nf_conntrack_ipv4       4624  8
nf_conntrack_ipv6       4944  3
nf_conntrack_rtcache    2448  0
nf_defrag_ipv4           790  1 nf_conntrack_ipv4
nf_defrag_ipv6          9047  1 nf_conntrack_ipv6
nf_log_common           2335  2 nf_log_ipv4
nf_log_ipv4             3232  0
nf_log_ipv6             3360  0
nf_nat                  8923  5 nf_nat_ipv4
nf_nat_ftp              1200  0
nf_nat_ipv4             3697  1 iptable_nat
nf_nat_masquerade_ipv4    1372  1 ipt_MASQUERADE
nf_reject_ipv4          1779  1 ipt_REJECT
nf_reject_ipv6          2039  1 ip6t_REJECT
nls_base                4992  9 vfat
nls_cp437               4416  0
nls_iso8859_1           2880  0
nls_iso8859_15          3392  0
nls_iso8859_2           3392  0
nls_utf8                 848  0
option                 30768  0
ppp_async               6352  0
ppp_generic            20594  3 pppoe
pppoe                   8144  0
pppox                   1338  1 pppoe
ralink_gdma             3381  1 snd_soc_mt76xx_i2s_ctl
regmap_core            31326  4 snd_soc_wm8960
regmap_i2c              1654  1 snd_soc_wm8960
regmap_spi              1078  0
scsi_mod               85639  2 usb_storage
sd_mod                 26208  0
sdhci                  18349  1 sdhci_pltfm
sdhci_pltfm             2754  0
slhc                    4331  1 ppp_generic
snd                    39253 12 snd_usb_audio
snd_compress            6287  1 snd_soc_core
snd_hwdep               4558  1 snd_usb_audio
snd_mixer_oss          12585  1 snd_pcm_oss
snd_pcm                60503  6 snd_usb_audio
snd_pcm_oss            33953  0
snd_rawmidi            15498  1 snd_usbmidi_lib
snd_seq_device          4349  1 snd_rawmidi
snd_soc_core           96710  4 snd_soc_mt76xx_machine
snd_soc_mt76xx_i2s      1472  1
snd_soc_mt76xx_i2s_ctl   40336  3 snd_soc_mt76xx_machine
snd_soc_mt76xx_machine    1776  0
snd_soc_mt76xx_pcm      2800  1
snd_soc_wm8960         16720  1
snd_timer              15102  1 snd_pcm
snd_usb_audio          94582  0
snd_usbmidi_lib        15313  1 snd_usb_audio
soundcore               3740  1 snd
usb_common              1724  1 usbcore
usb_storage            37295  0
usb_wwan                4391  1 option
usbcore               117844  9 option
usbserial              17755  2 option
vfat                    7824  0
x_tables               11746 26 ipt_REJECT
xt_CT                   2208  0
xt_LOG                   752  0
xt_REDIRECT             1040  0
xt_TCPMSS               2640  2
xt_comment               480 62
xt_conntrack            2144 10
xt_id                    480  0
xt_limit                 992 20
xt_mac                   624  0
xt_mark                  656  0
xt_multiport            1184  0
xt_nat                  1056  0
xt_state                 688  0
xt_tcpudp               1712 10
xt_time                 1648  0
```

