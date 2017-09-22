# MiniDLNA

---
title: "OpenWrt - MiniDLNA"
author: "hnhkj@163.com"
date: "2016-6-13"
output: html_document
---

<https://wiki.openwrt.org/doc/uci/minidlna>

## MiniDLNA

MiniDLNA is a lightweight DLNA/UPnP media server. The MiniDNLA daemon serves media files (music, pictures, and video) to clients on a network.

MiniDLNA是一个轻量级的DLNA/UPnP媒体服务器。MiniDNLA守护进程服务媒体文件（music,pictures,和video）到网路上的客户端。

#### Installation//安装

````
root@OpenWrt:~# opkg update
root@OpenWrt:~# opkg install minidlna
````

#### Configuration //配置

edit file: /etc/minidlna.conf or /etc/config/minidlna.conf or /tmp/minidlna.conf depend on your miniDLNA Version

编辑文件：`/etc/minidlna.config`或`/etc/config/minidlna.conf`或`/tmp/minidlna.conf`，这依赖与你的miniDLNA版本。

```
#------------------------------------------------------#
# port for HTTP (descriptions, SOAP, media transfer) traffic
#------------------------------------------------------#
port=8200

#------------------------------------------------------#
# network interfaces to serve, comma delimited
#------------------------------------------------------#
network_interface=br-lan

#------------------------------------------------------#
# set this to the directory you want scanned.
# * if have multiple directories, you can have multiple media_dir= lines
# * if you want to restrict a media_dir to a specific content type, you
#   can prepend the type, followed by a comma, to the directory:
#   + "A" for audio  (eg. media_dir=A,/home/jmaggard/Music)
#   + "V" for video  (eg. media_dir=V,/home/jmaggard/Videos)
#   + "P" for images (eg. media_dir=P,/home/jmaggard/Pictures)
#------------------------------------------------------#
# Directory of media is depend on your storage
#------------------------------------------------------#
media_dir=A,/mnt/sda1/music
media_dir=P,/mnt/sda1/picture
media_dir=V,/mnt/sda1/video
#------------------------------------------------------#
# set this if you want to customize the name that shows up on your clients
#------------------------------------------------------#
friendly_name=My DLNA Server

#------------------------------------------------------#
# set this if you would like to specify the directory where you want MiniDLNA to store its database and album art cache
#------------------------------------------------------#
db_dir=/mnt/sda1/minidlna/db

#------------------------------------------------------#
# set this if you would like to specify the directory where you want MiniDLNA to store its log file
#------------------------------------------------------#
log_dir=/mnt/sda1/minidlna/log

#------------------------------------------------------#
# this should be a list of file names to check for when searching for album art
# note: names should be delimited with a forward slash ("/")
#------------------------------------------------------#
album_art_names=Cover.jpg/cover.jpg/AlbumArtSmall.jpg/albumartsmall.jpg/AlbumArt.jpg/albumart.jpg/Album.jpg/album.jpg/Folder.jpg/folder.jpg/Thumb.jpg/thumb.jpg

#------------------------------------------------------#
# set this to no to disable inotify monitoring to automatically discover new files
# note: the default is yes
#------------------------------------------------------#
inotify=yes

#------------------------------------------------------#
# set this to yes to enable support for streaming .jpg and .mp3 files to a TiVo supporting HMO
#------------------------------------------------------#
enable_tivo=no

#------------------------------------------------------#
# set this to strictly adhere to DLNA standards.
# * This will allow server-side downscaling of very large JPEG images,
#   which may hurt JPEG serving performance on (at least) Sony DLNA products.
#------------------------------------------------------#
strict_dlna=no

#------------------------------------------------------#
# default presentation url is http address on port 80
#------------------------------------------------------#
presentation_url=http://192.168.1.1:8200/

#------------------------------------------------------#
# notify interval in seconds. default is 895 seconds.
#------------------------------------------------------#
notify_interval=900

#------------------------------------------------------#
# serial and model number the daemon will report to clients
# in its XML description
#------------------------------------------------------#
serial=12345678
model_number=1

#------------------------------------------------------#
# specify the path to the MiniSSDPd socket
#------------------------------------------------------#
#minissdpdsocket=/var/run/minissdpd.sock

#------------------------------------------------------#
# use different container as root of the tree
# possible values:
#   + "." - use standard container (this is the default)
#   + "B" - "Browse Directory"
#   + "M" - "Music"
#   + "V" - "Video"
#   + "P" - "Pictures"
# if you specify "B" and client device is audio-only then "Music/Folders" will be used as root
#------------------------------------------------------#
#root_container=.
```

