---
title: linux下多个节点ssh互相免密码登陆
categories: 
  - Linux
tags:
  - 编程语言
  - Linux
  - ssh
date: 2018-04-29
---

**假定需要节点gm-35-2和gm-35-18之间免密登陆**

1. 确认安装了ssh
2. 创建私钥和密钥 （id_rsa和 id_rsa.pub）
执行`ssh-keygen-t rsa` ，这样你进入 `~/.ssh`文件夹下就会看到 `id_rsa` 和`id_rsa.pub`两个文件。每个节点都执行一次
3. 在`gm-35-2`下，运行`cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`
4. `ssh gm-35-18 cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys` 将其他节点公匙拷贝到本节点
5. ` scp ~/.ssh/authorized_keys usrname@gm-35-18:~/.ssh/authorized_keys ` 将文件拷贝到其他节点

*网上说要将该文件权限改为600*