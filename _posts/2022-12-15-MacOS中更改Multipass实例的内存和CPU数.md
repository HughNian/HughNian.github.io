---
layout:     post
title:      MacOS中更改Multipass实例的内存和CPU数
subtitle:   Multipass,轻量级虚拟机
date:       2022-12-15
author:     hughnian
header-img: https://picsum.photos/1024/768
catalog: true
tags:
- Multipass
- 虚拟机
- MacOs
---

## 介绍
**Multipass** 是一个轻量虚拟机管理器，是由 Ubuntu 运营公司 Canonical 所推出的开源项目。运行环境支持 Linux、Windows、macOS。
在不同的操作系统上，使用的是不同的虚拟化技术。在 Linux 上使用的是 KVM、Window 上使用 Hyper-V、macOS 中使用 HyperKit 以最小开销运行VM，支持在笔记本模拟小型云。 
Multipass有些非常方便的命令行操作虚拟机，甚至比图形化的vbox那些都方便。具体的操作大家自行google下。   

Multipass官网：[https://multipass.run/](https://multipass.run/)

## 修改操作
_注意_： 本操作是在你已经成功启动某一个Multipass虚拟机的前提下进行的操作。  

```shell
# 先停止 multipassd 服务
sudo launchctl unload /Library/LaunchDaemons/com.canonical.multipassd.plist

# 修改配置文件
sudo su

vim /var/root/Library/Application\ Support/multipassd/qemu/multipassd-vm-instances.json

# 再启动 multipassd
sudo launchctl load /Library/LaunchDaemons/com.canonical.multipassd.plist

```

## 配置文件格式
_注意_：如果是多个虚拟机，这里的会有多个对象信息。

```json
{
    "k8s-master": {
        "deleted": false,
        "disk_space": "10737418240",// 10G
        "mac_addr": "52:54:00:5d:ed:45",
        "mem_size": "4294967296",//4096M
        "metadata": {
        },
        "mounts": [
        ],
        "num_cores": 2,
        "ssh_username": "ubuntu",
        "state": 0
    }
}

```