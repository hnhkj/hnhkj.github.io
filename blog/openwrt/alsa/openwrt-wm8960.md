# wn8960


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


#### linux/sound/soc/codecs/wm8960.c


* `wm8960_snd_controls[]`

```c
static const struct snd_kcontrol_new wm8960_snd_controls[] = {
SOC_DOUBLE_R_TLV("Capture Volume", WM8960_LINVOL, WM8960_RINVOL,
		 0, 63, 0, adc_tlv),
SOC_DOUBLE_R("Capture Volume ZC Switch", WM8960_LINVOL, WM8960_RINVOL,
	6, 1, 0),
SOC_DOUBLE_R("Capture Switch", WM8960_LINVOL, WM8960_RINVOL,
	7, 1, 0),

SOC_SINGLE_TLV("Right Input Boost Mixer RINPUT3 Volume",
	       WM8960_INBMIX1, 4, 7, 0, boost_tlv),
SOC_SINGLE_TLV("Right Input Boost Mixer RINPUT2 Volume",
	       WM8960_INBMIX1, 1, 7, 0, boost_tlv),
SOC_SINGLE_TLV("Left Input Boost Mixer LINPUT3 Volume",
	       WM8960_INBMIX2, 4, 7, 0, boost_tlv),
SOC_SINGLE_TLV("Left Input Boost Mixer LINPUT2 Volume",
	       WM8960_INBMIX2, 1, 7, 0, boost_tlv),

SOC_DOUBLE_R_TLV("Playback Volume", WM8960_LDAC, WM8960_RDAC,
		 0, 255, 0, dac_tlv),

SOC_DOUBLE_R_TLV("Headphone Playback Volume", WM8960_LOUT1, WM8960_ROUT1,
		 0, 127, 0, out_tlv),
SOC_DOUBLE_R("Headphone Playback ZC Switch", WM8960_LOUT1, WM8960_ROUT1,
	7, 1, 0),

SOC_DOUBLE_R_TLV("Speaker Playback Volume", WM8960_LOUT2, WM8960_ROUT2,
		 0, 127, 0, out_tlv),
SOC_DOUBLE_R("Speaker Playback ZC Switch", WM8960_LOUT2, WM8960_ROUT2,
	7, 1, 0),
SOC_SINGLE("Speaker DC Volume", WM8960_CLASSD3, 3, 5, 0),
SOC_SINGLE("Speaker AC Volume", WM8960_CLASSD3, 0, 5, 0),

SOC_SINGLE("PCM Playback -6dB Switch", WM8960_DACCTL1, 7, 1, 0),
SOC_ENUM("ADC Polarity", wm8960_enum[0]),
SOC_SINGLE("ADC High Pass Filter Switch", WM8960_DACCTL1, 0, 1, 0),

SOC_ENUM("DAC Polarity", wm8960_enum[1]),
SOC_SINGLE_BOOL_EXT("DAC Deemphasis Switch", 0,
		    wm8960_get_deemph, wm8960_put_deemph),

SOC_ENUM("3D Filter Upper Cut-Off", wm8960_enum[2]),
SOC_ENUM("3D Filter Lower Cut-Off", wm8960_enum[3]),
SOC_SINGLE("3D Volume", WM8960_3D, 1, 15, 0),
SOC_SINGLE("3D Switch", WM8960_3D, 0, 1, 0),

SOC_ENUM("ALC Function", wm8960_enum[4]),
SOC_SINGLE("ALC Max Gain", WM8960_ALC1, 4, 7, 0),
SOC_SINGLE("ALC Target", WM8960_ALC1, 0, 15, 1),
SOC_SINGLE("ALC Min Gain", WM8960_ALC2, 4, 7, 0),
SOC_SINGLE("ALC Hold Time", WM8960_ALC2, 0, 15, 0),
SOC_ENUM("ALC Mode", wm8960_enum[5]),
SOC_SINGLE("ALC Decay", WM8960_ALC3, 4, 15, 0),
SOC_SINGLE("ALC Attack", WM8960_ALC3, 0, 15, 0),

SOC_SINGLE("Noise Gate Threshold", WM8960_NOISEG, 3, 31, 0),
SOC_SINGLE("Noise Gate Switch", WM8960_NOISEG, 0, 1, 0),

SOC_DOUBLE_R_TLV("ADC PCM Capture Volume", WM8960_LADC, WM8960_RADC,
	0, 255, 0, adc_tlv),

SOC_SINGLE_TLV("Left Output Mixer Boost Bypass Volume",
	       WM8960_BYPASS1, 4, 7, 1, bypass_tlv),
SOC_SINGLE_TLV("Left Output Mixer LINPUT3 Volume",
	       WM8960_LOUTMIX, 4, 7, 1, bypass_tlv),
SOC_SINGLE_TLV("Right Output Mixer Boost Bypass Volume",
	       WM8960_BYPASS2, 4, 7, 1, bypass_tlv),
SOC_SINGLE_TLV("Right Output Mixer RINPUT3 Volume",
	       WM8960_ROUTMIX, 4, 7, 1, bypass_tlv),
};

```

This array can use amixer/alsamixer to control.

```sh
root@ess:~# amixer controls
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
