# Linkit Smart 7688 //OpenWrt - 2port

---
title: "Linkit Smart 7688 //OpenWrt - 2port"
author: "hnhkj@163.com"
date: "2016-6-18"
output: html_document
---

## OpenWrt Linkit Smart 7688如何多使用一个网口

在我的一个项目中，需要使用两个网口，一个WAN和一个LAN。但是OpenWrt默认是使用了一个端口port0，我该如何实现2个port呢？对于OpenWrt来讲，它本身就是一个Router程序。理论上来讲，这样该是非常容易的事情，也应该是基本的功能。可是在这个领域确实是一个生手。我到处寻求帮助，寻求了多日，依然没有结果。无奈之下，只能自己深入进去研究实现的方法了。

对于默认的OpenWrt，Port0的插拔系统都能检测并且相应它。而Port1的插拔没有发现任何的反应。所以，我觉得应该重这里开始入手。

```
[ 6826.460000] rt305x-esw 10110000.esw: link changed 0x00
[ 6828.070000] rt305x-esw 10110000.esw: link changed 0x01
```

#### 驱动文件查找

对于在Linux中的任何更改和关键支持，我们都可以在下面的目录中查找到。因为，下面目录中的文件是根据不同的芯片对原生linux进行的针对性的修改。包括驱动，服务等信息，我们都可以在下面的目录文件中查询到蛛丝马迹。

```
openwrt\target\linux\ramips\patches-3.18
```


经过查找，`0035-NET-MIPS-add-ralink-SoC-ethernet-driver.patch`文件是针对网络的修改，因而我主要从这个文件开始进行工作。在该文件中，我查询到了驱动功能修改的文件名和内容。

```
/linux-3.18.29/arch/mips/ralink/rt305x.c
/linux-3.18.29/drivers/net/ethernet/Kconfig
/linux-3.18.29/drivers/net/ethernet/Makefile
```


`0301-mt7688-detect.patch`文件也可以让我查询到一些相关的文件。

```
/linux-3.18.29/arch/mips/include/asm/mach-ralink/mt7620.h
/linux-3.18.29/arch/mips/ralink/mt7620.c
/linux-3.18.29/arch/mips/include/asm/mach-ralink/ralink_regs.h
*/linux-3.18.29/drivers/net/ethernet/ralink/esw_rt3052.c*
```

#### 相关文件位置

经过对文件查询到的文件进行分析，网络的驱动相关文件包含在`/linux-ramips_mt7688/linux-3.18.29/drivers/net/ethernet/ralink`目录下。该目录目前的文件状态，我在下面给出了一个列表。

* ralink\
    * **esw_rt3052.c**  -- 选择文件
    * **esw_rt3052.h**  -- 选择文件
    * gsw_mt7620a.c
    * gsw_mt7620a.h
    * **Kconfig**
    * **Makefile**
    * mdio.c
    * mdio.h
    * mdio_rt2880.c
    * mdio_rt2880.h
    * mt7530.c
    * mt7530.h
    * ralink_ethtool.c
    * ralink_ethtool.h
    * ralink_soc_eth.c
    * ralink_soc_eth.h
    * soc_mt7620.c
    * **soc_rt305x.c**   -- 选择文件
    * **soc_rt305x.h**   -- 选择文件
    * soc_rt2880.c
    * soc_rt3883.c

对于OpenWrt编译调用的驱动文件，我们需要分析根据`.config`文件的配置情况而定。目前我采用的是默认的状态。因而，我就分析`openwrt\target\linux\ramips\mt7688\config-3.18`中的配置选择，再配合驱动目录下的`Kconfig`和`Makefile`文件来确定编译的文件。**经过对文件分析，我确定了几个文件，在目录列表中我做了备注。**

`config-3.18`中的相关选项：

```
CONFIG_NET_RALINK=y
CONFIG_NET_RALINK_ESW_RT3052=y
# CONFIG_NET_RALINK_MT7620 is not set
CONFIG_NET_RALINK_RT305X=y
CONFIG_NO_GENERIC_PCI_IOPORT_MAP=y
```

为了便于分析，我列出了`Kconfig`和`Makefile`的文件内容。

`Kconfig`文件内容。

