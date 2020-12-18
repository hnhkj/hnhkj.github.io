# mt7688 driver


## 驱动文件

```
sound/soc/codec/wm8960.c
sound/soc/codec/wm8960.h
sound/soc/mtk/i2c_wm8960.c
sound/soc/mtk/i2c_wm8960.h
sound/soc/mtk/i2s_ctrl.c
sound/soc/mtk/i2s_ctrl.h
sound/soc/mtk/i2s_debug.c
sound/soc/mtk/mt76xx_i2s.c
sound/soc/mtk/mt76xx_i2s.h
sound/soc/mtk/mt76xx_machine.c
sound/soc/mtk/mt76xx_machine.h
sound/soc/mtk/mt76xx_pcm.c
sound/soc/mtk/mt76xx_pcm.h
sound/soc/mtk/ralink_gdma.c
sound/soc/mtk/ralink_gdma.h
```


#### i2c_wm8960.c/h

* i2c_wm8960.c
	* audiohw_preinit()
	* audiohw_postinit()
	* audiohw_close()
	* audiohw_set_frequency()
	* audiohw_mute()
	* audiohw_micboost()
	* audiohw_micin()
	* audiohw_set_apll()
	* audiohw_set_lineout_vol()
	* audiohw_set_linein_vol()
	* audiohw_mute()
	* audiohw_loopback()
	* audiohw_codec_exlbk()
	* audiohw_bypass()
	*
	* wm8960_i2c_init()
	* wm8960_i2c_exit()
	*
	* module_init(wm8960_i2c_init)
	* module_exit(wm8960_i2c_exit)

#### mt76xx_pcm.c

* mt76xx_pcm.c
	* mt76xx_pcm_open()
	* mt76xx_pcm_close()
	* mt76xx_pcm_pointer()
	* mt76xx_pcm_trigger()
	* mt76xx_pcm_copy()
	* mt76xx_pcm_mmap()
	* mt76xx_pcm_prepare()
	* mt76xx_pcm_hw_params()
	* mt76xx_pcm_hw_free()
	* mt76xx_pcm_free_dma_buffer()
	* mt76xx_pcm_allocate_dma_buffer()
	* mt76xx_pcm_new()
	* mt76xx_pcm_free()
	* mt76xx_platform_drv_probe()
	* mt76xx_platform_drv_remove()

#### mt76xx_i2s.c
	
* mt76xx_i2s.c
	* mt76xx_i2s_set_fmt()
	* mt76xx_i2s_play_prepare()
	* mt76xx_i2s_rec_prepare()
	* mt76xx_i2s_startup()
	* mt76xx_i2s_hw_param()
	* mt76xx_i2s_hw_free()
	* mt76xx_i2s_prepare()
	* 
	* mt76xx_i2s_drv_probe()
	* mt76xx_i2s_drv_remove()
	* 
	* mt76xx_i2s_init()
	* mt76xx_i2s_exit()
	*
	* module_init(mt76xx_i2s_init)
	* module_exit(mt76xx_i2s_exit)

#### i2s_ctrl.c/h

