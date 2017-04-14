# uboot

---
title: "LinkIt smart 7688 - uboot"
author: "hnhkj@163.com"
date: "2016年6月14日"
output: html_document
---

源代码地址：<https://github.com/MediaTek-Labs/linkit-smart-7688-uboot>

## uboot

Linkit-smart-7688-uboot是Mediatek公司的MT7688芯片的专用Uboot。该代码源自于`Ralink UBoot Version: 4.3.0.0`.


## Uboot编译

```
  $git clone https://github.com/MediaTek-Labs/linkit-smart-7688-uboot.git
  $cd linkit-smart-7688-uboot
  $sudo tar jxf buildroot-gcc342.tar.bz2 -C /opt/
  $make
```

## Uboot命令

```
MT7628 # ?
?       - alias for 'help'  // 'help'命令别名
bootm   - boot application image from memory  // 从内存启动应用image
cp      - memory copy  // 内存拷贝
crc32   - checksum calculation  // 交验和计算
erase   - erase SPI FLASH memory  // 擦除SPI Flash存储器
fatinfo - print information about filesystem  //打印文件系统信息
fatload - load binary file from a dos filesystem  // 从一个dos文件系统装载一个二进制文件
fatls   - list files in a directory (default /)  // 列出一个目录下的文件（默认根目录）
go      - start application at address 'addr'  // 在"addr"地址开始运行
help    - print online help  // 打印在线帮助
loadb   - load binary file over serial line (kermit mode)  // 基于串口线（kermit mode）装载二进制文件
md      - memory display  // 内存显示
mdio   - Ralink PHY register R/W command !!  // Ralink PHY寄存器读/写命令！！
mm      - memory modify (auto-incrementing)  // 内存修改（自动增加）
nm      - memory modify (constant address)  // 内存修改（指定地址）
printenv- print environment variables  // 打印环境变量
reset   - Perform RESET of the CPU  // 执行MCU复位
rf      - read/write rf register  // 读/写rf寄存器
saveenv - save environment variables to persistent storage  // 保存环境变量到
setenv  - set environment variables
spi     - spi command
tftpboot- boot image via network using TFTP protocol
usb     - USB sub-system
usbboot - boot from USB device
version - print monitor version
```

#### ? - `help`命令别名

#### bootm - 