```
config NET_RALINK
    tristate "Ralink ethernet driver"
    depends on RALINK
    help
      This driver supports the ethernet mac inside the ralink wisocs

if NET_RALINK
choice
    prompt "MAC type"

config NET_RALINK_RT288X
    bool "RT288X"
    depends on SOC_RT288X

config NET_RALINK_RT305X
    bool "RT305X"
    depends on (SOC_RT305X || SOC_MT7620)

config NET_RALINK_RT3883
    bool "RT3883"
    depends on SOC_RT3883

config NET_RALINK_MT7620
    bool "MT7620"
    depends on (SOC_MT7620 || SOC_MT7621)

endchoice

config NET_RALINK_MDIO
    def_bool NET_RALINK
    depends on (NET_RALINK_RT288X || NET_RALINK_RT3883 || NET_RALINK_MT7620 || NET_RALINK_MT7621)
    select PHYLIB

config NET_RALINK_MDIO_RT2880
    def_bool NET_RALINK
    depends on (NET_RALINK_RT288X || NET_RALINK_RT3883)
    select NET_RALINK_MDIO

config NET_RALINK_ESW_RT3052
    def_bool NET_RALINK
    depends on NET_RALINK_RT305X
    select PHYLIB
    select SWCONFIG

config NET_RALINK_GSW_MT7620
    def_bool NET_RALINK
    depends on NET_RALINK_MT7620 || NET_RALINK_MT7621
    select NET_RALINK_MDIO
    select PHYLIB
    select SWCONFIG
endif
```


`Makefile`文件内容。

```
#
# Makefile for the Ralink SoCs built-in ethernet macs
#

ralink-eth-y                    += ralink_soc_eth.o ralink_ethtool.o

ralink-eth-$(CONFIG_NET_RALINK_MDIO)        += mdio.o
ralink-eth-$(CONFIG_NET_RALINK_MDIO_RT2880) += mdio_rt2880.o

ralink-eth-$(CONFIG_NET_RALINK_ESW_RT3052)  += esw_rt3052.o
ralink-eth-$(CONFIG_NET_RALINK_GSW_MT7620)  += gsw_mt7620a.o mt7530.o

ralink-eth-$(CONFIG_NET_RALINK_RT288X)      += soc_rt2880.o
ralink-eth-$(CONFIG_NET_RALINK_RT305X)      += soc_rt305x.o
ralink-eth-$(CONFIG_NET_RALINK_RT3883)      += soc_rt3883.o
ralink-eth-$(CONFIG_NET_RALINK_MT7620)      += soc_mt7620.o
ralink-eth-$(CONFIG_NET_RALINK_MT7621)      += soc_mt7621.o

obj-$(CONFIG_NET_RALINK)            += ralink-eth.o
```


## 源代码分析

源代码分析的话，应该大家都可以了。这里我就不多讲了。我只是写出问题的关键位置在哪里。


#### esw_rt3052.c文件分析**


**驱动输入数据数据结构**

```
struct rt305x_esw {
    struct device       *dev;
    void __iomem        *base;
    int         irq;
    const struct rt305x_esw_platform_data *pdata;
    /* Protects against concurrent register rmw operations. */
    spinlock_t      reg_rw_lock;

    unsigned char       port_map;
    unsigned char       port_disable;
    unsigned int        reg_initval_fct2;
    unsigned int        reg_initval_fpa2;
    unsigned int        reg_led_polarity;


    struct switch_dev   swdev;
    bool            global_vlan_enable;
    bool            alt_vlan_disable;
    int         bc_storm_protect;
    int         led_frequency;
    struct esw_vlan vlans[RT305X_ESW_NUM_VLANS];
    struct esw_port ports[RT305X_ESW_NUM_PORTS];

};
```


**驱动接口**

```
static struct platform_driver esw_driver = {
    .probe = esw_probe,
    .remove = esw_remove,
    .driver = {
        .name = "rt305x-esw",
        .owner = THIS_MODULE,
        .of_match_table = ralink_esw_match,
    },
};

int __init rtesw_init(void)
{
    return platform_driver_register(&esw_driver);
}

void rtesw_exit(void)
{
    platform_driver_unregister(&esw_driver);
}

```


**驱动执行代码**

