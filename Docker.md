# Docker 常用命令

## 帮助命令

```shell
docker version  # 显示docker版本信息
docker info  # 显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help  # 帮助命令
```

帮助文档地址：https://docs.docker.com/reference/

## 镜像命令 

**docker images 查看本地所有主机上的镜像**

```shell
[root@izbp11tm4pvghonrtfzq17z docker]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
hello-world   latest    d1165f221234   3 weeks ago   13.3kB

# 解释
REPOSITORY 镜像的仓库源
TAG 镜像的标签
IMAGE ID 镜像的id
CREATED 镜像的创建时间
SIZE 镜像的大小

# 可选项
  -a, --all             Show all images (default hides intermediate images)
  -q, --quiet           Only show image IDs
```

**docker search 搜索镜像** 

```shell
[root@izbp11tm4pvghonrtfzq17z docker]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10680     [OK]       
mariadb                           MariaDB Server is a high performing open sou…   4011      [OK]  

# 可选项，通过收藏来过滤
  --filter=STARS=3000 搜索出来的镜像就是STARS大于3000的
  
[root@izbp11tm4pvghonrtfzq17z docker]# docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   10680     [OK]       
mariadb   MariaDB Server is a high performing open sou…   4011      [OK]  
```

**docker pull 下载镜像**

```shell
# 下载镜像 docker pull 镜像名[:tag]
[root@izbp11tm4pvghonrtfzq17z docker]# docker pull mysql
Using default tag: latest # 如果不写 tag，默认就是 latest
latest: Pulling from library/mysql
a076a628af6f: Pull complete # 分层下载，docker image的核心 联合文件系统
f6c208f3f991: Pull complete 
88a9455a9165: Pull complete 
406c9b8427c6: Pull complete 
7c88599c0b25: Pull complete 
25b5c6debdaf: Pull complete 
43a5816f1617: Pull complete 
1a8c919e89bf: Pull complete 
9f3cf4bd1a07: Pull complete 
80539cea118d: Pull complete 
201b3cad54ce: Pull complete 
944ba37e1c06: Pull complete 
Digest: sha256:feada149cb8ff54eade1336da7c1d080c4a1c7ed82b5e320efb5beebed85ae8c # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # 真实地址

# 等价于它
docker pull mysql
docker pull docker.io/library/mysql:latest

# 指定版本下载
[root@izbp11tm4pvghonrtfzq17z docker]# docker pull mysql:5.7
5.7: Pulling from library/mysql
a076a628af6f: Already exists 
f6c208f3f991: Already exists 
88a9455a9165: Already exists 
406c9b8427c6: Already exists 
7c88599c0b25: Already exists 
25b5c6debdaf: Already exists 
43a5816f1617: Already exists 
1831ac1245f4: Pull complete 
37677b8c1f79: Pull complete 
27e4ac3b0f6e: Pull complete 
7227baa8c445: Pull complete 
Digest: sha256:b3d1eff023f698cd433695c9506171f0d08a8f92a0c8063c1a4d9db9a55808df
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

**docker rmi 删除镜像**

```shell
[root@izbp11tm4pvghonrtfzq17z docker]# docker rmi -f 镜像id  # 删除指定的镜像
[root@izbp11tm4pvghonrtfzq17z docker]# docker rmi -f 镜像id 镜像id 镜像id  # 删除多个镜像
[root@izbp11tm4pvghonrtfzq17z docker]# docker rmi -f $(docker images -qa)  # 删除全部镜像
```

## 容器命令

说明：我们有了镜像才可以创建容器，linux，下载一个centos镜像来测试学习

```shell
docker pull centos
```

**新建容器并启动**

```shell
docker run [可选参数] image

# 参数说明
--name="Name" 容器名字 tomcat1 tomcat2，用来区分容器
-d 后台方式运行
-it 使用交互方式运行，进入容器查看内容
-p 指定容器的端口 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口（常用）
	-p 容器端口
	容器端口
-P 随机指定端口

# 测试，启动并进入容器
[root@izbp11tm4pvghonrtfzq17z docker]# docker run -it centos /bin/bash
[root@2c46534669b8 /]# ls 
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 从容器中退回主机
[root@2c46534669b8 /]# exit
exit
```

**列出所有运行的容器**

```shell
# docker ps 命令
  # 列出当前正在运行的容器
-a # 列出当前正在运行的容器+带出历史运行过的容器
-n=? # 显示最近创建的容器
-q # 只显示容器的编号

[root@izbp11tm4pvghonrtfzq17z docker]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@izbp11tm4pvghonrtfzq17z docker]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                     PORTS     NAMES
2c46534669b8   centos         "/bin/bash"   4 minutes ago   Exited (0) 2 minutes ago             loving_cohen
f8ee4e64568f   d1165f221234   "/hello"      2 hours ago     Exited (0) 2 hours ago               keen_black
094ee2c31714   d1165f221234   "/hello"      3 hours ago     Exited (0) 3 hours ago               inspiring_babbage

