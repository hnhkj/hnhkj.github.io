# openwrt button

#### how to use button

#### modify dts

***Note: linux,code = <xxx>***

search from `linux/inlude/dt-bindings/input/input.h`
or `linux/inlcude/uapi/linux/input.h`

```sh
cat dts/LINKIT7688.dts
...
// linux,code = <211> search from `inlude/dt-bindings/input/input.h`

	gpio-keys-polled {
		compatible = "gpio-keys-polled";
		#address-cells = <1>;
		#size-cells = <0>;
		poll-interval = <20>;
		wps {
			label = "wps";
			gpios = <&gpio1 6 1>;			//GPIO38-WIFI Button
			linux,code = <0x211>;			// KEY_WPS_BUTTON
		};
		lvd {
			label = "lvd";
			gpios = <&gpio1 10 1>;			//GPIO42-S8(42-32=10)
			linux,code = <263>;				// KEY_BATTERY
		};
		btn_1 {
			label = "btn_1";
			gpios = <&gpio1 9 1>;			//GPIO41-S8(41-32=9)
			linux,code = <0x101>;			// BTN_1
		};
		btn_2 {
			label = "btn_2";
			gpios = <&gpio1 8 1>;			//GPIO40-S8(40-32=8)
			linux,code = <0x102>;			// BTN_2
		};
		btn_3 {
			label = "BTN_3";
			gpios = <&gpio1 7 1>;			//GPIO39-S9(39-32=7)
			linux,code = <0x103>;			// BTN_3
		};
	};
...
```

#### create button process script in `/etc/rc.button/`

```sh
root@ess:/etc/rc.button# ls
btn1      btn3      lvd       reset     wps
btn2      failsafe  power     rfkill
```

#### test hot-button

create `/etc/hotplug.d/button/buttons`

```sh
cat /etc/hotplug.d/button/buttons
#!/bin/sh
logger the button was $BUTTON and the action was $ACTION 
```
#### logread


```
Tue Sep  8 02:03:12 2020 user.notice root: BTN_7 pressed for 0 seconds
Tue Sep  8 02:03:12 2020 user.notice root: the button was BTN_7 and the action was released
Tue Sep  8 02:03:12 2020 user.notice root: the button was BTN_7 and the action was pressed
Tue Sep  8 02:03:14 2020 user.notice root: BTN_7 pressed for 2 seconds
Tue Sep  8 02:03:14 2020 user.notice root: the button was BTN_7 and the action was released
```