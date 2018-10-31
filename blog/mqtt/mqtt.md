# MQTT


## 服务器apache-apollo

<https://activemq.apache.org/apollo/>


#### 安装到ubuntu

1. 根据你的系统[下载](https://activemq.apache.org/apollo/download.html)合适的Apollo distribution.我才用的是ubuntu，下载的是<http://mirror.bit.edu.cn/apache/activemq/activemq-apollo/1.7.1/apache-apollo-1.7.1-unix-distro.tar.gz>
2.　解压`tar -zxvf apache-apollo-1.7.1-unix-distro.tar.gz`到制定目录.我下载的是下面的目录。

```
$ mkdir ~/mqtt
$ mv apache-apollo-1.7.1-unix-distro.tar.gz ~/mqtt
$ cd ~/mqtt
$ tar -zxvf apache-apollo-1.7.1-unix-distro.tar.gz
```

#### 创建一个Broker实例


根据官网的建议，它建议不要在apache-apollo-1.7.1目录下创建实例,以免影响将来的升级.我们将实例创建在/var/lib目录下.

```
$ cd /var/lib
$ sudo ~/mqtt/apache-apollo-1.7.1/bin/apollo create mybroker
[sudo] password for ubuntu: 
Creating apollo instance at: mybroker
Generating ssl keystore...

Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore keystore -destkeystore keystore -deststoretype pkcs12".

You can now start the broker by executing: 

   "/var/lib/mybroker/bin/apollo-broker" run

Or you can setup the broker as system service and run it in the background:

   sudo ln -s "/var/lib/mybroker/bin/apollo-broker-service" /etc/init.d/
   /etc/init.d/apollo-broker-service start

```

创建成功后，会创建一个mybroker目录.

```
ubuntu@ubuntu-Founder-PC:/var/lib$ ls mybroker/
bin  data  etc  log  tmp
```

A broker instance directory will contain the following sub directories:

    bin: holds execution scripts associated with this instance.
    etc: hold the instance configuration files
    data: holds the data files used for storing persistent messages
    log: holds rotating log files
    tmp: holds temporary files that are safe to delete between broker runs

At this point you may want to adjust the default configuration located in the etc directory.



#### 运行一个Broker实例

```
$ sudo /var/lib/mybroker/bin/apollo-broker run
```

## 参考链接

* １个开源的MQTT软件，支持个个版本的MQTT.<http://www.eclipse.org/paho/>

* MQTT的官网见：http://mqtt.org/。其中http://mqtt.org/software里面提供了官方推荐的各种服务器和客户端使用的各种语言版本的API。

* MQTT——服务器搭建（一）<https://www.cnblogs.com/chenrunlin/p/5090916.html?utm_source=tuicool&utm_medium=referral>