```
//驱动器启动
static int esw_probe(struct platform_device *pdev)
{
    struct device_node *np = pdev->dev.of_node;
    const struct rt305x_esw_platform_data *pdata;
    const __be32 *port_map, *port_disable, *reg_init;
    struct rt305x_esw *esw;
    struct switch_dev *swdev;
    struct resource *res, *irq;
    int err;

    pdata = pdev->dev.platform_data;
    if (!pdata) {
        const struct of_device_id *match;
        match = of_match_device(ralink_esw_match, &pdev->dev);
        if (match)
            pdata = (struct rt305x_esw_platform_data *) match->data;
    }
    if (!pdata)
        return -EINVAL;

    res = platform_get_resource(pdev, IORESOURCE_MEM, 0);
    if (!res) {
        dev_err(&pdev->dev, "no memory resource found\n");
        return -ENOMEM;
    }

    irq = platform_get_resource(pdev, IORESOURCE_IRQ, 0);
    if (!irq) {
        dev_err(&pdev->dev, "no irq resource found\n");
        return -ENOMEM;
    }

    esw = kzalloc(sizeof(struct rt305x_esw), GFP_KERNEL);
    if (!esw) {
        dev_err(&pdev->dev, "no memory for private data\n");
        return -ENOMEM;
    }

    esw->dev = &pdev->dev;
    esw->irq = irq->start;
    esw->base = ioremap(res->start, resource_size(res));
    if (!esw->base) {
        dev_err(&pdev->dev, "ioremap failed\n");
        err = -ENOMEM;
        goto free_esw;
    }

    port_map = of_get_property(np, "ralink,portmap", NULL);
    if (port_map)
        esw->port_map = be32_to_cpu(*port_map);

    port_disable = of_get_property(np, "ralink,portdisable", NULL);
    if (port_disable)
        esw->port_disable = be32_to_cpu(*port_disable);

    reg_init = of_get_property(np, "ralink,fct2", NULL);
    if (reg_init)
        esw->reg_initval_fct2 = be32_to_cpu(*reg_init);

    reg_init = of_get_property(np, "ralink,fpa2", NULL);
    if (reg_init)
        esw->reg_initval_fpa2 = be32_to_cpu(*reg_init);

    reg_init = of_get_property(np, "ralink,led_polarity", NULL);
    if (reg_init)
        esw->reg_led_polarity = be32_to_cpu(*reg_init);

    swdev = &esw->swdev;
    swdev->of_node = pdev->dev.of_node;
    swdev->name = "rt305x-esw";
    swdev->alias = "rt305x";
    swdev->cpu_port = RT305X_ESW_PORT6;
    swdev->ports = RT305X_ESW_NUM_PORTS;
    swdev->vlans = RT305X_ESW_NUM_VIDS;
    swdev->ops = &esw_ops;

    err = register_switch(swdev, NULL);
    if (err < 0) {
        dev_err(&pdev->dev, "register_switch failed\n");
        goto unmap_base;
    }

    platform_set_drvdata(pdev, esw);

    esw->pdata = pdata;
    spin_lock_init(&esw->reg_rw_lock);

    esw_hw_init(esw);

    esw_w32(esw, RT305X_ESW_PORT_ST_CHG, RT305X_ESW_REG_ISR);
    esw_w32(esw, ~RT305X_ESW_PORT_ST_CHG, RT305X_ESW_REG_IMR);
    request_irq(esw->irq, esw_interrupt, 0, "esw", esw);

    return 0;

unmap_base:
    iounmap(esw->base);
free_esw:
    kfree(esw);
    return err;
}

// 驱动器删除
static int esw_remove(struct platform_device *pdev)
{
    struct rt305x_esw *esw;

    esw = platform_get_drvdata(pdev);
    if (esw) {
        unregister_switch(&esw->swdev);
        platform_set_drvdata(pdev, NULL);
        iounmap(esw->base);
        kfree(esw);
    }

    return 0;
}
```


**硬件初始化函数**

