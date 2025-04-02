---
title: Docker通过BUILDKIT提高build速度
published: true
---



在linux服务器上编译docker镜像时，尤其jenkins装到linux环境中，有没有发现默认编译docker耗时较久。

可以通过开启BUILDKIT引擎编译来提高速度。

`前提需要确保安装了 Docker 版本 18.09 或更高版本`

有以下几种开启方式：

- 单行命令执行 
```
DOCKER_BUILDKIT=1  docker build  xxx
```

- 设置环境变量
```
export DOCKER_BUILDKIT=1
docker build  xxx
```

- 设置daemon.json属性

```
vim /etc/docker/daemon.json
{
  "log-opts": {
    "max-size": "5m", // 日志文件的最大尺寸，单位为兆字节（MB）
    "max-file": "3" // 最多保留的日志文件数量
  },
  "exec-opts": ["native.cgroupdriver=systemd"], // 使用 systemd 作为 cgroup 驱动程序
  "insecure-registries": ["https://harbor.xxx.com"], // 不安全的镜像仓库地址
  "features": {
    "buildkit": true // 启用 BuildKit 特性，以提高构建性能
  },
  "registry-mirrors": [ "https://dockerproxy.net" ],
  "log-driver":"json-file",
  "log-opts": {"max-size":"300m", "max-file":"3"}
  
}
```

该种方式需要重启docker服务
```
systemctl restart docker

```


拉取镜像并推送到本地仓库,支持多平台拉取

```
docker buildx imagetools create \
  --tag harbor.hagenmagpie.com/jdk/maven:3-openjdk-11 \
  maven:3-openjdk-11
```