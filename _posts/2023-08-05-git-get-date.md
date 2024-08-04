---
title: Git按时间获取代码
published: true
---

如果Git未打tag或分支，按时间获取代码步骤如下：


- 下载目标代码，通过下面命令找到该时间之前的最后一次提交记录

git rev-list -n 1 --before="2024-06-25 16:40" [分支号]

- 检出该次提交的代码

git checkout abcdef1234567890abcdef1234567890abcdef12

- 创建一个新的分支

git checkout -b state-on-2023-08-05


```

git rev-list -n 1 --before="2024-06-25 16:40" 1.0.x

git checkout 978ff365d9efadde700ddd7c39d4f9918b183e7e

git checkout -b state-on-2024-06-25-16-40


```

