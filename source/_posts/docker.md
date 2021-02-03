---
title: docker
toc: true
date: 2019-12-28 20:48:48
tags: [docker]
categories:
- CODE工具
---
[思维导图](Docker.html)
# docker简介
<!--more-->
几个重要的概念，镜像（image），容器（container），仓库（repository）
 Docker 本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置依赖打包好形成一个可交付的运行环境，这个打包好的运行环境就似乎 image镜像文件。只有通过这个镜像文件才能生成 Docker 容器。image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。
image 文件生成的容器实例，本身也是一个文件，称为镜像文件。
一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例，也就是我们的容器
至于仓储，就是放了一堆镜像的地方，我们可以把镜像发布到仓储中，需要的时候从仓储中拉下来就可以了。

# docker的安装
## mac下docker的安装
直接在`www.docker.com`下载dmg安装包进行安装
新版本需要账号（用户名xiaobais）
速度过慢可以在下一节中的阿里云镜像加速器中获取

# hello-world
## 阿里云镜像加速
1. dev.aliyun.com进行登录
2. 在服务中搜索-》容器镜像服务
3. 镜像中心-》镜像加速器
4. 按照对应的教程进行

## docker为什么比虚拟机快
(1)docker有着比虚拟机更少的抽象层。
(2)docker利用的是宿主机的内核,而不需要Guest OS。
![](docker_vm.png)

# docker常用命令
## docker帮助命令
```
docker version
docker info
docker --help
```

## docker镜像命令
1. `docker images`
列出本地的镜像模板
> Options
> -a 列出本地的所有镜像
> -q 只显示对象的ID
> --digests: 显示对象的摘要信息
> --no-trunc: 显示完整的信息

2. `docker search`
> Options
> -s 列出点赞数不小于指定值的镜像
> -no-trunc

3. `docker pull 《镜像名》`
> Options
> docker pull 镜像名 等价于 docker pull 镜像名:lastest
> docker pull 镜像名[:TAG]

4. `docker rmi`
> Options
> 删除单个镜像 docker rmi -f 镜像名
> 删除多个镜像 docker rmi -f 镜像名:TAG 镜像名2:TAG
> 删除全部  dockr rmi -f $(docker images -qa)

## docker容器命令
1. 新建并启动容器
`docker run [OPTIONS] IMAGE [COMMAND][ARG...]`
`docker run -it IMAGE` 启用交互式容器

> OPTIONS
> --name="容器的新名字"：为容器制定一个名称
>  -i：以交互模式运行容器，通常与 -t 同时使用；
> -t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；
> -d: 后台运行容器，并返回容器ID，也即启动守护式容器；
> -P: 随机端口映射；
> -p: 指定端口映射，有以下四种格式
>     ip:hostPort:containerPort
>     ip::containerPort
>     hostPort:containerPort
>     containerPort 

2. 列出所有正在运行的容器
`docker ps`
> OPTIONS说明（常用）：
> -a :列出当前所有正在运行的容器+历史上运行过的
> -l :显示最近创建的容器。
> -n：显示最近n个创建的容器。
> -q :静默模式，只显示容器编号。
> --no-trunc :不截断输出。

3. 启动容器
`docker start`

4. 重启容器
`docker restart`

5. 停止容器
`docker stop`

6. 强制停止容器
`docker kill`

7. 删除已停止容器
`docker rm `
删除多个容器
- `docker rm -f $(docker ps -a -q)`
- `docker ps -a -q |xargs docker rm`

### 重要
1. 启用守护式容器 `docker run -d IMAGE`  后台运行就会自动退出
   `docker run -d mycentos /bin/sh -c "while true;do echo hello xx;sleep 2;done"`
2. 查看容器日志 `docker logs -f -t --tail 容器id`
3. 查看容器内进程 `docker top`
4. 查看容器内部细节 `docker inspect`
5. 进入正在运行的容器并以命令行交互
  - 进入容器 `docker attach 容器id`
  - 在容器中执行命令`docker exec -it 容器id COMMAND`
  - 在容器中执行命令并以命令行交互 `docker exec -it 容器id /bin/bash` 此时使用exit退出容器并不会关闭容器
6. 将容器内数据拷贝到主机上
  - `docker cp 容器id:地址 本地地址`
7. 提交容器副本，使之成为新的镜像
  `docker commit`
  `docker commit -m="描述信息" -a="作者" 容器id 要创建的镜像的名称:[标签名]`

