---
title: Linux服务器免密登录
published: true
---

日常中经常遇到通过命令行访问linux服务器的场景，通常用命令行工具用ssh命令进行连接。

但是每次连接都需要输入密码，这里记录一种方法，只需要输入一次密码即可。



## 1、生成公钥和私钥

如果上一步之前执行过，无需重复执行

```
ssh-keygen -t rsa
```

生产如下文件

![image-20240704125356890](./assets/image-20240704125356890.png)

## 2、上传公钥

```
ssh-copy-id root@1.14.120.224
```

本次需要输入密码。



上述步骤完成后，后面再通过ssh 连接该设备无需输入密码，在写shell 做自动化时该方法很好用。