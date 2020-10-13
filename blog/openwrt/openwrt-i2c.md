# I2C

## LM75

#### how to get LM75 data (0x49)(1001001x)(1001_A2_A1_A0_X)

参考了widora的说明文档，注意LM75的地址0x49(A2=0,A1=0,A0=1)，大多数默认的为0x48(A2=0,A1=0,A0=0)。

```
root@ess:~# i2cdump -y 0 0x49 W
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f    0123456789abcdef
00: 1c 00 4b 00 ff ff ff ff 1c 00 4b 00 ff ff ff ff    ?.K.....?.K.....
10: 1c 00 4b 00 ff ff ff ff 1c 00 4b 00 ff ff ff ff    ?.K.....?.K.....
20: 1c 00 4b 00 ff ff ff ff 1c 00 4b 00 ff ff ff ff    ?.K.....?.K.....
30: 1c 00 4b 00 ff ff ff ff 1c 00 4b 00 ff ff ff ff    ?.K.....?.K.....
40: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
50: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
60: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
70: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
80: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
90: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
a0: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
b0: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
c0: 1c 20 4b 00 ff ff ff ff 1c 20 4b 00 ff ff ff ff    ? K.....? K.....
d0: 1c 20

i2cget -y 0 0x49 0 w
```



#### How to compile i2ctools

<https://blog.csdn.net/lx14884036/article/details/90643691?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param>

```
i2c-tools-3.1.0.tar.bz2资源链接：http://repository.timesys.com/buildsources/i/i2c-tools/i2c-tools-3.1.0/

OpenWrt版本：CHAOS CALMER (Bleeding Edge, r46386)

step1.

下载文件，解压

step2.

cd i2c-tools-3.1.0

make CC=~/trunk/staging_dir/toolchain-mipsel_24kec+dsp_gcc-4.8-linaro_musl-1.1.10/bin/mipsel-openwrt-linux-gcc

编译的时候会有warning，不用理会。

step3.

打开i2c-tools-3.1.0文件夹下面的tools文件夹，将里面的i2cdetect、i2cdump、i2cget、i2cset这四个文件拷贝到开发板bin文件夹下。

看到有的教程说编译之后还会出现一个libi2c.so*文件，但是我这并没有发现生成了这个文件。

step4.

在开发板中可以执行i2cdetect和i2cdump相关命令。

若提示Permission denied，可以执行以下命令再试：

chmod +x i2cdetect

chmod +x i2cdump

chmod +x i2cget

chmod +x i2cset
```

## reference

<https://widora.io/i2c>

```
I2C

固件内置了i2ctools软件包
1. 用i2cdetect -l 命令来列出所有I2C总线

root@Widora:/# i2cdetect -l
i2c-0   i2c          10000900.i2c                 I2C adapt

2. 用i2cdetect -F 0 来查看I2C 0号总线启用的功能

root@Widora:/# i2cdetect -F 0
Functionalities implemented by /dev/i2c-0:
I2C                              yes
SMBus Quick Command              yes
SMBus Send Byte                  yes
SMBus Receive Byte               yes
SMBus Write Byte                 yes
SMBus Read Byte                  yes
SMBus Write Word                 yes
SMBus Read Word                  yes
SMBus Process Call               yes
SMBus Block Write                yes
SMBus Block Read                 no
SMBus Block Process Call         no
SMBus PEC                        yes
I2C Block Write                  yes
I2C Block Read                   yes

3. 查看I2C 0号总线上挂载的设备

root@Widora:/# i2cdetect -y 0
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- UU -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- UU -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- 48 -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --

其中标识为UU的设备（地址分别为0x1a和0x34）表示内核已经加载了相应的驱动，而地址为0x48的I2C设备尚未被内核所驱动。
4. 在sysfs文件中可查看已被内核驱动的I2C设备的名称

root@Widora:/# ls /sys/bus/i2c/devices
0-001a  0-0034  i2c-0
root@Widora:/# cat /sys/bus/i2c/devices/0-001a/name
wm8960
root@Widora:/# cat /sys/bus/i2c/devices/0-0034/name
codec_wm8960

5. 用i2cdump -y 0 0x48 W 命令来读取地址为0x48的I2C设备(LM75温度传感器)所有寄存器中的内容

root@Widora:/# i2cdump -y 0 0x48 W
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f    0123456789abcdef
00: 1e 00 4b 00 ff ff ff ff 1e 00 4b 00 ff ff ff ff    ?.K.....?.K.....
10: 1e 00 4b 00 ff ff ff ff 1e 00 4b 00 ff ff ff ff    ?.K.....?.K.....
20: 1e 00 4b 00 ff ff ff ff 1e 00 4b 00 ff ff ff ff    ?.K.....?.K.....
30: 1e 00 4b 00 ff ff ff ff 1e 00 4b 00 ff ff ff ff    ?.K.....?.K.....
40: 1e 00 4b 00 ff ff ff ff 1e 00 4b 00 ff ff ff ff    ?.K.....?.K.....
50: 1e 00 4b 00 ff ff ff ff 1e 00 4b 00 ff ff ff ff    ?.K.....?.K.....
60: 1e 00 4b 00 ff ff ff ff 1e 00 4b 00 ff ff ff ff    ?.K.....?.K.....

6. 用i2cget -y 0 0x48 0 w 命令来读取地址为0x48的I2C设备0号寄存器中的内容
```