```
static void esw_hw_init(struct rt305x_esw *esw)
{
    int i;
    u8 port_disable = 0;
    u8 port_map = RT305X_ESW_PMAP_LLLLLL;

    /* vodoo from original driver */
    esw_w32(esw, 0xC8A07850, RT305X_ESW_REG_FCT0);  //初始化FCT0寄存器
    esw_w32(esw, 0x00000000, RT305X_ESW_REG_SGC2);  //初始化SGC2寄存器
    /* Port priority 1 for all ports, vlan enabled. */
    esw_w32(esw, 0x00005555 |
              (RT305X_ESW_PORTS_ALL << RT305X_ESW_PFC1_EN_VLAN_S),
              RT305X_ESW_REG_PFC1);

    /* Enable all ports, Back Pressure and Flow Control */
    esw_w32(esw,
              ((RT305X_ESW_PORTS_ALL << RT305X_ESW_POC0_EN_BP_S) |
               (RT305X_ESW_PORTS_ALL << RT305X_ESW_POC0_EN_FC_S)),
              RT305X_ESW_REG_POC0);

    /* Enable Aging, and VLAN TAG removal */
    esw_w32(esw,
              ((RT305X_ESW_PORTS_ALL << RT305X_ESW_POC2_ENAGING_S) |
               (RT305X_ESW_PORTS_NOCPU << RT305X_ESW_POC2_UNTAG_EN_S)),
              RT305X_ESW_REG_POC2);

    if (esw->reg_initval_fct2)
        esw_w32(esw, esw->reg_initval_fct2, RT305X_ESW_REG_FCT2);
    else
        esw_w32(esw, esw->pdata->reg_initval_fct2, RT305X_ESW_REG_FCT2);

    /*
     * 300s aging timer, max packet len 1536, broadcast storm prevention
     * disabled, disable collision abort, mac xor48 hash, 10 packet back
     * pressure jam, GMII disable was_transmit, back pressure disabled,
     * 30ms led flash, unmatched IGMP as broadcast, rmc tb fault to all
     * ports.
     */
    esw_w32(esw, 0x0008a301, RT305X_ESW_REG_SGC);

    /* Setup SoC Port control register//设置SoC口控制寄存器 */
    esw_w32(esw,
              (RT305X_ESW_SOCPC_CRC_PADDING |
               (RT305X_ESW_PORTS_CPU << RT305X_ESW_SOCPC_DISUN2CPU_S) |
               (RT305X_ESW_PORTS_CPU << RT305X_ESW_SOCPC_DISMC2CPU_S) |
               (RT305X_ESW_PORTS_CPU << RT305X_ESW_SOCPC_DISBC2CPU_S)),
              RT305X_ESW_REG_SOCPC);

    if (esw->reg_initval_fpa2)
        esw_w32(esw, esw->reg_initval_fpa2, RT305X_ESW_REG_FPA2);
    else
        esw_w32(esw, esw->pdata->reg_initval_fpa2, RT305X_ESW_REG_FPA2);
    esw_w32(esw, 0x00000000, RT305X_ESW_REG_FPA);   // 关闭Port4~Port0口

    /* Force Link/Activity on ports */
    esw_w32(esw, 0x00000005, RT305X_ESW_REG_P0LED); //LED默认link/activity
    esw_w32(esw, 0x00000005, RT305X_ESW_REG_P1LED); 
    esw_w32(esw, 0x00000005, RT305X_ESW_REG_P2LED);
    esw_w32(esw, 0x00000005, RT305X_ESW_REG_P3LED);
    esw_w32(esw, 0x00000005, RT305X_ESW_REG_P4LED);

    /* Copy disabled port configuration from device tree setup */
    port_disable = esw->port_disable;   //默认为0

    /* Disable nonexistent ports by reading the switch config
     * after having enabled all possible ports above
     */
    /*在上面使能所有可能的口之后，通过读`switch config'禁止不存在的口 */
    port_disable |= esw_get_port_disable(esw);  // 默认port_disable=0x20

    for (i = 0; i < 6; i++)
        esw->ports[i].disable = (port_disable & (1 << i)) != 0;  // 默认 

    if (ralink_soc == RT305X_SOC_RT3352) {
        /* reset EPHY */
        fe_reset(RT5350_RESET_EPHY);

        rt305x_mii_write(esw, 0, 31, 0x8000);
        for (i = 0; i < 5; i++) {
            if (esw->ports[i].disable) {
                rt305x_mii_write(esw, i, MII_BMCR, BMCR_PDOWN);
            } else {
                rt305x_mii_write(esw, i, MII_BMCR,
                     BMCR_FULLDPLX |
                     BMCR_ANENABLE |
                     BMCR_SPEED100);
            }
            /* TX10 waveform coefficient LSB=0 disable PHY */
            rt305x_mii_write(esw, i, 26, 0x1601);
            /* TX100/TX10 AD/DA current bias */
            rt305x_mii_write(esw, i, 29, 0x7016);
            /* TX100 slew rate control */
            rt305x_mii_write(esw, i, 30, 0x0038);
        }

        /* select global register */
        rt305x_mii_write(esw, 0, 31, 0x0);
        /* enlarge agcsel threshold 3 and threshold 2 */
        rt305x_mii_write(esw, 0, 1, 0x4a40);
        /* enlarge agcsel threshold 5 and threshold 4 */
        rt305x_mii_write(esw, 0, 2, 0x6254);
        /* enlarge agcsel threshold  */
        rt305x_mii_write(esw, 0, 3, 0xa17f);
        rt305x_mii_write(esw, 0,12, 0x7eaa);
        /* longer TP_IDL tail length */
        rt305x_mii_write(esw, 0, 14, 0x65);
        /* increased squelch pulse count threshold. */
        rt305x_mii_write(esw, 0, 16, 0x0684);
        /* set TX10 signal amplitude threshold to minimum */
        rt305x_mii_write(esw, 0, 17, 0x0fe0);
        /* set squelch amplitude to higher threshold */
        rt305x_mii_write(esw, 0, 18, 0x40ba);
        /* tune TP_IDL tail and head waveform, enable power down slew rate control */
        rt305x_mii_write(esw, 0, 22, 0x253f);
        /* set PLL/Receive bias current are calibrated */
        rt305x_mii_write(esw, 0, 27, 0x2fda);
        /* change PLL/Receive bias current to internal(RT3350) */
        rt305x_mii_write(esw, 0, 28, 0xc410);
        /* change PLL bias current to internal(RT3052_MP3) */
        rt305x_mii_write(esw, 0, 29, 0x598b);
        /* select local register */
        rt305x_mii_write(esw, 0, 31, 0x8000);
    } else if (ralink_soc == RT305X_SOC_RT5350) {
        /* reset EPHY */
        fe_reset(RT5350_RESET_EPHY);

        /* set the led polarity */
        esw_w32(esw, esw->reg_led_polarity & 0x1F, RT5350_EWS_REG_LED_POLARITY);

        /* local registers */
        rt305x_mii_write(esw, 0, 31, 0x8000);
        for (i = 0; i < 5; i++) {
            if (esw->ports[i].disable) {
                rt305x_mii_write(esw, i, MII_BMCR, BMCR_PDOWN);
            } else {
                rt305x_mii_write(esw, i, MII_BMCR,
                     BMCR_FULLDPLX |
                     BMCR_ANENABLE |
                     BMCR_SPEED100);
            }
            /* TX10 waveform coefficient LSB=0 disable PHY */
            rt305x_mii_write(esw, i, 26, 0x1601);
            /* TX100/TX10 AD/DA current bias */
            rt305x_mii_write(esw, i, 29, 0x7015);
            /* TX100 slew rate control */
            rt305x_mii_write(esw, i, 30, 0x0038);
        }

        /* global registers */
        rt305x_mii_write(esw, 0, 31, 0x0);
        /* enlarge agcsel threshold 3 and threshold 2 */
        rt305x_mii_write(esw, 0, 1, 0x4a40);
        /* enlarge agcsel threshold 5 and threshold 4 */
        rt305x_mii_write(esw, 0, 2, 0x6254);
        /* enlarge agcsel threshold 6 */
        rt305x_mii_write(esw, 0, 3, 0xa17f);
        rt305x_mii_write(esw, 0, 12, 0x7eaa);
        /* longer TP_IDL tail length */
        rt305x_mii_write(esw, 0, 14, 0x65);
        /* increased squelch pulse count threshold. */
        rt305x_mii_write(esw, 0, 16, 0x0684);
        /* set TX10 signal amplitude threshold to minimum */
        rt305x_mii_write(esw, 0, 17, 0x0fe0);
        /* set squelch amplitude to higher threshold */
        rt305x_mii_write(esw, 0, 18, 0x40ba);
        /* tune TP_IDL tail and head waveform, enable power down slew rate control */
        rt305x_mii_write(esw, 0, 22, 0x253f);
        /* set PLL/Receive bias current are calibrated */
        rt305x_mii_write(esw, 0, 27, 0x2fda);
        /* change PLL/Receive bias current to internal(RT3350) */
        rt305x_mii_write(esw, 0, 28, 0xc410);
        /* change PLL bias current to internal(RT3052_MP3) */
        rt305x_mii_write(esw, 0, 29, 0x598b);
        /* select local register */
        rt305x_mii_write(esw, 0, 31, 0x8000);
    } else if (ralink_soc == MT762X_SOC_MT7628AN || ralink_soc == MT762X_SOC_MT7688) { //********默认运行到此处 *********
        int i;
//      u32 phy_val;
        u32 val;

        /* reset EPHY */
        fe_reset(RT5350_RESET_EPHY);

        rt305x_mii_write(esw, 0, 31, 0x2000); /* change G2 page */
        rt305x_mii_write(esw, 0, 26, 0x0020);

        for (i = 0; i < 5; i++) {
            rt305x_mii_write(esw, i, 31, 0x8000); //change L0 page
            rt305x_mii_write(esw, i,  0, 0x3100);
//          mii_mgr_read(i, 26, &phy_val);// EEE setting
//          phy_val |= (1 << 5);
//          rt305x_mii_write(esw, i, 26, phy_val);
            rt305x_mii_write(esw, i, 30, 0xa000);
            rt305x_mii_write(esw, i, 31, 0xa000); // change L2 page
            rt305x_mii_write(esw, i, 16, 0x0606);
            rt305x_mii_write(esw, i, 23, 0x0f0e);
            rt305x_mii_write(esw, i, 24, 0x1610);
            rt305x_mii_write(esw, i, 30, 0x1f15);
            rt305x_mii_write(esw, i, 28, 0x6111);
//          mii_mgr_read(i, 4, &phy_val);
//          phy_val |= (1 << 10);
//          rt305x_mii_write(esw, i, 4, phy_val);
            rt305x_mii_write(esw, i, 31, 0x2000); // change G2 page
            rt305x_mii_write(esw, i, 26, 0x0000);
        }

        //100Base AOI setting
        rt305x_mii_write(esw, 0, 31, 0x5000);  //change G5 page
        rt305x_mii_write(esw, 0, 19, 0x004a);
        rt305x_mii_write(esw, 0, 20, 0x015a);
        rt305x_mii_write(esw, 0, 21, 0x00ee);
        rt305x_mii_write(esw, 0, 22, 0x0033);
        rt305x_mii_write(esw, 0, 23, 0x020a);
        rt305x_mii_write(esw, 0, 24, 0x0000);
        rt305x_mii_write(esw, 0, 25, 0x024a);
        rt305x_mii_write(esw, 0, 26, 0x035a);
        rt305x_mii_write(esw, 0, 27, 0x02ee);
        rt305x_mii_write(esw, 0, 28, 0x0233);
        rt305x_mii_write(esw, 0, 29, 0x000a);
        rt305x_mii_write(esw, 0, 30, 0x0000);
    } else {
        rt305x_mii_write(esw, 0, 31, 0x8000);
        for (i = 0; i < 5; i++) {
            if (esw->ports[i].disable) {
                rt305x_mii_write(esw, i, MII_BMCR, BMCR_PDOWN);
            } else {
                rt305x_mii_write(esw, i, MII_BMCR,
                     BMCR_FULLDPLX |
                     BMCR_ANENABLE |
                     BMCR_SPEED100);
            }
            /* TX10 waveform coefficient */
            rt305x_mii_write(esw, i, 26, 0x1601);
            /* TX100/TX10 AD/DA current bias */
            rt305x_mii_write(esw, i, 29, 0x7058);
            /* TX100 slew rate control */
            rt305x_mii_write(esw, i, 30, 0x0018);
        }

        /* PHY IOT */
        /* select global register */
        rt305x_mii_write(esw, 0, 31, 0x0);
        /* tune TP_IDL tail and head waveform */
        rt305x_mii_write(esw, 0, 22, 0x052f);
        /* set TX10 signal amplitude threshold to minimum */
        rt305x_mii_write(esw, 0, 17, 0x0fe0);
        /* set squelch amplitude to higher threshold */
        rt305x_mii_write(esw, 0, 18, 0x40ba);
        /* longer TP_IDL tail length */
        rt305x_mii_write(esw, 0, 14, 0x65);
        /* select local register */
        rt305x_mii_write(esw, 0, 31, 0x8000);
    }

    if (esw->port_map)
        port_map = esw->port_map;
    else
        port_map = RT305X_ESW_PMAP_LLLLLL;

    /*
     * Unused HW feature, but still nice to be consistent here...
     * This is also exported to userspace ('lan' attribute) so it's
     * conveniently usable to decide which ports go into the wan vlan by
     * default.
     */
    esw_rmw(esw, RT305X_ESW_REG_SGC2,
               RT305X_ESW_SGC2_LAN_PMAP_M << RT305X_ESW_SGC2_LAN_PMAP_S,
               port_map << RT305X_ESW_SGC2_LAN_PMAP_S);

    /* make the switch leds blink */
    for (i = 0; i < RT305X_ESW_NUM_LEDS; i++)
        esw->ports[i].led = 0x05;

    /* Apply the empty config. */
    esw_apply_config(&esw->swdev);

    /* Only unmask the port change interrupt */
    esw_w32(esw, ~RT305X_ESW_PORT_ST_CHG, RT305X_ESW_REG_IMR);
}

