# openwrt - MTD

---
title: "OpenWrt - MTD"
author: "hnhkj@163.com"
date: "2016-6-12"
output: html_document
---

源文档：<https://wiki.openwrt.org/doc/techref/mtd>

This page is a Work In Progress. The goal is to make it similar to opkg and then link to it as often as possible

这页是一个正在进行的工作。目标是使它类似于opkg，并且尽可能地连接它。

## MTD-Memory Technology Device//内存支持设备

mtd is a utility we use to write to an MTD (Memory Technology Device). Please read the Notes to learn more. `cd /tmp wget http://www.example.org/original_firmware.bin mtd -r write /tmp/original_firmware.bin firmware`

mtd是一个实用的程序，我们用它来写到一个MTD（Memory Technology Device）设备。请度note文档学习多的内容。`cd /tmp wget http://www.example.org/original_firmware.bin mtd -r write /tmp/original_firmware.bin firmware`

#### Invocation //启用

```
Usage: mtd [<options> ...] <command> [<arguments> ...] <device>[:<device>...]
```

#### Writing to MTD//写MTD

|unlock <dev> | unlock the device |
|-------------|-------------------|
|refresh `<dev>`|refresh mtd partition|
|erase `<dev>`|erase all data on device|
|write `<imagefile>|`\- | write <imagefile> (use - for stdin) to device |
|jffs2write `<file>` |append <file> to the jffs2 partition on the device|
|fixtrx `<dev>`|fix the checksum in a trx header on first boot|

#### Options//控制

|-q | quiet mode (once: no [w] on writing, twice: no status messages)|
|------------------------------|----------------------------------------------|
|-n | write without first erasing the blocks|
|-r | reboot after successful command |
|-f | force write without trx checks |
|-e <device> | erase <device> before executing the command |
|-d <name>   | directory for jffs2write, defaults to "tmp" |
|-j <name>  | integrate <file> into jffs2 data when writing an image |
|-o offset  | offset of the image header in the partition(for fixtrx) |
|-F <part>[:<size>[:<entrypoint>]][,<part>…] | alter the fis partition table to create new partitions replacing the partitions provided as argument to the write command (only valid together with the write command)|

## Examples//例子

To write linux.trx to mtd4 labeled as linux and reboot afterwards:

写linux.trx到mtd4，作为linux的标签，并且完成后重新启动。

```
# mtd -r write linux.trx linux
```

#### mtd vs dd

The differences between [dd](http://linux.die.net/man/1/dd) (disc dump) and mtd are … TODO

[dd](http://linux.die.net/man/1/dd) (disc dump) 和 mtd 不同 … TODO

## Notes//笔记

* [Flash memory - things to consider//注意事项](https://wiki.openwrt.org/doc/techref/flash)
* [MTD (Memory Technology Device//内存支持设备)](https://en.wikipedia.org/wiki/Memory%20Technology%20Device)
* [documentation on MTDs.//MTDs文档](http://www.linux-mtd.infradead.org/doc/general.html)
* [The differences between flash devices and block drives in a table//表中flash设备和block设备的区别](http://www.linux-mtd.infradead.org/faq/general.html#L_mtd_vs_hdd)
* To make it more clear, here is a small [comparison of MTD devices and block devices](http://lxr.free-electrons.com/source/Documentation/filesystems/ubifs.txt):为了更清晰，这里有一个MTD设备和block设备小的区分：
    * MTD devices represent flash devices and they consist of eraseblocks of rather large size, typically about 128KiB. Block devices consist of small blocks, typically 512 bytes. MTD devices support 3 main operations - read from some offset within an eraseblock, write to some offset within an eraseblock, and erase a whole eraseblock. Block devices support 2 main operations - read a whole block and write a whole block.//
    * The whole eraseblock has to be erased before it becomes possible to re-write its contents. Blocks may be just re-written.
    * Eraseblocks become worn out after some number of erase cycles - typically 100K-1G for SLC NAND and NOR flashes, and 1K-10K for MLC NAND flashes. Blocks do not have the wear-out property.
    * Eraseblocks may become bad (only on NAND flashes) and software should deal with this. Blocks on hard drives typically do not become bad, because hardware has mechanisms to substitute bad blocks, at least in modern LBA disks.
* Sometimes flash memory uses FTL: [Raw Flash vs. FTL (Flash Translation Layer)](http://www.linux-mtd.infradead.org/doc/ubifs.html#L_raw_vs_ftl)//一些时候，flash内存使用(FTLFlash Translation Layer)：
* Although most flashes on the commodity hardware have FTL, there are systems which have bare flashes and do not use FTL! Those are mostly various handheld devices and embedded systems//虽然大多数flash硬件商品上有FTL，但是，仍有一些系统使用裸Flash，不使用FTL！这些大多是各种的手持设别和嵌入式系统。