#### Configuration (uci) //配置（uci)

This describes the configuration options in /etc/config/minidlna.

这个描述配置文件在`/etc/config/minidlna`。

```
This is just an initial dump of all the options I found, I am not certain about any other options or their actual meaning!
```

```
 I just guessed the Type entries. — hcc23 2013/02/21 02:02
```

|Name|Type|Required|Default |Description|
|-----|-------|----------|-----------|
|enabled|boolean|???|0| |
|port|string|???|8200|port for HTTP (descriptions, SOAP, media transfer) traffic|
|interface|string|???|br-lan|network interfaces to serve; comma (,) delimited friendly_name string no (none) set this if you want to customize the name that shows up on your clients |
|db_dir|string|no|/var/run/minidlna|set this if you would like to specify the directory where you want MiniDLNA to store its database and album art cache|
|log_dir|string|no|/var/log|set this if you would like to specify the directory where you want MiniDLNA to store its log file|
|inotify|boolean|no|1|set this to no to disable inotify monitoring to automatically discover new files|
|enable_tivo|boolean|no|0|set this to yes to enable support for streaming .jpg and .mp3 files to a TiVo supporting HMO|
|strict_dlna|boolean|???|0|strictly adhere to DLNA standards; This will allow server-side down-scaling of very large JPEG images, which may hurt JPEG serving performance on (at least) Sony DLNA products.|
|notify_interval|integer|no|895|notify interval in seconds|
|serial|integer|???|12345678|model number the daemon will report to clients in its XML description|
|model_number|integer|???|1|model number the daemon will report to clients in its XML description|
|root_container|.,B,M,V,P|no|.|container for the tree root; .: default, B:browse directory, M:music, V:video, P:pictures|
|media\_dir|string|???|/mnt|set this to the directory you want scanned;if have multiple directories, you can have multiple media_dir lines; if you want to restrict a media_dir to a specific content type, you can prepend the type, followed by a comma, to the directory: A for audio (eg. A,/home/jmaggard/Music)V for video, P for pictures; if you specify B and client device is audio-only then "Music/Folders" will be used as root|
|album\_art\_names|string|no|Cover.jpg<br>/cover.jpg<br>/AlbumArtSmall.jpg<br>/albumartsmall.jpg<br>/AlbumArt.jpg<br>/albumart.jpg<br>/Album.jpg<br>/album.jpg<br>/Folder.jpg<br>/folder.jpg|a list of file names to check when searching for album art; / delimited|
|presentation\_url|string|???||default presentation url is http address on port 80|
|minisdpdsocket|string|???||specify the path to the MiniSSDPd socket|


#### Execute Daemon //执行守护进程

```
root@OpenWrt:~# /etc/init.d/minidlna enable
root@OpenWrt:~# /etc/init.d/minidlna start
```

#### Done! //结束

miniDLNA server is ready to use。

miniDLNA服务已经可以使用了。

#### Notes //注意

MiniDLNA's inotify support may not work as expected when trying to watch on an overlayfs filesystem. In the case of extroot, e.g. you can watch on `/overlay/root/minidlna` instead of `/root/minidlna`.

MiniDLNA新的支持可能不像期望的那样工作，当试着观看一个voerlayfs文件系统的时候。至于extroot，例如。你能够察看`/overlay/root/minidlna`来替代`/root/minidlna`。

#### Reference://参考：

* Comparison of UPnP AV media servers
* https://wiki.archlinux.org/index.php/Minidlna
* http://wiki.ubuntuusers.de/MiniDLNA
* The package has uci-support since R31211.

## Example //例子

A video demonstration of how MiniDLNA 1.1.3-1 in the repositories can be installed on OpenWrt 14.07 Barrier Breaker: <https://www.youtube.com/watch?v=-vU2zOw6ga0>

一个video事例，讲述在repository中的MiniDLNA 1.1.3如何安装到OpenWrt 14.07 BarrierBreaker：<https://www.youtube.com/watch?v=-vU2zOw6ga0>

## Chaos Calmer Tips // Chaos Calmer小窍门

If you have a decent size music library, you will more than likely find building the minidlna database on your OpenWrt device extremely slow or impossible due to RAM constraints. The solution is to build the minidlna database on a Linux PC.

如何已有一个合适尺寸的music库，你将更多的发现在你的OpenWrt设备编译MiniDLNA非常的慢或者不一定是RAM限制。解决的方法是在linuxPC上编译MiniDLNA。

* Create a directory on your OpenWrt hard or flash drive to hold the minidlna log and database files.//在你的OpenWrt硬盘或Flash驱动器上创建一个目录，抓住MiniDLNA的Log和database文件
    * mkdir /PathToHardDrive/minidlna
