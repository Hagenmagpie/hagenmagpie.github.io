---
title: docker镜像拉取方法
published: true
---

最近docker镜像仓库被封掉了，如果系统架构还在使用docker，在未找到更好方法前还是需要使用镜像的。

在本地搭建harbor仓库。

# 1、通过科学上网方式把镜像拉取到本地

通过科学上网方式把镜像拉取到本地，然后再推送到本地仓库。

如果你恰巧是Mac电脑作为工作机器，默认下载的是arm架构的容器。在amd架构linux服务器中是无法运行的。

可通过下面方法拉取指定架构的docker镜像：

## 1.1 通过命令设置docker平台架构

```
export DOCKER_DEFAULT_PLATFORM=linux/amd64

docker pull ubuntu:20.04
```


## 1.2 直接在拉取命令中增加参数

```
docker pull --platform=linux/amd64 ubuntu:20.04
```

# 2、通过配置docker代理拉取

## 2.1 修改 daemon.json ( 推荐 )

如果仅仅是使用 Docker Hub 官方镜像,可以将本站加入到 daemon.json 文件中,那么可以直接使用正常的官方命令来拉取镜像或启动容器,系统会自动使用本站代理服务,而不会有上述镜像名的问题.参考添加以下信息,或者查看 这篇文章 .

```
{ 
    "registry-mirrors": [ "https://dockerproxy.net" ],
    "log-driver":"json-file",
    "log-opts": {"max-size":"300m", "max-file":"3"} 
}
```


## 2.2 常规镜像代理
官方命令：docker pull stilleshan/frpc:latest
代理命令：docker pull dockerproxy.net/stilleshan/frpc:latest


参考 https://dockerproxy.net/docs

# 3、 通过设置服务器代理

如果你的工作电脑可以科学上网 比如ip是 10.0.32.1 ，网络与linux服务器可以相互联通，在linux服务器上执行下面命令，可以通过你的工作电脑网络进行下载

```
export https_proxy=http://10.0.32.1:7890 http_proxy=http://10.0.32.1:7890 all_proxy=socks5://10.0.32.1:7890
```