```

**退出容器**

```shell
exit # 直接容器停止并退出
Ctrl + p + q # 容器不停止退出
```

**删除容器**

```shell
docker rm 容器id # 删除指定的容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq) # 删除所有的容器
docker ps -a -q | xargs docker rm # 删除所有的容器
```

**启动和停止容器的操作**

```shell
docker start 容器id # 启动容器
docker restart 容器id # 重启容器
docker stop 容器id # 停止当前正在运行的容器
docker kill 容器id # 强制停止当前容器
```

## 常用其它命令

 **后台启动容器**

```shell
# 命令 docker run -d 镜像名
[root@izbp11tm4pvghonrtfzq17z /]# docker run -d centos

# 问题docker ps，发现 centos 停止
# 常见的坑，docker 容器使用后台运行，就必须要有一个前台进程，docker 发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止
```

**查看日志**

```shell
docker logs -f -t --tail 容器，没有日志

# 自己编写一段shell脚本
[root@izbp11tm4pvghonrtfzq17z /]# docker run -d centos /bin/sh -c "while true;do echo milla;sleep 2;done"

[root@izbp11tm4pvghonrtfzq17z /]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
4fbb65c2f215   centos    "/bin/sh -c 'while t…"   5 seconds ago   Up 5 seconds             bold_banach

# 查看日志
-tf	# 显示日志
--tail number # 要显示日志条数
[root@izbp11tm4pvghonrtfzq17z /]# docker logs -tf --tail 10 4fbb65c2f215
[root@izbp11tm4pvghonrtfzq17z /]# docker logs -tf 4fbb65c2f215
```

**查看容器中的进程信息**

```shell
# 命令 docker top 容器id
[root@izbp11tm4pvghonrtfzq17z /]# docker top 4fbb65c2f215
UID                 PID                 PPID                C                   STIME               TTY     
root                8294                8255                0                   22:48               ?       
root                8805                8294                0                   23:00               ?  
```

**查看镜像的元数据**

```shell
# 命令 docker inspect 容器id
[root@izbp11tm4pvghonrtfzq17z /]# docker inspect 4fbb65c2f215
[
    {
        "Id": "4fbb65c2f215288029d27376c215de7255ba72044ffbbbdd9668de4f19b1cff9",
        "Created": "2021-04-01T14:48:13.251805266Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo milla;sleep 2;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 8294,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-04-01T14:48:13.634451808Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/4fbb65c2f215288029d27376c215de7255ba72044ffbbbdd9668de4f19b1cff9/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/4fbb65c2f215288029d27376c215de7255ba72044ffbbbdd9668de4f19b1cff9/hostname",
        "HostsPath": "/var/lib/docker/containers/4fbb65c2f215288029d27376c215de7255ba72044ffbbbdd9668de4f19b1cff9/hosts",
        "LogPath": "/var/lib/docker/containers/4fbb65c2f215288029d27376c215de7255ba72044ffbbbdd9668de4f19b1cff9/4fbb65c2f215288029d27376c215de7255ba72044ffbbbdd9668de4f19b1cff9-json.log",
        "Name": "/bold_banach",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/5856cd6e6cac3df34272c29e926f99438c13d80f43a4058d74418d0853fe2d4f-init/diff:/var/lib/docker/overlay2/fe88001470606eaa1b94400a5b658905f581ab7c289ec8cecf5bbd4176999d6b/diff",
                "MergedDir": "/var/lib/docker/overlay2/5856cd6e6cac3df34272c29e926f99438c13d80f43a4058d74418d0853fe2d4f/merged",
                "UpperDir": "/var/lib/docker/overlay2/5856cd6e6cac3df34272c29e926f99438c13d80f43a4058d74418d0853fe2d4f/diff",
                "WorkDir": "/var/lib/docker/overlay2/5856cd6e6cac3df34272c29e926f99438c13d80f43a4058d74418d0853fe2d4f/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "4fbb65c2f215",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo milla;sleep 2;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "c34acd28a9404fb62a2e1dfa6fce49888f31a395ec27cf9c092894dd4b99f90d",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/c34acd28a940",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "40045d4858fcd339b17dd03135b055cf4a8bd08a9b48f1e3860fb3bf9be015f8",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "e7a0064379362e134aced403c3e2bc73db70bd98a2830787f45a158368323502",
                    "EndpointID": "40045d4858fcd339b17dd03135b055cf4a8bd08a9b48f1e3860fb3bf9be015f8",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

**进入当前正在运行的容器**

```shell
# 我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

# 命令 方式一
docker exec -it 容器it bashShell

# 测试
[root@izbp11tm4pvghonrtfzq17z /]# docker exec -it 4fbb65c2f215 /bin/bash
[root@4fbb65c2f215 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@4fbb65c2f215 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 14:48 ?        00:00:00 /bin/sh -c while true;do echo milla;sleep 2;done
root       692     0  0 15:11 pts/0    00:00:00 /bin/bash
root       711     1  0 15:11 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 2
root       712   692  0 15:11 pts/0    00:00:00 ps -ef
[root@4fbb65c2f215 /]# 

# 方式二
docker attach 容器id
# 测试
[root@izbp11tm4pvghonrtfzq17z /]# docker attach 4fbb65c2f215
正在执行当前的代码...

# docker exec # 进入容器后开启一个新的终端，可以在里面操作（常用）
# docker attach # 进入容器正在执行的终端，不会启动新的进程
```

**从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径 目的主机路径
[root@izbp11tm4pvghonrtfzq17z home]# docker cp bcdf87e20f3b:/home/test.java /home

# 拷贝是一个手动过程，未来我们使用 -v 卷的技术，可以实现自动同步
```