* On the OpenWrt device, modify the following /etc/config/minidlna lines to put the minidlna database and log file on the hard or flash drive connected to your OpenWrt device.//在OpenWrt设备，修改`/etc/config/minidlna`，连到放置在连接到你的OpenWrt设备的硬盘或flash设备的MiniDLNA database和log文件。

```
option db_dir '/PathToHardDrive/minidlna'
option log_dir '/PathToHardDrive/minidlna'
option inotify '0'
list media_dir 'A,/PathToHardDrive/PathToMp3s'
```

* Create a minidlna configuration file on the hard or flash drive connected to your OpenWrt device//在连接到你的OpenWrt设备的硬盘或flash驱动创建一个MiniDLNA配置文件。
    * Put the following in file cp_minidlna_conf.sh.//放置下面内容到cp_minidlna_config.sh文件。

```
#!/bin/sh
 
. /lib/functions.sh
 
minidlna_cfg_append() {
    echo "$1" 
}
 
minidlna_cfg_addbool() {
    local cfg="$1"
    local key="$2"
    local def="$3"
    local val
 
    config_get_bool val "$cfg" "$key" "$def"
    [ "$val" -gt 0 ] && val="yes" || val="no"
    minidlna_cfg_append "$key=$val"
}
 
minidlna_cfg_addstr() {
    local cfg="$1"
    local key="$2"
    local def="$3"
    local val
 
    config_get val "$cfg" "$key" "$def"
    [ -n "$val" ] && minidlna_cfg_append "$key=$val"
}
 
minidlna_cfg_add_media_dir() {
    local val=$1
 
    minidlna_cfg_append "media_dir=$val"
}
 
minidlna_create_config() {
    local cfg=$1
    local port
    local interface
 
    config_get port $cfg port
    config_get interface $cfg interface
 
    [ -z "$interface" -o -t "$port" ] && return 1
 
    echo "# this file is generated automatically, don't edit" 
 
    minidlna_cfg_append "port=$port"
    minidlna_cfg_append "network_interface=$interface"
 
    minidlna_cfg_addstr $cfg friendly_name
    minidlna_cfg_addstr $cfg db_dir
    minidlna_cfg_addstr $cfg log_dir
    minidlna_cfg_addbool $cfg inotify '1'
    minidlna_cfg_addbool $cfg enable_tivo '0'
    minidlna_cfg_addbool $cfg strict_dlna '0'
    minidlna_cfg_addstr $cfg album_art_names
    minidlna_cfg_addstr $cfg presentation_url
    minidlna_cfg_addstr $cfg notify_interval '900'
    minidlna_cfg_addstr $cfg serial '12345678'
    minidlna_cfg_addstr $cfg model_number '1'
    minidlna_cfg_addstr $cfg minissdpsocket
    minidlna_cfg_addstr $cfg root_container '.'
    config_list_foreach "$cfg" "media_dir" minidlna_cfg_add_media_dir
 
    return 0
}
 
 
config_load 'minidlna'
 
minidlna_create_config config
```

**
    * `chmod +x cp_minidlna_conf.sh`
    * `./cp_minidlna_conf.sh > /PathToHardDrive/minidlna/minidlna.conf`
* Remove the hard or flash drive from the OpenWrt device and connect it to your Linux PC.//从OpenWrt设别移出硬盘或flash设备连接它到你的Linux PC。
* Build the minidlna database on a Linux PC with minidlna 1.1.4.//在你的LinuxPC用minidlna 1.1.4编译minidlna数据库。
    * `minidlnad -d -R -v -f /PathToHardDrive/minidlna/minidlna.conf`
* Remove the hard or flash drive from your Linux PC and reconnect to your OpenWrt device.//从你的LinuxPC上移出硬盘或flash设备，重新连接到你的OpenWrt设备。

Note://注意：

```
Ensure that the Linux PC portable hard or flash drive mount point is exactly the same as the OpenWrt hard or flash drive mount point. Otherwise, the OpenWrt minidlna will delete and rescan the database you generated on the PC since the paths don't match

确保Linux PC的便携式硬盘或Flash设备mount点和OpenWrt硬盘或Flash设备mount点相同。否则，OpenWrt minidlna将删除并且重新稍描数据库。因为你在PC产生的路径不匹配。
```

This method has been tested with an Openwrt device with 32MB of RAM and a music library of over 11,000 songs. You may also want to add swap if you are running low on memory.

这个方法用一台32MB的RAM和一个超过11,000首歌音乐库的Openwrt设备实现过。你可以增加swap如果你低内存运行。