* i2s_ctrl.c/h
	* i2s_mod_init()
	* i2s_mod_exit()
	* i2s_open()
	* i2s_release()
	* i2s_mmap_alloc()
	* i2s_mmap_remap()
	* i2s_mmap()
	* i2s_mem_unmap()
	* i2s_param_init()
	* i2s_txbuf_alloc()
	* i2s_rxbuf_alloc()
	* i2s_txPagebuf_alloc()
	* i2s_rxPagebuf_alloc()
	* i2s_txbuf_free()
	* i2s_rxbuf_freee()
	* i2s_txPagebuf_free()
	* i2s_rxPagebuf_free()
	* i2s_reset_tx_param()
	* i2s_reset_rx_param()
	* i2s_pll_config_mt7621()
	* i2s_pll_refclk_set()
	* i2s_pll_config_mt7623()
	* i2s_driving_strength_adjust()
	* i2s_refclk_12m_enable()
	* i2s_refclk_18p432m_enable()
	* i2s_refclk_disable()
	* i2s_refclk_gpio_out_config()
	* i2s_refclk_gpio_in_config()
	* i2s_slave_clock_gpio_in_mt7623()
	* i2s_master_clock_gpio_out_mt7623()
	* i2s_share_pin_mt7623()
	* i2s_share_pin_config()
	* i2s_ws_config()
	* i2s_mode_config()
	* i2s_codec_frequency_config()
	* i2s_clock_enable()
	* i2s_clock_disable()
	* i2s_codec_enable()
	* i2s_codec_disable()
	* i2s_reset_config()
	* i2s_tx_config()
	* i2s_rx_config()
	* i2s_tx_enable()
	* i2s_rx_enable()
	* i2s_tx_disable()
	* i2s_rx_disable()
	* i2s_dma_tx_transf_data()
	* i2s_dma_tx_transf_zero()
	* i2s_dma_rx_transf_data()
	* i2s_dma_rx_transf_zero()
	* i2s_dma_tx_handler()
	* i2s_dma_rx_handler()
	* i2s_init_status()
	* i2s_tx_task()
	* i2s_rx_task()
	* i2s_dma_unmask_handler()
	* i2s_dma_tx_unmask_handler()
	* i2s_dma_rx_unmask_handler()
	* i2s_dma_mask_handler()
	* i2s_dma_tx_init()
	* i2s_dma_rx_init()
	* i2s_dma_tx_end_handle()
	* i2s_tx_end_sleep_on()
	* i2s_rx_end_sleep_on()
	* i2s_dma_tx_soft_stop()
	* i2s_dma_rx_soft_stop()
	* i2s_gen_test_pattern()
	* i2s_put_audio()　　　　　　-- 非常重要
	* i2s_get_audio()			-- 非常重要
	* i2s_ioctl()
	* 
	* i2s_memPool_Alloc()
	* i2s_memPool_free()
	* i2s_page_prepare()
	* i2s_page_release()
	* i2s_startup()
	* gdma_En_Switch()
	* i2s_audio_exchange()
	* gdma_mask_handler()
	* gdma_unmask_handler()
	* i2s_mmap_phys_addr()
	* 



#### 声卡启动流程

* i2s_startup()   - mtk/i2s_ctrl.c
* 0xB0000034 = 0x04020000
* 0xB0000034 = 0x04000000
* wm8960_set_dai_pll() - 设置PLL频率  - codec/wm8960.c
* i2s_ioctl() - mtk/i2s_ctrl.c  - 设置i2s频率
* i2s_memPool_free() - mtk/i2s_ctrl.c - 释放mmap内存
* mt76xx_pcm_free_dma_buffer() - mt76xx_pcm.c - 释放dma buffer
* i2s_mmap_alloc() - mtk/i2s_ctrl.c - 映射内存mmap
* i2s_mmap_remap() - mtk/i2s_ctrl.c - C


* i2s_refclk_12m_enable() - 始终使能 i2s_ctrl.c
* i2s_ws_config()


## 参考信息

#### ESS dmesg 打印信息


