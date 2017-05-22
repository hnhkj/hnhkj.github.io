# Hi35xx 开发

## SDK安装

#### 交叉编译工具安装

```
$ tar -zxf Hi3518E_SDK_V1.0.3.0.tgz
$ cd Hi3518E_SDK_V1.0.3.0
$ ./sdk.unpack
$ cd osdrv/opensource/toolchain/arm-hisiv300-linux
$ sudo ./cross.install.v300
$ source /etc/profile
```

#### 编译

参考 osdrv/readme_cn.txt

## 问题

#### 问题1：Ubuntu切换默认sh为bash或者dash

```
$ sudo dpkg-reconfigure dash
```

选择`no`.

#### 问题2：mkimage没有安装

```
$ sudo apt-get install u-boot-tools
```

#### 问题3：mkyaffs2image编译不过

清除`*.o`，或`make clean`.

```
$ cd osdrv/tools/pc/mkyaffs2image/mkyaffs2image
$ make clean
```

<http://blog.csdn.net/ternence_hsu/article/details/53421814>
