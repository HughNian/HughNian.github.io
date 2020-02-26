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
`scope launch`可以这样使用`scope launch {OPTIONS} {PEERS}`,`scope help`命令可以看到具体的`{OPTIONS} {PEERS}`。具体的如下
```php
Launch options:
Usage of /home/weave/scope:
  -app-only
        Only run the app.
  -app.aws.create.tables
        Create the tables in DynamoDB
  -app.basicAuth
        Enable basic authentication for app
  -app.basicAuth.password string
        Password for basic authentication (default "admin")
  -app.basicAuth.username string
        Username for basic authentication (default "admin")
  -app.billing.default-publish-interval duration
        default publish interval to assume for reports (default 3s)
  -app.billing.enabled
        enable emitting billing info
  -app.block.profile.rate int
        If more than 0, enable block profiling. The profiler aims to sample an average of one blocking event per rate nanoseconds spent blocked.
  -app.collector string
        Collector to use (local, dynamodb, or file/directory) (default "local")
  -app.collector.s3 string
        S3 URL to use (when collector is dynamodb) (default "local")
  -app.consul.inf string
        The interface who's address I should advertise myself under in consul
  -app.container-label-filter value
        Add container label-based view filter, specified as title:label. Multiple flags are accepted. Example: --app.container-label-filter='Database Containers:role=db'
  -app.container-label-filter-exclude value
        Add container label-based view filter that excludes containers with the given label, specified as title:label. Multiple flags are accepted. Example: --app.container-label-filter-exclude='Database Containers:role=db'
  -app.container.name string
        Name of this container (to lookup container ID) (default "weavescope")
  -app.control.router string
        Control router to use (local or sqs) (default "local")
  -app.control.rpctimeout duration
        Timeout for control RPC (default 1m0s)
  -app.docker string
        Overwrite location of docker endpoint (to lookup container ID) (default "$DOCKER_HOST")
  -app.externalUI
        Point to externally hosted static UI assets
  -app.http.address string
        webserver listen address (default ":4040")
  -app.log.http
        Log individual HTTP requests
  -app.log.httpHeaders
        Log HTTP headers. Needs app.log.http to be enabled.
  -app.log.level string
        logging threshold level: debug|info|warn|error|fatal|panic (default "info")
  -app.log.prefix string
        prefix for each log line (default "<app>")
  -app.max-topology-nodes int
        drop topologies with more than this many nodes (0 to disable) (default 10000)
  -app.memcached.compression int
        How much to compress reports stored in memcached. (default -1)
  -app.memcached.expiration duration
        How long reports stay in the memcache. (default 30s)
  -app.memcached.hostname string
        Hostname for memcached service to use when caching reports.  If empty, no memcached will be used.
  -app.memcached.service string
        SRV service used to discover memcache servers. (default "memcached")
  -app.memcached.timeout duration
        Maximum time to wait before giving up on memcached requests. (default 100ms)
  -app.metrics-graph string
        Enable extended metrics graph by providing a templated URL (supports :instanceID and :query). Example: --app.metrics-graph=/prom/:instanceID/notebook/new
  -app.nats string
        Hostname for NATS service to use for shortcut reports.  If empty, shortcut reporting will be disabled.
  -app.pipe.router string
        Pipe router to use (local) (default "local")
  -app.service-name string
        The name for this service which should be reported in instrumentation (default "app")
  -app.stopTimeout duration
        How long to wait for http requests to finish when shutting down (default 5s)
  -app.userid.header string
        HTTP header to use as userid
  -app.weave.addr string
        Address on which to contact WeaveDNS (default "http://127.0.0.1:6784")
  -app.weave.hostname string
        Hostname to advertise in WeaveDNS
  -app.window duration
        window (default 15s)
  -billing.ingester string
        points to the billing ingester sidecar (should be on localhost) (default "localhost:24225")
  -billing.max-buffered-events int
        Maximum number of billing events to buffer in memory (default 1024)
  -billing.retry-delay duration
        How often to retry sending events to the billing ingester. (default 500ms)
  -debug
        Force debug logging.
  -dry-run
        Don't start scope, just parse the arguments.  For internal use only.
  -mode string
        For internal use. (default "help")
  -no-app
        Don't run the app.
  -no-probe
        Don't run the probe.
  -probe-only
        Only run the probe.
  -probe.basicAuth
        Use basic authentication to authenticate with app
  -probe.basicAuth.password string
        Password for basic authentication (default "admin")
  -probe.basicAuth.username string
        Username for basic authentication (default "admin")
  -probe.conntrack
        also use conntrack to track connections (default true)
  -probe.conntrack.buffersize int
        conntrack buffer size (default 4194304)
  -probe.cri
        collect CRI-related attributes for processes
  -probe.cri.endpoint string
        The endpoint to connect to the CRI (default "unix///var/run/dockershim.sock")
  -probe.docker
        collect Docker-related attributes for processes
  -probe.docker.bridge string
        the docker bridge name (default "docker0")
  -probe.docker.interval duration
        how often to update Docker attributes (default 10s)
  -probe.ebpf.connections
        enable connection tracking with eBPF (default true)
  -probe.ecs
        Collect ecs-related attributes for containers on this node
  -probe.ecs.cache.expiry duration
        How long to keep cached ECS info (default 1h0m0s)
  -probe.ecs.cache.size int
        Max size of cached info for each ECS cluster (default 1048576)
  -probe.ecs.cluster.region string
        ECS Cluster Region
  -probe.full-report-every int
        publish full report every N times, deltas in between. Make sure N < (app.window / probe.publish.interval) (default 3)
  -probe.http.listen string
        listen address for HTTP profiling and instrumentation server
  -probe.insecure
        (SSL) explicitly allow "insecure" SSL connections and transfers
  -probe.kubernetes
        collect kubernetes-related attributes for containers
  -probe.kubernetes.api string
        The address and port of the Kubernetes API server (deprecated in favor of equivalent probe.kubernetes.server)
  -probe.kubernetes.certificate-authority string
        Path to a cert. file for the certificate authority
  -probe.kubernetes.client-certificate string
        Path to a client certificate file for TLS
  -probe.kubernetes.client-key string
        Path to a client key file for TLS
  -probe.kubernetes.cluster string
        The name of the kubeconfig cluster to use
  -probe.kubernetes.context string
        The name of the kubeconfig context to use
  -probe.kubernetes.insecure-skip-tls-verify
        If true, the server's certificate will not be checked for validity. This will make your HTTPS connections insecure
  -probe.kubernetes.kubeconfig string
        Path to the kubeconfig file to use
  -probe.kubernetes.kubelet-port uint
        Node-local TCP port for contacting kubelet (zero to disable) (default 10255)
  -probe.kubernetes.node-name string
        Name of this node, for filtering pods
  -probe.kubernetes.password string
        Password for basic authentication to the API server
  -probe.kubernetes.role string
        host, cluster or blank for everything
  -probe.kubernetes.server string
        The address and port of the Kubernetes API server
  -probe.kubernetes.token string
        Bearer token for authentication to the API server
  -probe.kubernetes.user string
        The name of the kubeconfig user to use
  -probe.kubernetes.username string
        Username for basic authentication to the API server
  -probe.log.level string
        logging threshold level: debug|info|warn|error|fatal|panic (default "info")
  -probe.log.prefix string
        prefix for each log line (default "<probe>")
  -probe.no-controls
        Disable controls (e.g. start/stop containers, terminals, logs ...)
  -probe.omit.cmd-args
        Disable collection of command-line arguments
  -probe.omit.env-vars
        Disable collection of environment variables (default true)
  -probe.plugins.root string
        Root directory to search for plugins (disable plugins if blank) (default "/var/run/scope/plugins")
  -probe.proc.root string
        location of the proc filesystem (default "/proc")
  -probe.proc.spy
        associate endpoints with processes (needs root) (default true)
  -probe.processes
        produce process topology & include procspied connections (default true)
  -probe.publish.interval duration
        publish (output) interval (default 3s)
  -probe.publish.stdout
        Print reports on stdout instead of sending to app, for debugging
  -probe.resolver string
        IP address & port of resolver to use.  Default is to use system resolver.
  -probe.spy.interval duration
        spy (scan) interval (default 1s)
  -probe.token string
        Token to authenticate with cloud.weave.works
  -probe.weave.addr string
        IP address & port of the Weave router (default "127.0.0.1:6784")
  -probe.weave.hostname string
        Hostname to lookup in WeaveDNS
  -service-token string
        Token to authenticate with cloud.weave.works
  -weave
        Enable Weave Net integrations. (default true)
  -weave.hostname string
        Hostname to advertise/lookup in WeaveDNS (default "scope.weave.local.")
```

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
