# Git server

---
title: "Openwrt-搭建一个Git服务器"
author: "huang Kongjun"
date: "2016-7-25"
output: html_document
---

## 简介

对于Git文件的管理，一直是一个比较困扰的问题。目前是流行的Github是一个不错的选择。但是默认是开源的，如果是一些不愿共享出来的文件代码，我们最好是搭建一个自己的服务器。在git官方网站有git服务器的搭建方法。

今天，我要说的是在局域网中通过openwrt搭建一个简单的git server。这样只要我的Openwrt设备是打开的，我的所有电脑就都可以git commit到这个设备。非常方便，非常简单。

## 方法

#### 1. 要有一台Openwrt，我采用的是LinktiSmart 7688。该设备支持ssh登录。

#### 2. 一个Micro-SD卡或U盘，用来保存文件。我使用的是一个16GB U盘，作为git服务器应该绰绰有余了。

#### 3. 在u盘中创建一个.git文件夹

```
# mkdir /Media/USB-A1/git/sample.git
```

#### 4. 初始化.git仓库

```
# sudo git init --bare /Media/USB-A1/git/sample.git
```

好了，现在你就可以远程克隆samples.git仓库了，可以实现提交和下载。

#### ubuntu下测试实现

```
$ git clone root@192.168.1.104:/Media/USB-A1/git/sample.git 
```

实现方法和在github上相同。不过这可以私用的奥。别人是访问不到到。


## 参考连接

<http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000>

<https://git-scm.com/book/zh/v1/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E9%83%A8%E7%BD%B2-Git>