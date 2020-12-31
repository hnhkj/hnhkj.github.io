# mt76xx_driver


## 

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