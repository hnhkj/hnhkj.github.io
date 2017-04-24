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

* 问题1：Ubuntu切换默认sh为bash或者dash

```
$ sudo dpkg-reconfigure dash
```

选择`no`.