# Docker


## Docker 创建私有库

#### harbor

参考文档 <https://www.cnblogs.com/xiao987334176/p/11326467.html>

#### Registry

参考文档<https://www.cnblogs.com/gcgc/p/10489385.html>

```
docker pull registry:2
docker run -d -v /opt/registry:/var/lib/registry -p 5000:5000 --name myregistry registry:2
```

```
docker run -d --name=MyRegistry -p 5000:5000 -v /opt/data/registry:/tmp/registry registry:2.2
```

访问 http://127.0.0.1:5000/v2/_catalog 查看私有仓库目录，可以看到刚上传的镜像了：


简介


在 Docker 中，当我们执行 docker pull xxx 的时候 ，它实际上是从 registry.hub.docker.com 这个地址去查找，这就是Docker公司为我们提供的公共仓库。在工作中，我们不可能把企业项目push到公有仓库进行管理。所以为了更好的管理镜像，Docker不仅提供了一个中央仓库，同时也允许我们搭建本地私有仓库。这一篇介绍registry、harbor两种私有仓库搭建。

registry 的搭建

搭建

Docker 官方提供了一个搭建私有仓库的镜像 registry ，只需把镜像下载下来，运行容器并暴露5000端口，就可以使用了。

```
docker pull registry:2
docker run -d -v /opt/registry:/var/lib/registry -p 5000:5000 --name myregistry registry:2
```

Registry服务默认会将上传的镜像保存在容器的/var/lib/registry，我们将主机的/opt/registry目录挂载到该目录，即可实现将镜像保存到主机的/opt/registry目录了。

浏览器访问http://127.0.0.1:5000/v2，出现下面情况说明registry运行正常。


验证

现在通过push镜像到registry来验证一下。

查看本地镜像：

```
ubuntu@ubuntu-B150M-VP:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
<none>              <none>              bb782eba876d        16 hours ago        3.62GB
registry            2                   678dfa38fcfa        3 weeks ago         26.2MB
ubuntu              18.04               2c047404e52d        6 weeks ago         63.3MB
ubuntu              14.04               df043b4f0cf1        3 months ago        197MB

```

要通过docker tag将该镜像标志为要推送到私有仓库：

```
ubuntu@ubuntu-B150M-VP:~$ docker tag ubuntu:18.04 localhost:5000/ubuntu:18.04
```

通过 docker push 命令将 nginx 镜像 push到私有仓库中：

```
ubuntu@ubuntu-B150M-VP:~$ docker push localhost:5000/ubuntu:18.04
```

访问 http://127.0.0.1:5000/v2/_catalog 查看私有仓库目录，可以看到刚上传的镜像了：


下载私有仓库的镜像，使用如下命令：

```
docker pull localhost:5000/镜像名:版本号
例如
docker pull localhost:5000/nginx:latest
```

<https://www.cnblogs.com/huanchupkblog/p/10843800.html>


## 问题

#### docker login

```
ubuntu@ubuntu-B150M-VP:~$ docker login 192.168.0.169
Username: huang
Password: 
Error response from daemon: Get http://192.168.0.169/v2/: Get http://192.168.0.101/service/token?account=huang&client_id=docker&offline_token=true&service=harbor-registry: dial tcp 192.168.0.101:80: connect: no route to host
```

```
1 # cd /home/ubuntu/docker/harbor
2 # docker-comppose down
3 # vi harbor.cfg
4 hostname = xxx.xxx.xxx.xxx
5 # ./prepare
6 # docker-compose up -d
```