```



```
static const struct switch_dev_ops esw_ops = {
    .attr_global = {
        .attr = esw_global,     //注册esw_global信息
        .n_attr = ARRAY_SIZE(esw_global),
    },
    .attr_port = {
        .attr = esw_port,       //注册esw_port信息
        .n_attr = ARRAY_SIZE(esw_port),
    },
    .attr_vlan = {
        .attr = esw_vlan,       //注册esw_vlan信息
        .n_attr = ARRAY_SIZE(esw_vlan),
    },
    .get_vlan_ports = esw_get_vlan_ports,   //得到vlan口
    .set_vlan_ports = esw_set_vlan_ports,   //设置vlan口
    .get_port_pvid = esw_get_port_pvid,     //得到port vpid标识
    .set_port_pvid = esw_set_port_pvid,     //设置port vpid标识
    .get_port_link = esw_get_port_link,     //得到port连接状态
    .apply_config = esw_apply_config,       //应用配置
    .reset_switch = esw_reset_switch,       //复位switch
};
```


**OpenWrt系统内查找到的driver文件：**

```
/sys/bus/platform/drivers/rt305x-esw
/sys/bus/platform/drivers/ralink_soc_eth
```


#### rt305x-ew函数启动顺序

我在`esw_rt3052.c`文件中增加了一些debug信息，通过这些信息可以了解该文件中函数的调用过程。以方便我了解它的工作原理。详细信息参考OpenWrt_boot.txt文件。这里我只摘录了部分相关的信息。

[    0.670000] drivers/net/ethernet/ralink/esw_rt3052.c-esw_apply_config
[    0.690000] ralink_soc_eth 10100000.ethernet eth0: ralink at 0xb0100000, irq 5
......
[    5.110000] init: - preinit -
[    5.950000] drivers/net/ethernet/ralink/esw_rt3052.c-esw_reset_switch
[    5.970000] drivers/net/ethernet/ralink/esw_rt3052.c-esw_apply_config
[    5.980000] rt305x-esw 10110000.esw: link changed 0x00
[    6.010000] drivers/net/ethernet/ralink/esw_rt3052.c-esw_set_port_pvid
[    6.030000] drivers/net/ethernet/ralink/esw_rt3052.c-esw_set_port_pvid
[    6.040000] drivers/net/ethernet/ralink/esw_rt3052.c-esw_set_vlan_ports
[    6.070000] drivers/net/ethernet/ralink/esw_rt3052.c-esw_apply_config

可以看到，在该部分有对网络端口的写操作，所以我想这部分应该是设定端口的部分。同时看到该部分代码的操作是在init:-preinit-部分。所以，我想对于端口的操作应该是通过配置完成的。所以，我觉得应该查询一些相应的网络初始化配置文件。经过查找，觉得这个文件`openwrt\target\linux\ramips\base-files\etc\board.d\02_network`非常可疑。经过对文件的查找，里面确实是配置了对网络的操作。好了，对它进行一下测试吧。

修改前：

```
    ...
    linkits7688 | \
    linkits7688d)
    ...
        wnce2001)
        ucidef_add_switch "switch0" "1" "0"
        ucidef_set_interface_lan "eth0"
        ;;
    ...
