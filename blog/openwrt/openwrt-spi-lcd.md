# openwrt-spi-lcd

## How to install fbtft driver

#### download fbtft source code

```
cd chaos_calmer/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/linux-ramips_mt7688/linux-3.18.44/drivers/staging
git clone https://github.com/notro/fbtft --depth=1
```

#### modify Kconfig

```sh
cat chaos_calmer/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/linux-ramips_mt7688/linux-3.18.44/drivers/staging/Kconfig

...
source "drivers/staging/unisys/Kconfig"

source "drivers/staging/fbtft/Kconfig"

endif # STAGING
```

#### modify Makefile

```sh
cat chaos_calmer/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/linux-ramips_mt7688/linux-3.18.44/drivers/staging/Makefile

...
obj-$(CONFIG_UNISYSSPAR)	+= unisys/
obj-$(CONFIG_FB_TFT)		+= fbtft/
```

#### modify dts

modify LINKIT7688.dts and add the ili9341 device.

```sh
cat chaos_calmer/target/linux/ramips/dts/LINKIT7688.dts

...
	palmbus@10000000 {
		spi@b00 {
			status = "okay";

			pinctrl-names = "default";
			pinctrl-0 = <&spi_pins>, <&spi_cs1_pins>;

			m25p80@0 {
				#address-cells = <1>;
				#size-cells = <1>;
				compatible = "mx25l25635e";
				reg = <0 0>;
				linux,modalias = "m25p80", "mx25l25635e";
				spi-max-frequency = <40000000>;
				m25p,chunked-io = <31>;

				partition@0 {
					label = "u-boot";
					reg = <0x0 0x30000>;
					read-only;
				};

				partition@30000 {
					label = "u-boot-env";
					reg = <0x30000 0x10000>;
				};

				factory: partition@40000 {
					label = "factory";
					reg = <0x40000 0x10000>;
					//read-only;
				};

				partition@50000 {
					label = "firmware";
					reg = <0x50000 0x1fb0000>;
				};
			};

			ili9341@1 {
				#address-cells = <1>;
				#size-cells = <1>;
				status = "okay";
				compatible = "ilitek,ili9341";
				reg = <1 0>;
				spi-max-frequency = <32000000>;
				txbuflen = <16>;
				rotate = <0>;
				bgr;
				fps = <15>;
				buswidth = <8>;
				dc-gpios = <&gpio0 11 1>;
			};
		};

		i2c@900 {
			status = "okay";
		};
...
```

## FAQ

##### How to Rotate FBTFT LCD

modify rotate to <0>/<90>/<180>/<270>

```
            ili9341@1 {
                #address-cells = <1>;
                #size-cells = <1>;
                status = "okay";
                compatible = "ilitek,ili9341";
                reg = <1 0>;
                spi-max-frequency = <32000000>;
                txbuflen = <16>;
                rotate = <270>;
                bgr;
                fps = <15>;
                buswidth = <8>;
                dc-gpios = <&gpio0 11 1>;
            };
```

## reference

<https://widora.io/ips>

<https://www.amobbs.com/thread-5686620-1-1.html>