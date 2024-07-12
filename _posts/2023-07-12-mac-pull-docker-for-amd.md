---
title: Mac下拉取amd架构的docker容器
published: true
---

最近docker容器镜像仓库国内封掉了，如果系统架构还在使用docker，在未找到更好方法前还是需要下载镜像的。

目前可以通过自己电脑通过科学上网方式把镜像拉取到本地，然后再推送到本地仓库。

如果你恰巧是Mac电脑作为工作机器，默认下载的是arm架构的容器。在amd系统下的linux服务器中是服务运行的。


## 通过命令设置docker平台架构

```
export DOCKER_DEFAULT_PLATFORM=linux/amd64

docker pull ubuntu:20.04
```


## 直接在拉取命令中增加参数

```
docker pull --platform=linux/amd64 ubuntu:20.04
```