```

修改后：

```
    linkits7688 | \
    linkits7688d)
        ucidef_set_interfaces_lan_wan "eth0.1" "eth0.2"
        ucidef_add_switch "switch0" "1" "1"
        ucidef_add_switch_vlan "switch0" "1" "1 6t"
        ucidef_add_switch_vlan "switch0" "2" "0 6t"
        ;;  
```

修改后生成的network文件：

```
root@ipRec:/# cat /etc/config/network

config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fd3f:744c:07fc::/48'

config interface 'lan'
        option ifname 'eth0.1'
        option force_link '1'
        option macaddr '00:0c:43:e1:76:2a'
        option type 'bridge'
        option proto 'static'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option ipaddr '192.168.100.1'

config interface 'wan'
        option ifname 'eth0.2'
        option force_link '1'
        option macaddr '00:0c:43:e1:76:28'
        option proto 'dhcp'

config interface 'wan6'
        option ifname 'eth0.2'
        option proto 'dhcpv6'

config switch
        option name 'switch0'
        option reset '1'
        option enable_vlan '1'

config switch_vlan
        option device 'switch0'
        option vlan '1'
        option ports '1 6t'

config switch_vlan
        option device 'switch0'
        option vlan '2'
        option ports '0 6t'
```

## OpenWrt命令应用

#### `swconfig`命令

```
root@ipRec:/# swconfig
swconfig list
swconfig dev <dev> [port <port>|vlan <vlan>] (help|set <key> <value>|get <key>|load <config>|show)
```

#### `swconfig`显示交换机设备

```
root@ipRec:/# swconfig list
Found: switch0 - rt305x
```

#### `swconfig`显示交换机设备的端口数量

```
root@ipRec:/# swconfig dev rt305x help
switch0: rt305x(rt305x-esw), ports: 7 (cpu @ 6), vlans: 4096
     --switch
        Attribute 1 (int): enable_vlan (VLAN mode (1:enabled))
        Attribute 2 (int): alternate_vlan_disable (Use en_vlan instead of doubletag to disable VLAN mode)
        Attribute 3 (int): bc_storm_protect (Global broadcast storm protection (0:Disable, 1:64 blocks, 2:96 blocks, 3:128 blocks))
        Attribute 4 (int): led_frequency (LED Flash frequency (0:30mS, 1:60mS, 2:240mS, 3:480mS))
        Attribute 5 (none): apply (Activate changes in the hardware)
        Attribute 6 (none): reset (Reset the switch)
     --vlan
        Attribute 1 (ports): ports (VLAN port mapping)
     --port
        Attribute 1 (int): disable (Port state (1:disabled))
        Attribute 2 (int): doubletag (Double tagging for incoming vlan packets (1:enabled))
        Attribute 3 (int): untag (Untag (1:strip outgoing vlan tag))
        Attribute 4 (int): led (LED mode (0:link, 1:100m, 2:duplex, 3:activity, 4:collision, 5:linkact, 6:duplcoll, 7:10mact, 8:100mact, 10:blink, 11:off, 12:on))
        Attribute 5 (int): lan (HW port group (0:wan, 1:lan))
        Attribute 6 (int): recv_bad (Receive bad packet counter)
        Attribute 7 (int): recv_good (Receive good packet counter)
        Attribute 8 (int): tr_bad (Transmit bad packet counter. rt5350 only)
        Attribute 9 (int): tr_good (Transmit good packet counter. rt5350 only)
        Attribute 10 (int): pvid (Primary VLAN ID)
        Attribute 11 (string): link (Get port link information)