```
[  134.520000] I2S: i2s_startup*******************
[  134.530000] 0xB0000034 = 0x04020000
[  134.540000] 0xB0000034 = 0x04000000
[  134.560000] WM: **wm8960_set_dai_pll**
[  134.570000] WM: WM8960 PLL: setting 12000000Hz->24576000Hz
[  134.580000] WM: WM8960 PLL: N=8 K=3126e9 pre_div=0
[  134.860000] WM: 1[4]185
[  134.870000] I2S: set audio sampling rate to 8000 Hz
[  134.890000] I2S: is_tx_mmap:0
[  134.890000] I2S: is_rx_mmap:0
[  134.900000] capture free_dma_buffer
[  134.910000] ptri2s_config->mmap_index:0
[  134.910000] I2S: mmap_index=8
[  134.920000] I2S: MMAP[8]=0x86280000, i2s_mmap_addr[8]=0x06280000
[  134.930000] I2S: MMAP[9]=0x86281800, i2s_mmap_addr[9]=0x06281800
[  134.940000] I2S: MMAP[10]=0x86283000, i2s_mmap_addr[10]=0x06283000
[  134.960000] I2S: MMAP[11]=0x86284800, i2s_mmap_addr[11]=0x06284800
[  134.970000] I2S: MMAP[12]=0x86286000, i2s_mmap_addr[12]=0x06286000
[  134.980000] I2S: MMAP[13]=0x86287800, i2s_mmap_addr[13]=0x06287800
[  134.990000] I2S: MMAP[14]=0x86289000, i2s_mmap_addr[14]=0x06289000
[  135.000000] I2S: MMAP[15]=0x8628A800, i2s_mmap_addr[15]=0x0628a800
[  135.020000] I2S: <><><><><><>mt76xx_pcm_mmap<><><><>
[  135.030000] I2S: ******* mt76xx_pcm_mmap: size :c000 end:76676000 start:7666a000 *******
[  135.040000] I2S: i2s_mmap_remap:8
[  135.060000] I2S: ******* mt76xx_pcm_prepare *******
[  135.060000] I2S: ******* prepare: SNDRV_PCM_STREAM_CAPTURE*******
[  135.080000] I2S: ******* i2s_dma_rx_init *******
[  135.090000] I2S: gdma_En_Switch:1
[  135.090000] I2S: RX:wordLen=0, sysEndian=0
[  135.100000] 0xB0000A00 = 0x20014040
[  135.110000] 0xB0000A18 = 0x00000000
[  135.110000] I2S: Adjust MT7628 current's driving strngth
[  135.130000] 0xB0001354 = 0x00000000
[  135.130000] 0xB0001364 = 0x00000020
[  135.140000] I2S: Enable SoC MCLK 12Mhz
[  135.150000] I2S: turn on REFCLK output for MCLK1
[  135.160000] 0xB000002C = 0x0020120C
[  135.160000] 0xB0000060 = 0x00014045
[  135.170000] 0xB0000060 = 0x00014045
[  135.180000] 0xB0000060 = 0x00014005
[  135.180000] I2S: Internal REFCLK with fractional division
[  135.190000] I2S: 16 bit int table
[  135.200000] 0xB0000A24 = 0x000003A9
[  135.210000] I2S: 16 bit comp table
[  135.210000] 0xB0000A20 = 0x80000100
[  135.220000] 0xB0000A00 = 0x20004040
[  135.230000] 0xB0000A00 = 0x60104040
[  135.240000] 0xB0000A00 = 0xE0104040
[  135.240000] I2S: i2s_rx_enable done
[  135.270000] I2S: is_tx_mmap:0
[  135.280000] I2S: is_rx_mmap:1
[  135.280000] playback free_dma_buffer
[  135.290000] ptri2s_config->mmap_index:16
[  135.300000] I2S: mmap_index=0
[  135.300000] I2S: MMAP[0]=0x86290000, i2s_mmap_addr[0]=0x06290000
[  135.320000] I2S: MMAP[1]=0x86291800, i2s_mmap_addr[1]=0x06291800
[  135.330000] I2S: MMAP[2]=0x86293000, i2s_mmap_addr[2]=0x06293000
[  135.340000] I2S: MMAP[3]=0x86294800, i2s_mmap_addr[3]=0x06294800
[  135.350000] I2S: MMAP[4]=0x86296000, i2s_mmap_addr[4]=0x06296000
[  135.360000] I2S: MMAP[5]=0x86297800, i2s_mmap_addr[5]=0x06297800
[  135.380000] I2S: MMAP[6]=0x86299000, i2s_mmap_addr[6]=0x06299000
[  135.390000] I2S: MMAP[7]=0x8629A800, i2s_mmap_addr[7]=0x0629a800
[  135.400000] I2S: <><><><><><>mt76xx_pcm_mmap<><><><>
[  135.410000] I2S: ******* mt76xx_pcm_mmap: size :c000 end:7666a000 start:7665e000 *******
[  135.430000] I2S: i2s_mmap_remap:0
[  135.460000] I2S: ******* mt76xx_pcm_prepare *******
[  135.460000] I2S: ******* prepare: SNDRV_PCM_STREAM_PLAYBACK*******
[  135.480000] I2S: ******* i2s_dma_tx_init *******
[  135.490000] I2S: TX:wordLen=0, sysEndian=0
[  135.490000] 0xB0000A00 = 0xE0104040
[  135.500000] I2S: internal loopback: 0
[  135.510000] 0xB0000A18 = 0x00000000
[  135.520000] 0xB0000A00 = 0xE1104040
[  135.520000] 0xB0000A00 = 0xE1104040
[  135.530000] I2S: i2s_tx_enable done
[  135.540000] I2S: I2S_TXENABLE done
```