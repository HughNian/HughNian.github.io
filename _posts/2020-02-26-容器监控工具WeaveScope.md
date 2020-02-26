---
layout:     post   				    
title:      容器监控工具WeaveScope
subtitle:   Docker监控，Kubernetes监控
date:       2020-02-26				
author:     hughnian				
header-img: img/docker.png
catalog: true 						
tags:							
    - Docker
    - Golang
    - 容器监控
    - 工具
---

最近一段时间整了一些docker容器，弄了一些基于docker的微服务通信，弄好一套服务系统之后，对于服务的性能，基础数据的监控就显的很重要，
不然就是两眼一抹黑了，要不就是维护成本很高，这些都不符合一个高性能服务系统😄。这里就介绍下我最近用的容器监控工具WeaveScope。这个工具不仅可以
有基础性能的数据监控，同时还可以在线cli的操作，除了Docker外，这个工具还可以监控Kubernetes集群，可以说相当强大。   

### 安装与运行
由于我用的是Docker，这里主要介绍这个工具在Docker上的使用。Kubernetes网上搜了下也是有很多的，大家可以自行了解🤭。
对于WeaveScope的安装相当简单，只要三行命令就可以了。官方的文档中是这样命令
```php
    sudo curl -L git.io/scope -o /usr/local/bin/scope
    sudo chmod a+x /usr/local/bin/scope
    scope launch
```
但是经过本人的实验，这个命令存在一些坑。第一个下载命令我下载下来的版本有问题，运行时会报错。第三个运行命令只是最基本的，不能很好的满足我的需求。
所以经过我的探索我建议使用如下命令
```php
    sudo curl -L https://github.com/weaveworks/scope/releases/download/latest_release/scope -o /usr/local/bin/scope
    sudo chmod a+x /usr/local/bin/scopesudo chmod a+x /usr/local/bin/scope
    scope launch -app.basicAuth -app.basicAuth.password 123456 -app.basicAuth.username user -probe.basicAuth -probe.basicAuth.password 123456 -probe.basicAuth.username user
```
- 第一个下载命令换成从它官方的github上下载最新版本。
- 第二个命令只是加权限都一样没啥区别。
- 第三个运行命令是有很多道道的，官方的命令是最基本的运行。我改进版的是可以支持用户密码登录的功能，因为毕竟容器的监控我们不希望任何人都可以看到的。这个东西我也是搜了很久在官方的issues中找到了答案
![](/img/weave1.png)
![](/img/weave2.png)
`scope launch`可以这样使用`scope launch {OPTIONS} {PEERS}`,`scope help`命令可以看到具体的`{OPTIONS} {PEERS}`。里面的东西很多，包括很多基础的设置，用户名和密码登录、端口设置等都可以通过这里来设置    

### 使用 Scope
成功启动scope后，可以在浏览器中方法http://ip:4040，如果端口没有修改默认是4040。之后输入用户名和密码进入监控首页    

![](/img/weave5.png)   

一般我们可以点击Container查看容器的情况，这里一般会列出你自己的容器和weave自带的容器，通过名字可以区别。点击相应容器可以对它进行
在线bash操作，或者重启，暂停，关闭容器。   

![](/img/weave3.png)
![](/img/weave4.png)   

点击Hosts按钮，可以对自己的主机进行监控和操作，当然也可以在线bash。  
   
![](/img/weave6.png)   

点击by image可以看到自己现在docker中所有的镜像。   

![](/img/weave7.png)   


好了先介绍这么多了，我也再慢慢体验研究中😄。