### docker命令总结
![](docker_command.png)
> attach    Attach to a running container                 # 当前 shell 下 attach 连接指定运行镜像
> 
> build     Build an image from a Dockerfile              # 通过 Dockerfile 定制镜像
> 
> commit    Create a new image from a container changes   # 提交当前容器为新的镜像
> 
> cp        Copy files/folders from the containers filesystem to the host path   #从容器中拷贝指定文件或者目录到宿主机中
> 
> create    Create a new container                        # 创建一个新的容器，同 run，但不启动容器
> 
> diff      Inspect changes on a container's filesystem   # 查看 docker 容器变化
> 
> events    Get real time events from the server          # 从 docker 服务获取容器实时事件
> 
> exec      Run a command in an existing container        # 在已存在的容器上运行命令
> 
> export    Stream the contents of a container as a tar archive   # 导出容器的内容流作为一个 tar 归档文件[对应 import ]
> 
> history   Show the history of an image                  # 展示一个镜像形成历史
> 
> images    List images                                   # 列出系统当前镜像
> 
> import    Create a new filesystem image from the contents of a tarball # 从tar包中的内容创建一个新的文件系统映像[对应export]
> 
> info      Display system-wide information               # 显示系统相关信息
> 
> inspect   Return low-level information on a container   # 查看容器详细信息
> 
> kill      Kill a running container                      # kill 指定 docker 容器
> 
> load      Load an image from a tar archive              # 从一个 tar 包中加载一个镜像[对应 save]
> 
> login     Register or Login to the docker registry server    # 注册或者登陆一个 docker 源服务器
> 
> logout    Log out from a Docker registry server          # 从当前 Docker registry 退出
> 
> logs      Fetch the logs of a container                 # 输出当前容器日志信息
> 
> port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT    # 查看映射端口对应的容器内部源端口
> 
> pause     Pause all processes within a container        # 暂停容器
> 
> ps        List containers                               # 列出容器列表
> 
> pull      Pull an image or a repository from the docker registry server   # 从docker镜像源服务器拉取指定镜像或者库镜像
> 
> push      Push an image or a repository to the docker registry server    # 推送指定镜像或者库镜像至docker源服务器
> 
> restart   Restart a running container                   # 重启运行的容器
> 
> rm        Remove one or more containers                 # 移除一个或者多个容器
> 
> rmi       Remove one or more images             # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
> 
> run       Run a command in a new container              # 创建一个新的容器并运行一个命令
> 
> save      Save an image to a tar archive                # 保存一个镜像为一个 tar 包[对应 load]
> 
> search    Search for an image on the Docker Hub         # 在 docker hub 中搜索镜像
> 
> start     Start a stopped containers                    # 启动容器
> 
> stop      Stop a running containers                     # 停止容器
> 
> tag       Tag an image into a repository                # 给源中镜像打标签
> 
> top       Lookup the running processes of a container   # 查看容器中运行的进程信息
> 
> unpause   Unpause a paused container                    # 取消暂停容器
> 
> version   Show the docker version information           # 查看 docker 版本号
> 
> wait      Block until a container stops, then print its exit code   # 截取容器停止时的退出状态值

# docker镜像
镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。
**UnionFS(联合文件系统)**
UnionFS（联合文件系统）：Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。
**docker镜像加载**
  docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。
bootfs(boot file system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。
rootfs (root file system) ，在bootfs之上。包含的就是典型 Linux 系统中的 /dev, /proc, /bin, /etc 等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。 

# docker容器数据卷
docker容器数据卷目的是将容器产生的数据进行持久化
卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过Union File System提供一些用于持续存储或共享数据的特性：
卷的设计目的就是数据的持久化，完全独立于容器的生存周期，因此Docker不会在容器删除时删除其挂载的数据卷

**特点**：
1：数据卷可在容器之间共享或重用数据
2：卷中的更改可以直接生效
3：数据卷中的更改不会包含在镜像的更新中
4：数据卷的生命周期一直持续到没有容器使用它为止

## 数据卷
1. 容器内添加
`docker run -it -v /宿主机目录:/容器内目录 镜像名 /bin/bash`
`docker run -it -v /宿主机目录:/容器内目录:ro 镜像名 /bin/bash` 容器内目录只读read only
2. DockerFile添加
`VOLUME["/dataVolumeContainer","/dataVolumeContainer2","/dataVolumeContainer3"]`
```
# volume test
FROM centos
VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]
CMD echo "finished,--------success1"
CMD /bin/bash
```
`docker build -f dockerfile -t newcentos .`
对应的宿主机上的卷地址可以通过`docker inspect newcentos`

Docker挂载主机目录Docker访问出现cannot open directory .: Permission denied
解决办法：在挂载目录后多加一个--privileged=true参数即可

## 数据卷容器
命名的容器挂载数据卷，其它容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器
容器见传递共享`--volumes-from`

`docker run -it --name doc2 --volume-from doc1 centos`
容器之间配置信息传递，数据卷的生命周期一直持续到没有容器使用他为止。

# DockerFile解析
## 构建三步骤
编写dockerfile - > docker build - > docker run

## 内容基础
1. 每条保留字命令都必须为大写字母且后面要跟随至少一个参数
2. # 表示注释
3. 每条指令都会创建一个新的镜像层进行提交

## docker执行dockerfile的大致流程
1. docker从基础镜像运行一个容器
2. 执行一条指令并对容器进行修改
3. 执行类似commit的操作提交一个新的镜像层
4. docker再基于刚提交的镜像运行一个新容器
5. 执行dockerfile中的下一条指令直到所有指令都执行完成

## docker保留字指令
1. FROM: 基础镜像，当前的新镜像基于哪个镜像
2. MAINTAINER：镜像维护者姓名和邮箱
3. RUN：容器构建时需要运行的命令
4. EXPOSE：当前容器对外暴露出的端口
5. WORKDIR：指定在创建容器后，终端默认登陆进来的工作目录
6. ENV：用来在构建镜像过程中设置环境变量
7. ADD：将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包
8. COPY：类似ADD,拷贝文件和目录到镜像中，将从构建上下文目录中<源路径>的文件/目录复制到新的一层的镜像内的<目标路径>位置
9. VOLUME：容器数据卷，用于数据保存与持久化工作
10. CMD：指定一个容器启动时要执行的命令。dockerfile可以有多个CMD命令，但只有最后一个生效，CMD会被docker run后的参数替换
11. ENTRYPOINT：指定一个容器启动时要运行的命令。会追加参数
12. ONBUILD：当构建一个被继承的dockerfile时运行命令，父镜像在被子继承后父镜像的onbuild被触发

![](docker_image.png)
## 将镜像发布到阿里云
dev.aliyun.com创建仓库
在仓库中的管理按钮中查看帮助文档即可
```
sudo docker login --username= registry.cn-xxx.aliyuncs.com
sudo docker tag [Imageid] resgistry.cn-xxx.aliyuncs.com/xasdf/asdff:[镜像版本号]
sudo docker push registry.cn-xxx.aliyuncs.com/xasdf/asdff:[镜像版本号]
```
