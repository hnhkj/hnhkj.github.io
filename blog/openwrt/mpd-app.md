# OpenWrt - MPDroid实现手机控制OpenWRT播放音乐

---
title: "OpenWrt - MPDroid实现手机控制OpenWRT播放音乐"
author: "hnhkj@163.com"
date: "2016年7月1日"
output: html_document
---

## MPD 简介

<https://www.musicpd.org/>
<https://tnetter.wordpress.com/2015/03/14/install-mpd-di-openwrt/>

<https://www.musicpd.org/doc/user/>

## MPD OpenWrt实现

<http://bbs.qzxx.com/thread-13547-1-2.html>

#### 安装MPD到OpenWRT

```
# opkg update
# opkg install mpd-mini
```

#### 修改`/etc/mpd.conf`

```
music_directory   "/Media/SD-P1/music"
playlist_directory  "/Media/SD-P1/music/.mpd"
db_file     "/Media/SD-P1/music/.mpd/mpd.db"
log_file    "/Media/SD-P1/music/.mpd/mpd.log"
pid_file    "/Media/SD-P1/music/.mpd/mpd.pid"
state_file    "/Media/SD-P1/music/.mpd/mpd.state"
bind_to_address   "any"
port        "6600"

audio_output {
  type    "alsa"
  name    "MT7688"
  use_mmap  "yes"
  mixer_control "Playback"
}
```

如果有更多的配置需要，可以参考mpd.conf的例子文档或mpd网站的配置手册。

#### 按照配置创建目录

```
mkdir /Media/SD-P1/music/.mpd
```

#### 启动mpd服务

```
/etc/init.d/mpd restart
```


## mpc 应用 //命令行显示客户端

<http://linux.die.net/man/1/mpc>

mpc是mpd的一款前端程序。可以通过该程序对mpd进程进行控制。

#### 安装mpc

```
opkg install mpc
```

#### 测试mpc

* mpc update  //更新
* mpc play    //播放
* mpc stop   //停止
* mpc pause //暂停
* mpc prev  //前一首
* mpc playlist - Prints entire playlist //打印整个播放列表
* mpc repeat on  //循环播放
* mpc repeat off //停止循环播放

#### mpc调节音量

```
# mpc volume +1
```

## Android手机测试

#### 安装MPDroid

通过安卓市场安装MPDroid，或者通过网络搜索下载该文件，安装到你的Android手机，运行该程序。

#### 设置MPDroid

点击`sets`按钮配置`Defaut connnection settings`选项，选择正确的网络端口，然后返回。

#### OK

你现在可以通过MPDroid控制你的路由器播放音乐文件了。文件切换，音乐播放。一切都很爽。

有真相没图，自己努力会有惊喜。哈哈哈

## Window客户端 auremo

http://auremo.codeplex.com/

#### 参考文档

<http://mpd.wikia.com/wiki/Clients>