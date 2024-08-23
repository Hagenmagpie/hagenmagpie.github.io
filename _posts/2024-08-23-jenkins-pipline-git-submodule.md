---
title: Jenkins中pipline中使用git submodule 方式拉取代码
published: true
---

# Jenkins中pipline中使用git submodule 方式拉取代码,如果在不配置的情况下会提示没有权限：

```

stage('clone source') {
    steps {
        git branch: '1.0', credentialsId: 'ops_jenkins', url: 'git@git.hagenmagpie.com:framework/hagenmagpie-cloud.git'
        sh label: '', script: 'git submodule update --init --recursive'
    }
}

```

会报错

```

+ git submodule update --init --recursive

```

解决方法：

可以通过把打包服务器中的 /root/.ssh/id_rsa.pub  内容 添加到你Gitlab服务器的`User Settings` - `SSH Keys` 中，前提是你有下载代码的权限。