```
*从显示的状态开，该设备具备7个端口，cpu端口号是6，vlan地址是4096*


#### `swconfig`显示网络口的状态

```
root@ipRec:/# swconfig dev rt305x show | grep link
        link: port:0 link:up speed:100baseT full-duplex
        link: port:1 link:down
        link: port:2 link:down
        link: port:3 link:down
        link: port:4 link:down
        link: port:5 link:down
        link: port:6 link:up speed:1000baseT full-duplex
```


## 其它信息

<http://ask.wrtnode.cc/question/145>该信息讲述了为什么不能使用多余的网络，他说是因为使能了SD Card功能。我觉得对于Port2，Port3，Port4是有效的，因为它们和SD Card是复用的。但是对于Port1来讲，我想它应该是没有影响的。但是，经过我的初步测试，它应该是有影响的。因为，原生的OpenWrt编译确实可以使用2个网口。

#### mmc driver

```
drivers/mmc/card/block
```

## 结论

我采用原生的openwrt进行了编译测试，禁止了SD card（mmc）功能。上电后两个网口都能正常工作了。看来SD卡确实对网络造成了影响。最后，我要使能SD card功能，而不影响Port1。

`kmod-sdhci-mt7620`是罪魁祸首

```
ubuntu@ubuntu-System-Name:~/openwrt/openwrt$ cat sdhci.diff
1705c1705
< # CONFIG_PACKAGE_kmod-mmc is not set
---
> CONFIG_PACKAGE_kmod-mmc=y
1714,1715c1714,1715
< # CONFIG_PACKAGE_kmod-sdhci is not set
< # CONFIG_PACKAGE_kmod-sdhci-mt7620 is not set
---
> CONFIG_PACKAGE_kmod-sdhci=y
> CONFIG_PACKAGE_kmod-sdhci-mt7620=y
```


经过分析`kmod-sdhci-mt7620`相关文件位置在`openwrt\target\linux\ramips\patches-3.18\0053-mmc-MIPS-ralink-add-sdhci-for-mt7620a-SoC.patch`。

启动信息，当插入sd卡后，系统多次调用`msdc_ops_set_ios`,但是，最后显示的`DC_CFG[b0130030] Bus Width = 2`,及总线宽度为8bit。觉得不可思议，问题不知道出现在哪里。

```
[    4.190000] MTK MSDC device init.
[    4.200000] msdc_init_hw:
[    4.200000] SDC_CFG[b0130030] Bus Width = 1msdc_ops_set_ios:
[    4.210000] SDC_CFG[b0130030] Bus Width = 1msdc_ops_set_ios:
[    4.240000] SDC_CFG[b0130030] Bus Width = 1
[    4.270000] mtk-sd: MediaTek MT6575 MSDC Driver
[    4.280000] platform gpio-leds: Driver leds-gpio requests probe deferral
[    4.290000] msdc_ops_set_ios:
[    4.300000] SDC_CFG[b0130030] Bus Width = 1
[    4.300000] sdhci: Secure Digital Host Controller Interface driver
[    4.320000] sdhci: Copyright(c) Pierre Ossman
[    4.330000] sdhci-pltfm: SDHCI platform and OF driver helper
[    4.340000] msdc_ops_set_ios:
[    4.350000] SDC_CFG[b0130030] Bus Width = 1
[    4.360000] usbcore: registered new interface driver usb-storage
[    4.370000] msdc_ops_set_ios:
[    4.380000] SDC_CFG[b0130030] Bus Width = 1msdc_ops_set_ios:
[    4.400000] SDC_CFG[b0130030] Bus Width = 1msdc_ops_set_ios:
[    4.440000] SDC_CFG[b0130030] Bus Width = 1msdc_ops_set_ios:
[    4.450000] SDC_CFG[b0130030] Bus Width = 1msdc_ops_set_ios:
[    4.460000] SDC_CFG[b0130030] Bus Width = 2
[    4.470000] mmc0: new high speed SDHC card at address 1234
[    4.480000] mmcblk0: mmc0:1234 SA08G 7.21 GiB
[    4.490000]  mmcblk0: p1
```



## 附录MT7628寄存器

#### 10110084-FPA(Force Port4 Port0 Ability)

* [Bit 31:27]FORCE_MODE - Port4 ~ Port0 force mode
* [Bit 26:22]FORCE_LINK - Port4 ~ Port0 PHY Link
* [Bit 20:16]FORCE_XFC  - Port4 ~ Port0 Flow control of PHY port
* [Bit 12:8 ]FORCE_DPX  - Flow Control Status of port 0~4
* [Bit 5    ]XTAL_COMP  - Crystal rate compensation
* [Bit 4:0  ]FORCE_SPD  - Port4 ~ Port0 Speed:

#### 1011008C-SOCPC(SoC Port Control)

* Bit25 - CRC_PADDING : CRC padding from CPU
* Bit 24:23 - CPU_SELECTION CPU Selection
    * 00b: Port 6
    * 01b: Port 0
    * 10b: Port 4
    * 11b: Port 5

#### 101100C8-FPA1(Force P5P6 Ability)

#### 101100A4-LEDP0(LED Port0)
* [Bit3:0]P0_LED - Port LED state, default=link/activity
    * 0000: link
    * 0001: 100M speed
    * 0010: duplex
    * 0011: activity
    * 0100: collision
    * 0101: link/activity
    * 0110: duplex/collsion
    * 0111: 10M speed/activity
    * 1000: 100M speed/activity
    * 1011: off
    * 1100: on
    * 1010: blink

#### 101100A8-LEDP1(LED Port1)
#### 101100AC-LEDP2(LED Port2)
#### 101100B0-LEDP3(LED Port3)
#### 101100B4-LEDP4(LED Port4)