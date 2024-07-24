---
title: Mac下拉取amd架构的docker容器
published: true
---

最近docker镜像仓库被封掉了，如果系统架构还在使用docker，在未找到更好方法前还是需要使用镜像的。

在本地搭建harbor仓库。

通过科学上网方式把镜像拉取到本地，然后再推送到本地仓库。

如果你恰巧是Mac电脑作为工作机器，默认下载的是arm架构的容器。在amd架构linux服务器中是无法运行的。

可通过下面方法拉取指定架构的docker镜像：

## 通过命令设置docker平台架构

```
export DOCKER_DEFAULT_PLATFORM=linux/amd64

docker pull ubuntu:20.04
```


## 直接在拉取命令中增加参数

```
docker pull --platform=linux/amd64 ubuntu:20.04
```

