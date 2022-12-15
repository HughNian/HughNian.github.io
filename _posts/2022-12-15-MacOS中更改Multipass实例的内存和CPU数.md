---
layout:     post
title:      微服务的限流与熔断
subtitle:   限流，熔断
date:       2022-12-15
author:     hughnian
header-img: https://picsum.photos/1024/768
catalog: true
tags:
- Multipass
- 虚拟机
- MacOs
---

## 操作
```cgo
# 先停止 multipassd 服务
sudo launchctl unload /Library/LaunchDaemons/com.canonical.multipassd.plist

# 修改配置文件
sudo su

vim /var/root/Library/Application\ Support/multipassd/qemu/multipassd-vm-instances.json

# 再启动 multipassd
sudo launchctl load /Library/LaunchDaemons/com.canonical.multipassd.plist

```

## 配置文件格式  
```cgo
{
    "primary": {
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