2023年2月3日 20点51分

# Docker

## 1 Docker基础

### 1.1 Docker介绍

> **Docker为什么出现**

如何确保应用能够在这些环境中运行和通过质量检测?并且在部署过程中不出现令人头疼的版本,配置问题,也无需重新编写代码和进行故障修复?

答案就是使用容器,Docker之所以发展如此迅速,也是因为它对此给出了一个标准的化的解决方案--**系统平滑移植,容器虚拟化技术**

环境配置相当麻烦,换一台机器,就要重来一次,费时费力,能不能从根本解决问题:**软件可以带环境安装?也就是说,安装的时候,把原始环境一摸一样的复制过来,开发人员利用Docker可以消除协作编码使"在我的机器上可正常工作"的问题**

哈哈哈哈,老师说的好形象啊,用docker之前,部署项目像是搬家,而用了docker,就像是直接把一栋楼都搬过去了

**Docker的出现使得Docker得以打破过去[程序即应用]的观念,透过镜像(images)将作业系统核心除外,运作应用程序所需要的系统环境,由下而上打包,达到应用程序跨平台间的无缝接轨运转**

> **Docker理念**

**一个镜像,处处运行**

一句话:**Docker是解决了运行环境和配置问题的软件容器,方便做持续集成并有助于整体发布的容器虚拟化技术**

> **传统虚拟机和Docker的区别**

传统虚拟机技术基于安装在操作系统上的虚拟机管理系统(VMWare等)创建虚拟机(虚拟出各种硬件),在虚拟机上安装从操作系统,在从操作系统中安装部署各种应用

虚拟机的缺点

- 资源占用多
- 用于步骤多
- 启动慢

而容器和虚拟机不一样,不需要捆绑一整套操作系统,Docker容器是在操作系统层面实现虚拟化,直接复用本地主机的操作系统,而传统虚拟机则是在硬件层面实现虚拟化,与传统的虚拟机相比,Docker优势体现在启动速度快,占用体积小

比较了Docker和传统虚拟化方式的不同之处

- 传统虚拟机技术是虚拟出一套硬件后,在其运行一个完整操作系统,在该系统上再运行所需要应用程序
- 容器内的应用程序直接运行于宿主的内容,容器内没有自己的内核**且也没有进行硬件虚拟**,因此容器要比传统虚拟机更加轻便
- 每个容器之间互相隔离,每个容器有自己的文件系统,容器之间进程不会相互影响,能区分计算资源

### 1.2 Docker安装配置

两个网站
[Docker官网](https://docs.docker.com/)
[DockerHub仓库](https://hub.docker.com/)
[阿里云](/)

**Docker必须部署在Linux内核的系统上**

> **Docker的基本组成**

1. 镜像
2. 容器
3. 仓库

镜像(image),就是一个只读的模板,镜像可以用来创建Docker容器,一个镜像可以创建很多容器
它也相当于是一个root文件系统,docker容器实例类似于一个Java中new出来的实例对象

> **开始安装**

[centos7安装](https://docs.docker.com/engine/install/centos/)

1. 安装docker所需的配置
   1. 安装gcc和g++(以前已经安装过了)
   2. 安装需要的软件包 `yum install -y yum-utils`
   3. 安装stable镜像仓库 `yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo`
   4. 更新yum软件包索引 `yum makecache fast`
2. 安装docker
   1. 安装docker CE `yum -y install docker-ce docker-ce-cli containerd.io`
   2. 启动docker `systemctl start docker`
   3. 验证docker安装是否成功 `docker version`查看版本号,输出hello-world `docker run hello-world`
      ![20230205095159](https://img01.zzmr.club/img/20230205095159.png)
3. docker卸载
   1. `systemctl stop docker`
   2. `yum remove docker-ce docker-ce-cli containerd.io`
   3. `rm -rf/var/lib/docker`
   4. `rm -rf/var/lib/containerd`

> **配置阿里云镜像加速**

个人环境

[镜像加速器](https://cr.console.aliyun.com/cn-qingdao/instances/mirrors)

我的:`https://kr4jc007.mirror.aliyuncs.com`

配置:

1. `mkdir -p /etc/docker`
2. 配置一个demo.json文件

```json
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://kr4jc007.mirror.aliyuncs.com"]
}
EOF
```

3. `systemctl daemon-reload`
4. `systemctl restart docker`

### 1.3 Docker常用命令

#### 1.3.1 帮助启动类命令

1. 启动docker:`systemctl start docker`
2. 停止docker:`systemctl stop docker`
3. 重启docker:`systemctl restart docker`
4. 查看docker状态:`systemctl status docker`
5. 开机启动:`systemctl enable docker`
6. 查看docker概要信息:`docker info`
7. 查看docker总体帮助文档:`docker --help`
8. 查看docker命令帮助文档:`docker 具体命令 --help`

#### 1.3.2 镜像命令

1. 列出本机上的所有镜像:`docker images`
   ![20230205103721](https://img01.zzmr.club/img/20230205103721.png)
   - -a: 列出本地所有的镜像(含历史映像层)
   - -q: 只显示镜像ID
   - RESPOSITORY: 表示镜像的仓库源
   - TAG: 镜像的标签(版本号)
   - IMAGE ID: 镜像ID
   - GREATED: 镜像创建时间
   - SIZE: 镜像大小
2. `docker search xxx`某个xxx镜像名字,可以查看本地和远程的镜像
   - 加上 `--limit 5 redis`就表示只列出5个
3. 下载镜像:`docker pull name`
   - `docker pull name[:TAG]`,指定版本号 `docker pull redis:6.0.8`
   - `docker pull name`,没有TAG,默认下载最新版
4. `docker system df`查看镜像/容器/数据卷所占用的空间
   ![20230205105757](https://img01.zzmr.club/img/20230205105757.png)
5. `docker rmi (-f) IMAGE_ID/name`根据镜像的名字或者id删除(-f:强制删除),**空格隔开,可以删除多个**,删除全部 `docker rmi -f $(docker images -qa)`

> **面试题:谈谈docker虚悬镜像是什么**
> 是什么:仓库名,标签名都是\<none\>的镜像,俗称虚悬镜像像 dangling image

#### 1.3.3 容器命令

> **Ubuntu容器说明**

有镜像才能创建容器
这时根本前提(下载一个CentOS或者Ubuntu镜像演示)

1. 新建+启动容器:`docker run [options] iamge [command] [arg...]`
   - `--name=容器名字`,为容器指定一个名称
   - `-d`后台运行容器并返回容器ID,也即启动守护式容器(后台运行)
   - `-i`以交互式模式运行容器,通常-i同时使用
   - `-t`为容器重新分配一个伪输入端,通常与-i同时使用,也即启动交互式容器(**前台有伪输入端,等待交互**)
   - `-P`随机端口映射
   - `-p`指定端口映射
   - ![20230205140717](https://img01.zzmr.club/img/20230205140717.png)
   - 参数说明,`-i`:交互式操作,`-t`:终端,`ubuntu`:ubuntu镜像,`/bin/bash`:放在镜像后面的是命令,这里我们希望有个交互式shell,因此使用的是 `/bin/bash`,
   - 要退出终端,直接输入exit

*这就相当于,我的centos装了docker,然后docker又装了一个ubuntu*

2. 列出所有当前正在运行的容器:`docker ps [Options]`
   - ![20230205141403](https://img01.zzmr.club/img/20230205141403.png)
   - `-a:`列出当前所有正在运行的容器+历史上运行过的
   - `-l`:显示最近创建的容器
   - `-n count`:显示最近count个创建的容器
   - `-q`:静默模式,只显示容器编号
3. 退出容器
   - `exit`,run进去容器,exit退出,容器停止
   - `ctrl+p+q`run进去的容器,执行该命令,容器退出但不会停止(但是这个一直出问题啊,这个按键一直用不了怎么办)
4. 启动已停止运行的容器:`docker start 容器ID或者容器名`
5. 重启容器 `docker restart容器ID或者容器名`
6. 停止容器 `docker stop容器名或者容器ID`
7. 强制停止容器 `docker kill 容器名/容器ID`
8. 删除已停止的容器 `docker rm 容器名/容器ID`
9. 一次删除多个容器实例 `docker rm -f$(docker ps -a -q)`以及 `docker ps -a -q | xargs docker rm`

又是隔了好多好多天没看,天天忙着复习考试,还待写课设,课设写的还是一坨

2023年2月25日 21点14分

继续吧

#### 1.3.4 启动守护式容器

> 在大部分的场景下,我们希望docker的服务是在后台运行的,我们可以通过 `-d`指定容器的后台运行模式

1. 先下载一个redis:`docker pull redis:6.0.8`直接用ubuntu也行
2. 执行 `docker run -d ubuntu`
3. 然后执行 `docker ps`你就会发现,并没有什么已运行的容器,而是这个**容器已经退出**
   1. **很重要的一点,Docker容器后台运行,就必须有一个前台进程**,容器运行的命令如果不是那些**一直挂起的命令(比如运行top,tail),就会自动退出的**
   2. 最佳的解决方案是:**将你要运行的程序以前台进程的形式运行,常见的就是命令行模式,表示我还有交互操作,不要中断**
   3. 意思就是,**有些容器就是要以前台(-it)的形式使用**
4. 使用redis演示前台和后台
   1. 前台:`docker run -it redis:6.0.8`
      ![20230225214012](https://img01.zzmr.club/img/20230225214012.png)
   2. 后台:`docker run -d redis:6.0.8`
   3. 执行 `docker logs xxx(xxx是容器的id)`,可以获取容器的日志,这里获取的就是前台打印的那些信息

#### 1.3.5 其他命令

1. 查看容器内运行的进程
   - Linux本机上是 `top`命令
   - 而docker中则是 `docker top xxx(容器id)`
   - ![20230225215343](https://img01.zzmr.club/img/20230225215343.png)
2. 查看容器内部细节
   - `docker inspect 容器id`
   - ![20230225215530](https://img01.zzmr.club/img/20230225215530.png)
3. 进入正在运行的容器并以命令行交互
   1. `docker exec -it 容器ID bashShell`
   2. 重新进入 `docker attach 容器ID`
   3. 上述两个区别
      - attach直接进入容器启动命令的终端，不会启动新的进程用exit退出，会导致容器的停止
      - exec是容器中打开新的终端，并且可以启动新的进程，用exit退出，不会导致容器的停止
      - 推荐使用docker exec命令,因为退出容器终端,不会导致容器的停止
   4. 使用redis测试,redis这个服务之前是开启的后台服务(守护式),所以现在可以使用 `docker exec -it 容器id /bin/bash`进入,再使用exit退出,还是可以进入的,容器并没有关闭
      ![20230304180652](https://img01.zzmr.club/img/20230304180652.png)
   5. 一般用-d后台启动的程序,再用exec进入对应容器实例
4. 从容器内拷贝文件到主机上
   1. `docker cp 容器ID:容器内路径 目的主机路径`
   2. ![20230304181749](https://img01.zzmr.club/img/20230304181749.png)
5. 导入和导出容器
   1. 导出容器:`docker export 容器ID > 目标位置/目标文件名`
   2. export导出容器的内容作为一个tar归档文件 `[对应import命令]`
      ![20230304183405](https://img01.zzmr.club/img/20230304183405.png)
   3. `cat 文件名.tar | docker import - 镜像用户/镜像名:镜像版本号`
   4. import从tar包中的内容创建一个新的文件系统再导入为镜像 `[对应export]`
   5. ![20230304184156](https://img01.zzmr.club/img/20230304184156.png)

#### 1.3.6 总结

![20230304184629](https://img01.zzmr.club/img/20230304184629.png)

```
attach    Attach to a running container                 # 当前 shell 下 attach 连接指定运行镜像

build     Build an image from a Dockerfile              # 通过 Dockerfile 定制镜像

commit    Create a new image from a container changes   # 提交当前容器为新的镜像

cp        Copy files/folders from the containers filesystem to the host path   #从容器中拷贝指定文件或者目录到宿主机中

create    Create a new container                        # 创建一个新的容器，同 run，但不启动容器

diff      Inspect changes on a container's filesystem   # 查看 docker 容器变化

events    Get real time events from the server          # 从 docker 服务获取容器实时事件

exec      Run a command in an existing container        # 在已存在的容器上运行命令

export    Stream the contents of a container as a tar archive   # 导出容器的内容流作为一个 tar 归档文件[对应 import ]

history   Show the history of an image                  # 展示一个镜像形成历史

images    List images                                   # 列出系统当前镜像

import    Create a new filesystem image from the contents of a tarball # 从tar包中的内容创建一个新的文件系统映像[对应export]

info      Display system-wide information               # 显示系统相关信息

inspect   Return low-level information on a container   # 查看容器详细信息

kill      Kill a running container                      # kill 指定 docker 容器

load      Load an image from a tar archive              # 从一个 tar 包中加载一个镜像[对应 save]

login     Register or Login to the docker registry server    # 注册或者登陆一个 docker 源服务器

logout    Log out from a Docker registry server          # 从当前 Docker registry 退出

logs      Fetch the logs of a container                 # 输出当前容器日志信息

port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT    # 查看映射端口对应的容器内部源端口

pause     Pause all processes within a container        # 暂停容器

ps        List containers                               # 列出容器列表

pull      Pull an image or a repository from the docker registry server   # 从docker镜像源服务器拉取指定镜像或者库镜像

push      Push an image or a repository to the docker registry server    # 推送指定镜像或者库镜像至docker源服务器

restart   Restart a running container                   # 重启运行的容器

rm        Remove one or more containers                 # 移除一个或者多个容器

rmi       Remove one or more images       # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]

run       Run a command in a new container              # 创建一个新的容器并运行一个命令

save      Save an image to a tar archive                # 保存一个镜像为一个 tar 包[对应 load]

search    Search for an image on the Docker Hub         # 在 docker hub 中搜索镜像

start     Start a stopped containers                    # 启动容器

stop      Stop a running containers                     # 停止容器

tag       Tag an image into a repository                # 给源中镜像打标签

top       Lookup the running processes of a container   # 查看容器中运行的进程信息

unpause   Unpause a paused container                    # 取消暂停容器

version   Show the docker version information           # 查看 docker 版本号

wait      Block until a container stops, then print its exit code   # 截取容器停止时的退出状态值
```

### 1.4 Docker镜像

#### 1.4.1 UnionFS(联合文件系统)

UnionFS是一种分层,**轻量级并且高性能的文件系统,**它支持对文件系统的修改作为一次提交来层层的叠加,同时可以将不同目录挂载到同一个虚拟文件系统下,**Union文件系统是Docker镜像的基础,镜像可以通过分层来进行继承**,基于基础镜像(没有父镜像),可以制作各种具体的应用镜像

特征:一次同时加载多个文件系统,但从外面看起来,只能看到一个文件系统,联合加载会把各层文件系统叠加起来,**这样最终的文件系统会包含所有底层的文件和目录**

> Docker镜像的最底层是引导文件系统bootfs,这一层与我们典型的Linux/Unix系统是一样的,包含boot加载器和内核.当boot加载完成之后整个内核就都在内存中了,此时内存的使用权已由bootfs转交给内核,此时系统也会卸载bootfs

对于一个精简的OS,rootfs可以很小,只需要包括最基本的命令,工具和程序库就可以了,因为底层直接用Host的kernel,自己只需要提供rootfs就行了,由此可见对于不同的linux发行版,bootfs基本是一致的,rootfs会有差别,因此不同的发行版可以公用bootfs

> 为什么Docker镜像要采用这种分层结构呢?

镜像分层最大的一个好处就是共享资源,方便复制迁移,就是为了复用
**比如说有多个镜像从相同的base镜像构建而来,那么Docker Host只需要在磁盘上保存一份base镜像;同时内存中也只需要加载一份base镜像,就可以为所有容器服务了,而且镜像的每一层都可以被共享**

**Docker镜像层都是只读的,容器层是可写的**,当容器启动时,一个新的可写层被加载到镜像的顶部,这一层通常被称作"容器层",容器层之下的都叫"镜像层"

#### 1.4.2 Commit命令

`docker commit`提交容器副本使之称为一个新的镜像

`docker commit -m="提交得描述信息"-a="作者" 容器ID 要创建的目标镜像名:[标签名]`

就是说,现在直接run得一个ubuntu容器,是没有vim命令的,想要给这个容器添加这个vim命令,就是要使用commit命令

执行步骤:

1. `apt-get update`,更新包管理工具
2. `apt-get install vim`,安装我们需要的vim

这时就可以直接使用vim命令了

刚好又学了一个命令:`cat a.txt`,打印a.txt的内容

这时提交Commit自己的镜像

`docker commit -m="vim cmd add ok" -a="zzmr" 容器ID zzmr/myubuntu:1.3`

此时,就会发现,多了一个ubuntu的镜像
![20230304212614](https://img01.zzmr.club/img/20230304212614.png)

再次run这个新的镜像,构造一个容器 `docker run -it 容器ID /bin/bash`,即可实现vim命令

> **Docker中的镜像分层,支持通过扩展现有镜像,创建新的镜像,类似Java继承于一个Base基础类,自己再按需扩展,新镜像是从base镜像一层一层叠加生成的,每安装一个软件,就在现有镜像的基础上再增加一层**

#### 1.4.3 发布镜像

> **本地镜像发布到阿里云**

1. 创建命名空间
   ![20230304214824](https://img01.zzmr.club/img/20230304214824.png)
2. 然后创建仓库,创建完之后来到仓库基本信息页面
3. 将镜像推送到Registry,分为三步:

```
$ docker login --username=灼灼某人 registry.cn-qingdao.aliyuncs.com
$ docker tag [ImageId] registry.cn-qingdao.aliyuncs.com/zzmr/myubuntu:[镜像版本号]
$ docker push registry.cn-qingdao.aliyuncs.com/zzmr/myubuntu:[镜像版本号]
```

![20230304215814](https://img01.zzmr.club/img/20230304215814.png)

> **从阿里云拉取镜像**

`docker pull registry.cn-qingdao.aliyuncs.com/zzmr/myubuntu:[镜像版本号]`

这时直接可以拉取该镜像

#### 1.4.4 Docker私有库

1. 执行 `docker pull registry`
2. 运行私有库registry,相当于本地有个私有docker hub

   - 默认情况下,仓库被创建在容器的/var/lib/registry目录下,加你自行用容器卷映射,方便于宿主机联调
   - 进行新建:`docker run -d -p 5000:5000  -v /zzmruse/myregistry/:/tmp/registry --privileged=true registry`
3. 给初始的ubuntu镜像安装ifconfig命令

   1. 更新包管理工具 `apt-get update`
   2. 安装 `apt-get install net-tools`
   3. Commit当前的镜像 `docker commit -m="vim cmd add ok" -a="zzmr" 容器ID zzmr/myubuntu:1.3`
   4. 成功
4. 推送镜像

   1. 推送之前,可以查看仓库中是否存在文件:`curl -XGET http://1.14.102.11:5000/v2/_catalog`![20230305093952](https://img01.zzmr.club/img/20230305093952.png)
   2. 将新镜像zzmrubuntu:1.2修改符合私服规范的Tag `docker tag eca965faa1a1 1.14.102.11:5000/zzmrubuntu:1.2`
   3. 修改配置文件使之支持http

   ```json
   {
     "registry-mirrors": ["https://kr4jc007.mirror.aliyuncs.com"],
     "insecure-registries": ["1.14.102.11:5000"] // 新增的这条
   }
   ```

   4. push推送到私服库 `docker push 1.14.102.11:5000/zzmrubuntu:1.2`
   5. 再次执行get命令:![20230305095338](https://img01.zzmr.club/img/20230305095338.png)
   6. pull到本地并运行 `docker pull 1.14.102.11:5000/zzmrubuntu:1.2`
      ![20230305095951](https://img01.zzmr.club/img/20230305095951.png)

### 1.5 Docker容器数据卷

#### 1.5.1 容器数据卷简介

> Docker挂载主机目录访问如果出现cannot open directory:Permission denied,解决办法:在挂载目录后多加一个 `--privileged=true`参数即可

![20230305101703](https://img01.zzmr.club/img/20230305101703.png)

卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，**但不属于联合文件系统**，因此能够绕过UnioFS提供一些用于**持续存储或共享数据的特征**

卷的设计目的就是**数据的持久化**，完全独立于容器的生存周期，因此Docker不会在容器删除时删除其挂载的数据卷。

所以,卷的存在,就是为了将docker容器内的数据保存到宿主机的磁盘中

> **卷能干嘛**
> 将运用与运行的环境打包镜像,run后形成容器实例运行,但是我们对数据的要求是希望是持久化的
> Dokcer容器产生的数据,如果不备份,那么当容器实例删除后,容器内的数据自然也就没有了
> 为了能保存数据在docker中我们使用卷

特点:

1. 数据卷可以在容器之间共享或重用数据
2. 卷中的更改可以直接实时生效
3. 数据卷中的更改不会包含在镜像的更新中
4. 数据卷的生命周期一直持续到没有容器使用它为止

#### 1.5.2 容器卷和主机互通

> **运行一个带有容器卷存储功能的容器实例**

`docker run -it --privileged=true -v/宿主机绝对路径目录:/容器内目录 镜像名`

`docker run -it --privileged=true -v/tmp/host_data:/tmp/docker_data --name=u1 ubuntu`

这时主机上的就会出现:`/tmp/host_data`目录,docker中就会出现 `/tmp/docker_data`,如果在这两个文件夹中的任意一个新建一个txt,另一方都能拿得到备份

![20230305105403](https://img01.zzmr.club/img/20230305105403.png)

> **查看docker数据卷是否挂载成功**

执行命令 `docker inspect 容器id`
![20230305105916](https://img01.zzmr.club/img/20230305105916.png)

如果容器挂了,此时主机新建一个c.txt
再启动容器,会发现容器内的文件夹内也多了一个c.txt,说明在容器启动后会同步数据

#### 1.5.3 容器卷ro和rw读写规则

上面的相当于默认,为rw,也就是可读可写

如果想让容器中的数据是只读的,**容器实例内部被限制,只能读取不能写**

`docker run -it --privileged=true -v/宿主机绝对路径目录:/容器内目录:ro 镜像名`

`docker run -it --privileged=true -v/mydocker/u:/tmp/u:ro --name=u2 ubuntu`

![20230305205315](https://img01.zzmr.club/img/20230305205315.png)

也就是说,宿主机新建的文件可以传送到容器中,容器可以进行查看,但是不能修改,是只读的.

**默认都是可读可写的**

#### 1.5.4 容器卷之间的继承

1. 容器1完成和宿主机的映射
2. 容器2继承容器1的卷规则
   - `docker run -it --privileged=true --volumes-from 父类 --name u2 ubuntu`
   - `docker run -it --privileged=true --volumes-from u11 --name u22 ubuntu`

> **这个也没啥难的,就是继承的关系啊,容器继承父容器,子容器可以接收到父容器的文件,子容器和父容器以及主机都可以实现互通**

如果父容器停止了,子容器仍在还在,而且和主机间的关系仍然保持,仍可以互通

**再重新启动u11,也就是父容器,会发现数据仍然存在,也就是说,父容器开机后,会同步数据**

### 1.6 Docker常规软件安装

这一章应该是操作上最重要的,到后面测试一下把项目部署到docker上,如果成功了就证明这个docker算是学到了

分为tomcat,mysql,redis,Nginx

> 总体步骤

1. 搜索镜像
2. 拉取镜像
3. 查看镜像
4. 启动镜像-服务端口映射
5. 停止容器
6. 移除容器

#### 1.6.1 安装tomcat

1. docker hub上面查找tomcat镜像:`docker search tomcat`
2. 从docker hub上拉取tomcat镜像到本地:`docker pull tomcat`
3. docker images查看是否有拉取到tomcat
4. 使用tomcat镜像创建容器实例(也叫运行镜像),`docker run -it -p 8080:8080 tomcat`
   - `-p`小写,主机端口:docker容器端口
   - `-P`大写,随机分配端口
   - `i`交互
   - `t:`终端
   - `d`后台
   - 实例命令:`docker run -d -p 8080:8080 --name t1 tomcat`
   - ![20230305215159](https://img01.zzmr.club/img/20230305215159.png)
5. 访问猫首页
   - 发现问题,访问首页:`http://1.14.102.11:8080/`,404,这是由于最新版的tomcat修改了配置
   - 解决:
     - 可能没有映射端口或者没有关闭防火墙
     - 把webapps.dist目录换成webapps
     - 进入到tomcat容器中,打开webapps文件夹,发现是空的.![20230305215724](https://img01.zzmr.club/img/20230305215724.png)
     - 删除webapps `rm -r webapps`
     - 将文件名更改 `mv webapps.dist webapps`
     - 成功![cat](https://img01.zzmr.club/img/cat.png)
6. 免修改版说明
   - 最新版的是要修改的,所以就有了免修改版,以后用的应该还是这个,tomcat8嘛
   - `docker pull billygoo/tomcat8-jdk8`
   - `docker run -d -p 8080:8080 --name mytomcat8 billygoo/tomcat8-jdk8`
   - 其实可以不执行pull,因为run时,如果本地没有这个镜像,就会自动pull
   - 执行完,就直接有了![cat8](https://img01.zzmr.club/img/cat8.png)

#### 1.6.2 安装mysql

视频是教5.7,我感觉我装8应该也一样吧
如果在docker里面创建好mysql,我在navicat里面也可以直接连接的,也就是说,我的项目也可以直接使用docker的mysql了

> **下面这个安装步骤有问题,往后翻**

1. `docker pull mysql:8.0.26`
2. 开启实例 `docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0.26`
   ![20230306141724](https://img01.zzmr.club/img/20230306141724.png)
3. 这个时候执行 `docker exec -it mysql容器ID /bin/bash`,进入mysql容器
4. 执行 `mysql -uroot -p`,进入mysql
   ![20230306141944](https://img01.zzmr.club/img/20230306141944.png)
5. 执行 `show databases;`,可以看到mysql内置的数据库
   ![20230306142122](https://img01.zzmr.club/img/20230306142122.png)
6. 执行新建数据库,新建表,插入数据,查询等操作都没问题.证明run成功
7. 远程连接出现问题:`Authentication plugin 'caching_sha2_password' cannot be loaded:`,解决办法:
   - 先使用mysql use mysql
   - 执行 `ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';`,意思就是客户端使用不了新的加密权限,所以要把mysql的密码加密方式改为老版的
   - `FLUSH PRIVILEGES;`刷新权限
8. 成功![20230306143346](https://img01.zzmr.club/img/20230306143346.png)

---

但是问题也就是出现了,如果往数据库中插入中文,在客户端里面中文是没问题的,但是在命令行就会显示问好
![20230306143800](https://img01.zzmr.club/img/20230306143800.png)

![20230306143819](https://img01.zzmr.club/img/20230306143819.png)

不过我感觉应该不影响使用哈哈哈

另一个重要的问题:MySQL的数据备份问题,这里就要用到数据卷了

所以,就有了实战版,也是正确的步骤

---

1. 新建mysql容器实例 `docker run -d -p 3306:3306 --privileged=true -v /zzmruse/mysql/log:/var/log/mysql -v /zzmruse/mysql/data:/var/lib/mysql -v /zzmruse/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:8.0.26`,可以看出,授予数据卷权限后,新增了三个 `-v`,也就是三个数据卷,分别用来保存log,data和conf

```
docker run -d -p 3306:3306 --privileged=true 
-v /zzmruse/mysql/log:/var/log/mysql 
-v /zzmruse/mysql/data:/var/lib/mysql 
-v /zzmruse/mysql/conf:/etc/mysql/conf.d 
-e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:8.0.26
```

![20230306150452](https://img01.zzmr.club/img/20230306150452.png)
2. 在 `/zzmruse/mysql/conf`新建my.cnf(配置文件),通过容器卷同步给mysql容器实例

```conf
[client]
default_character_set=utf8
[mysqld]
collation_server = utf8_general_ci
character_set_server = utf8
```

3. **重新启动mysql容器实例**,再重新进入并查看字符编码 `SHOW VARIABLES LIKE 'character%'`
   ![20230306151355](https://img01.zzmr.club/img/20230306151355.png)
4. 问题解决:![20230306151938](https://img01.zzmr.club/img/20230306151938.png),但是,不知道为啥,在命令行里不能输入中文了,可能是这个工具有问题.
5. 数据卷也应用成功,如果删除这个mysql容器,再新建一个,发现里面的数据仍在,甚至客户端连接工具都能照样连上.

#### 1.6.3 安装redis

docker中安装redis要比直接安装redis要麻烦

1. `docker run -d -p 6379:6379 redis:6.0.8`
2. 发现是没什么问题的:![20230306181520](https://img01.zzmr.club/img/20230306181520.png)
3. 参照mysql的配置,其实redis也是要进行配置的,并不是直接run一个这么简单

---

正经步骤:

1. 在centos宿主机新建目录 `/zzmruse/myredis`
2. 将一个redis.conf文件模板拷贝进 `/zzmruse/myredis`目录下,更改配置文件:
   - `requirepass "010203"`
   - `daemonize no`,这个选项是允许后台启动,会与 `-d`起冲突,所以要设为no或者注释到yes
   - 注释掉:`bind 127.0.0.1`,允许redis远程连接
   - 开启redis数据持久化 `appendonly yes`可选
3. `docker run -p 6379:6379 --name myr1 --privileged=true -v /zzmruse/myredis/redis.conf:/etc/redis/redis.conf -v /zzmruse/myredis/data:/data -d redis:6.0.8 redis-server /etc/redis/redis.conf`,执行成功:![20230306185749](https://img01.zzmr.club/img/20230306185749.png)
4. 连接时,由于设置了密码:`redis-cli -a 010203`

完事,算是把这三个软件给装明白了,但是还有一个东西没搞,就是jdk啊哈哈哈哈

算了,先看看高级篇讲的是什么

## 2 Docker高阶篇

这样吧,现在把DockerFile学了,然后紧接着看微服务实战,看完这两个,然后把项目部署到docker上

### 2.1 DockerFile

#### 2.1.1 DockerFile简介

> **DockerFile是什么**

DockerFile是用来**构建Docker镜像**的文本文件,是由一条条构建镜像所需的指令和参数构成的脚本

> **能干什么**

上面学到的commit也是构建镜像,这里又是构建镜像,区别在哪?

如果是commit形式,每改变一次内容,就要重新commit一次,很麻烦

DockerFile就是用来一次性解决这个问题的,提供一个list清单,后续需要加入任何功能,只需要在list单子里面run,相当于多次提交

#### 2.1.2 DockerFile构建

构建三步骤:

1. 编写DockerFile文件
2. docker build 命令构建镜像
3. docker run 依照镜像运行容器实例

> **DockerFile内容基础知识**

1. 每条保留字指令**都必须为大写字母且后面要跟随至少一个参数**
2. 指令按照从上到下,顺序执行
3. `#`表示注释
4. 每条指令都会创建一个新的镜像层并对镜像进行提交

> **Docker执行DockerFile的大致流程**

1. docker从基础镜像运行一个容器
2. 执行一条指令并对容器作出修改
3. 执行类似docker commit的操作提交一个新的镜像层
4. docker再基于刚提交的镜像运行一个新容器
5. 执行dockerfile中的下一条指令知道所有指令都执行完成

> **DockerFile,Docker镜像,Docker容器三者的关系**

Dockerfile面向开发,Docker镜像成为交付标准,Docker容器则涉及部署与运维,三者缺一不可,合力充当Docker体系的基石
![20230306211502](https://img01.zzmr.club/img/20230306211502.png)

1. DockerFile,需要定义一个DockerFile,DockerFile定义了进程需要的一切东西,DockerFile设计的内容包括执行代码或者是文件,环境变量,依赖包,运行时环境,动态链库,操作系统的发行版,服务进程和内核进程(当应用进程需要和系统服务和内核进程打交道,这时要考虑如何设计namespace的权限控制等)
2. Docker镜像,在用DockerFile定义一个文件之后,docker build时会产生一个docker镜像,当运行Docker镜像时会真正开始提供服务
3. Docker容器,容器是直接提供服务的

#### 2.1.3 DockerFile常用保留字

- From: 基础镜像,说白了,就是继承的谁,指定一个已经存在的镜像作为模板,第一条必须是from
- MAINTAINER: 镜像维护者的姓名和邮箱地址
- RUN
  - 容器构建时需要运行的命令
  - 两种格式:shell格式和exec格式
  - RUN是在docker build时运行
- EXPOSE: 当前容器对外暴露出的端口
- WORKDIR: 指定在创建容器后,终端默认登陆进来的工作目录,一个落脚点
- USER: 指定该镜像以什么样的用户去执行,如果都不指定,默认是root(一般不指定)
- ENV: 用来在构建镜像过程中设置环境变量
  - `ENV MY_PATH /usr/mytest`表示:这个环境变量可以在后续的任何RUN指令中使用,这就如同在命令前面指定了环境变量前缀一样;也可以在其它指令中直接使用这些环境变量,比如 `WORKDIR $MY_PATH`,就表示 `WORKDIR /usr/mytest`,**感觉就像是声明一个变量一样**
- ADD: 将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包
- COPY: 类似ADD,拷贝文件和目录到镜像中,将从构建上下文目录中 `<源路径>`的文件/目录复制到新的一层的镜像内的 `<目标路径>`位置
  - `COPY src dest`
  - `COPY ["SRC","dest"]`
  - `<src源路径>: 源文件或者源目录`
  - `<dest目标路径>: 容器内的指定路径,该路径不用事先建好`
- VOLUME: 容器数据卷,用于数据保存和持久化工作
- CMD: 指定容器启动后要干的事情
  - DockerFile中可以有多个CMD指令,**但只有最后一个生效,CMD会被docker run之后的参数替换**,什么意思呢,就是在执行run命令时,如果在最后加上了参数,比如 `/bin/bash`,就会覆盖掉CMD的内容(所以在run tomcat的时候不要加 `/bin/bash`)
  - 与RUN命令的区别
    - CMD是在docker run时运行
    - RUN是在docker build时运行
- ENTRYPOINT: 也是用来指定一个容器启动时要运行的命令,类似于CMD指令,但是ENTRYPOINT不会被docker run后面的命令覆盖,而且这些命令行参数会被当作参数送给ENTRYPOINT指令指定的程序

#### 2.1.4 DickerFile案例

1. 先pull一个centos镜像
2. 要求这个centos镜像具备vim,ifconfig,jdk8这三个功能
3. 在 `/myfile`编写Dockerfile文件-大写字母D

```dockerfile
FROM centos:7.9.2009
MAINTAINER zzmr<jim7yang@gmail.com>
 
ENV MYPATH /usr/local
WORKDIR $MYPATH
 
#安装vim编辑器
RUN yum -y install vim
#安装ifconfig命令查看网络IP
RUN yum -y install net-tools
#安装java8及lib库
RUN yum -y install glibc.i686
RUN mkdir /usr/local/java
#ADD 是相对路径jar,把jdk-8u171-linux-x64.tar.gz添加到容器中,安装包必须要和Dockerfile文件在同一位置
ADD jdk-8u171-linux-x64.tar.gz /usr/local/java/
#配置java环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
ENV PATH $JAVA_HOME/bin:$PATH
 
EXPOSE 80
 
CMD echo $MYPATH
CMD echo "success--------------ok"
CMD /bin/bash
```

4. 构建,`docker build -t 新镜像的名字:TAG .`,**注意,TAG后面有一个空格,和一个点**,`docker build -t centosjava8:1.5 .`,出现问题:`Failed to download metadata for repo 'appstream': Cannot prepare internal mirrorlist: No URLs in mirrorlist`,解决,就是在FROM的后面改成 `centos:7.9.2009`
   ![20230307174534](https://img01.zzmr.club/img/20230307174534.png)
5. 运行 `docker run -it 新镜像名字:TAG`,自动跳转到 `/usr/local`,证明这个Dockerfile文件生效了
   ![20230307174729](https://img01.zzmr.club/img/20230307174729.png)
   ![20230307174821](https://img01.zzmr.club/img/20230307174821.png)

算是完成了

---

#### 2.1.5 虚悬镜像

仓库名,标签都是 `<none>`的镜像,俗称dangling image

Dockerfile写一个:

```
from ubuntu
CMD echo 'action is success'
```

算是创建失败时,会产生虚悬镜像,删了就行

`docker image ls -f dangling=true`来查看所有的虚悬镜像

但是正经的删除方式是:`docker image prune`

### 2.2 微服务部署到docker

1. 通过IDEA新建一个普通微服务模块(我就直接拿之前写的软工课设了)
   - 那首先要把项目打成jar包,然后传到服务器上,不知道为啥,腾讯云的命令行上传的文件找不到,所以还是用的阿里网盘
   - 感觉还是有问题,就是配置文件那块,mysql的url应该改成服务器的id地址才对吧
   - 已经改成容器上的mysql的url了,也把数据库数据同步过去了,现在项目直接用的就是docker上的mysql
   - `/mycms`下
2. 通过dockerfile发布微服务部署到docker容器
   - 编写Dockerfile(注意将jar包和Dockerfile文件放在同一个目录下)

```
# 基础镜像使用java

FROM java:8

# 作者

MAINTAINER zzmr

# VOLUME 指定临时文件目录为/tmp，在主机/var/lib/docker目录下创建了一个临时文件并链接到容器的/tmp

VOLUME /tmp

# 将jar包添加到容器中并更名为zzyy_docker.jar

ADD cms-0.0.1-SNAPSHOT.jar zzmr_docker.jar

# 运行jar包

RUN bash -c 'touch /zzmr_docker.jar'

ENTRYPOINT ["java","-jar","/zzmr_docker.jar"]

#暴露6001端口作为微服务

EXPOSE 8888
```

3. 构建镜像 `docker build -t zzmr_cms:1.0 .`,打包成镜像文件,构建成功:
   ![20230307184835](https://img01.zzmr.club/img/20230307184835.png)
4. 创建运行容器:`docker run -d -p 8888:8888 zzmr_cms:1.0`

![20230307185449](https://img01.zzmr.club/img/20230307185449.png)

**运行成功**,但是我好像忘了一件事,vue那边忘了搬进jar中了

没事,一会再按流程走一遍就好了

突然意识到一个问题,就是前端的接口,全是用的localhost,这边不会发不过去吧

这样吧,把这个vue工程复制一份,改的名字,然后把要发布到服务器上的,都改成服务器的ip地址

部署成功:
![屏幕截图(724)](https://img01.zzmr.club/img/屏幕截图(724).png)

带吃顿好的庆祝一下

所以现在是先看后面的内容吧,把p41-p56放到以后用的时候在看,毕竟后面还有可视化工具呢,不用白不用

### 2.3 Docker网络

#### 2.3.1 Docker网络简介

> **是什么**

1. Docker不启动:默认网络情况
2. Docker启动后,网络情况
   - 启动之后,会有一个docker0
   - ![20230308131532](https://img01.zzmr.club/img/20230308131532.png)

> **查看docker网络模式命令**

当我们安装Docker后,默认会自动创建三个网络

1. `docker network ls`
   ![20230308131850](https://img01.zzmr.club/img/20230308131850.png)

> **Docker network 常用命令**

1. 创建一个docker网络 `docker network create aa_network`
2. 删除一个docker网络 `docker network rm aa_network`
3. 查看网络源数据 `docker network inspect xxx网络名字`

> **能干嘛**

1. 容器间的互联和通信以及端口映射
2. 容器IP变动时可以通过服务名直接网络通信而不受到影响

#### 2.3.2 网络模式

1. bridge：为每个容器分配,设置IP等,并将容器连到一个 `docker0`,虚拟网桥,默认为该模式
   - 使用 `--network bridge指定`,默认使用docker0
2. host: 容器将不会虚拟出自己的网卡,配置自己的IP地址等,而是使用宿主机的IP和端口
   - 使用 `--network host`指定
3. none: 容器有独立的network namespace,但并没有对其进行任何网络设置,如分配veth pair和网桥连接,IP等
   - 使用 `--network none`指定
4. container: 新创建的容器不会创建自己的网卡和配置自己的IP,而是和一个指定的容器共享IP,端口范围
   - 使用 `-network container:NAME或者容器ID`指定

> **network 底层ip和容器映射变化**

1. 先创建两个ubuntu容器,命名为u1和u2
2. 执行 `docker network inspect 容器名/ID`,也可以执行 `docker inspect u1|tail -n 20`,表示从后往前看,看20行
   ![20230308140432](https://img01.zzmr.club/img/20230308140432.png)

此时,可以看出,u1的地址是 `172.17.0.4`,u2的地址是 `172.17.0.5`,然后将u2这个容器删除,再新建一个u3,会发现,u3的ip地址也成了 `172.17.0.5`

也就是说,**docker容器内部的ip是有可能会发生改变的**

> **bridge网络模式**

Docker使用Linux桥接,在宿主机虚拟一个Docker容器网桥(docker0),Docker启动一个容器时会根据Docker网桥的网段分配给容器一个IP地址,称为Container-IP,同时Docker网桥是每个容器的默认网关,因为在同意宿主机内的容器都接入同一个网桥,这样容器之间就能够通过容器的Container-IP直接通信

- 网桥docker0创建一对对等虚拟设备接口,一个叫veth,另一个叫eth0,成对匹配
  - 整个宿主机的网桥模式都是docker0,类似一个交换机只有一堆接口,每个接口叫veth,在本地主机和容器内分别创建一个虚拟接口,并让他们彼此联通(这样一对接口叫veth pair)
  - 每个容器实例内部也有一块网卡,每个接口叫做eth0
  - docker0上面的每个veth匹配某个容器实例内部的eth0,两两配对,一一匹配

通过上述步骤,将宿主机上的所有容器都连接到这个内部网络上,两个容器在同一个网络下,会从这个网关下各自拿到分配的IP,此时两个容器的网络是互通的

> 题外话
> 查看端口占用程序:`netstat -tanlp`

服务器出问题了?
数据库被入侵了哈哈哈

bridge模式的对应关系
![20230308192445](https://img01.zzmr.club/img/20230308192445.png)

> **host网络模式**

直接使用宿主机的Ip地址与外界进行通讯,不再需要额外进行NAT转换

为什么感觉这块就是一些理论?

看看得了,有些东西以后用到再学

> **自定义网络模式**

1. 不用自定义网络:
   - `docker run -d -p 8081:8080 --name tomcat81 billygoo/tomcat8-jdk8`
   - `docker run -d -p 8082:8080 --name tomcat82 billygoo/tomcat8-jdk8`
   - 互相ping对方的ip地址,都是没问题的,但是ping服务名,就会提示 `ping: tomcat82: Name or service not known`
2. 用自定义网络:
   - 为解决ping服务名不生效的问题,可以使用自定义网络
   - 自定义桥接网络,自定义网络默认使用的是桥接网络bridge
   - 新建自定义网络
     - `docker run -d -p 8081:8080 --network zzmr_network --name tomcat8081 billygoo/tomcat8-jdk8`
     - `docker run -d -p 8082:8080 --network zzmr_network --name tomcat8082 billygoo/tomcat8-jdk8`
   - 新建容器加入上一步新建的自定义网络
   - 互相ping测试
     ![20230308200744](https://img01.zzmr.club/img/20230308200744.png)

没问题,这也挺简单啊哈哈哈

**自定义网络本身就维护好了主机名和ip的对应关系(ip和域名都能通)**

### 2.4 Compose容器编排

#### 2.4.1 Compose简介

> **是什么**

Docker-Compose 是Docker官方的开源项目,负责实现对Docker容器集群的快速编排

> **能干嘛**

Compose可以管理多个Docker容器组成一个应用,你需要定义一个YAML格式的配置文件 `docker-compose.yml`,写好多个容器之间的调用关系,然后,只需要一个命令,就能同时启动/关闭这些容器

> **为什么**

Docker建议我们每一个容器只运行一个服务,因为docker容器本身占用资源极少,所以最好是将每个服务单独分割开来,但是这样我们又面临一个问题,就是编写DockerFile文件,所以就诞生了docker-compose

Docker-compose允许用户通过一个单独的docker-compose.yml模板文件来定义一组相关联的应用容器为一个项目

#### 2.4.2 Compose安装

1. [官网](https://docs.docker.com/compose/install/linux/)
2. `curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
3. `chmod +x /usr/local/bin/docker-compose`
4. `docker-compose --version`

安装成功:
![20230308205427](https://img01.zzmr.club/img/20230308205427.png)

#### 2.4.3 Compose使用

- 核心概念:
  1. 服务(service),一个个应用容器实例,比如订单微服务,库存微服务,mysql容器,nginx容器或者redis容器等
  2. 工程(project),由一组关联的应用容器组成的一个完整业务单元
- 使用步骤:
  1. 编写Dockerfile定义各个微服务应用并构建出对应的镜像文件
  2. 使用docker-compose.yml,定义一个完整业务单元,安排好整体应用中的各个容器服务
  3. 最后,执行docker-compose up命令,来启动并与运行整个应用程序,完成一键部署上线

> **微服务升级上线**

刚好对这个小项目又进行了一下修复，修复了在用户名或者密码有一个为空还能给后端发送请求的bug

然后等到容器编排和图形化界面学完之后再重新部署上线一次哈哈哈

改造说明:

1. SQL建表建库
2. 一键生成说明
3. 改POM
4. 写YML
5. 主启动
6. 业务类
7. `mvn package`命令将微服务形成新的jar包,并上传到Linux服务器 `mydocker目录下`
8. 编写Dockerfile
9. 构建镜像

差不多,我这个项目应该是符合要求,但是很明显,他这个应该会把mysql归并到一个镜像中,到时候生成的也是一个容器,我猜的

看完这个20多分钟的视频,我发现他这个微服务的项目还是没有把mysql和redis分开,应该还是一个容器一个服务吧

我懂了
**compose就是用来一次启动多个容器的!!**

---

1. 不使用compose的部署流程
   1. 单独的mysql容器实例
   2. ~单独的redis容器实例~
   3. 微服务工程
   4. 上面三个容器实例顺序启动成功

他的项目比我的这个项目多了个redis,由于没有写redis,所以没办法搞了,但应该差不多

这是什么bug啊,执行run之后有回传参数,但是容器并没有启动成功

**初步诊断,是jar包有问题导致的,现在去idea里看看**

测试完毕,不是jar包的问题,因为idea里测试并没有报错,而且数据也可以正常访问

发现问题:Dockerfile里面的touch语句写错了,应该是zzmr_docker_test.jar

问题解决,就是Dockerfile文件的内容出现了问题,现在问题解决

**所以,部署完这个服务,老师想表达的意思就是,一个服务一个容器,一个一个的run,非常麻烦**

上面是成功了,但是run一个又一个,还是很麻烦,具有以下问题:

1. 先后顺序要求固定,先是mysql然后才能开启微服务
2. 多个run命令
3. 容器间的启停或宕机,有可能导致IP地址对应的容器实例变化,映射出错,要么生产IP写死(可以但不推荐),要么通过服务调用

使用compose和不使用compose
![20230312225938](https://img01.zzmr.club/img/20230312225938.png)

---

使用Compose

1. 服务编排,一套带走
2. 编写docker-compose.yml文件
3. 第二次修改微服务工程(还能怎么修改?)
4. 执行 `docker-compose up`或者 `docker-compose up -d`
5. 测试

感觉重要的就是修改和编写yml文件

发现这个docker视频就剩下10集了,这几天就看完得了

23点02分 2023年3月12日

**docker-compose.yml**

```yml
version: "3"

services:

  microService:

    image: zzmr_cms_test:1.0

    container_name: ms01

    ports:

      - "8888:8888"

    volumes:

      - /app/microService:/data

    networks: 

      - zzmr_net 

    depends_on: 

      # - redis

      - mysql

 

#   redis:

#     image: redis:6.0.8

#     ports:

#       - "6379:6379"

#     volumes:

#       - /app/redis/redis.conf:/etc/redis/redis.conf

#       - /app/redis/data:/data

#     networks: 

#       - atguigu_net

#     command: redis-server /etc/redis/redis.conf

 

  mysql:

    image: mysql:8.0.26

    environment:

      MYSQL_ROOT_PASSWORD: '123456'

      MYSQL_ALLOW_EMPTY_PASSWORD: 'no'

      MYSQL_DATABASE: 'db_cms'

      MYSQL_USER: 'root'

      MYSQL_PASSWORD: '123456'

    ports:

       - "3306:3306"

    volumes:

       - /zzmruse/mysql/data:/var/lib/mysql

       - /zzmruse/mysql/conf/my.cnf:/etc/my.cnf

       - /app/mysql/init:/docker-entrypoint-initdb.d # 这一行不知道干啥的

    networks:

      - zzmr_net

    command: --default-authentication-plugin=mysql_native_password #解决外部无法访问

 

networks: 

   zzmr_net: 
```

其实相当于执行:`docker run -d -p 8888:8888 -v/zzmruse/microService:/data --network zzme_net --name ms01 zzmr_cms_test:1.0`

> **二次修改微服务工程**

~~应该是要改很多东西,下午回来再看吧~~
不用改多少,就是把ip地址改成了服务名

1. 写yml-通过服务名访问,IP无关

```yml
#    url: jdbc:mysql://1.14.102.11:3306/db_cms?serverTimezone=UTC
    url: jdbc:mysql://mysql:3306/db_cms?serverTimezone=UTC  # 固定写死的ip改成服务名
```

2. mvn package命令将微服务形成新的jar包,并上传到linux服务器/mydocker目录下
3. 编写Dockerfile
4. 构建镜像
   - 还是之前的步骤,我把之前的mysql的容器给删了 `docker build -t zzmr_cms_test:1.1 .`

---

> **Compose常用命令**

- docker-compose -h  查看帮助
- docker-compose up  启动所有docker-compose服务
- docker-compose up -d 启动所有docker-compose服务并后台运行
- docker-compose down   停止并删除容器,网络,卷,镜像
- docker-compose exec yml里面的服务id     进入容器实例内部docker-compose exec docker-compose.yml 文件中写的服务id /bin/bash
- docker-compose ps  展示当前docker-compose 编排过的运行的所有容器
- docker-compose top  展示当前docker-compose编排过的容器进程
- docker-compose logs yml里面的服务id     查看容器输出日志
- docker-compose config    检查配置
- docker-compose config -q    检查配置,有问题才有输出(下面有演示)
- docker-compose restart   重启服务
- docker-compose start     启动服务
- docker-compose stop      停止服务

![20230313131605](https://img01.zzmr.club/img/20230313131605.png)

启动:
![20230313131824](https://img01.zzmr.club/img/20230313131824.png)

启动成功:
![20230313131846](https://img01.zzmr.club/img/20230313131846.png)

关闭也是一键:
![20230313132727](https://img01.zzmr.club/img/20230313132727.png)

那我现在是怎么搞?

把docker可视化工具一学,然后就把项目部署上线

### 2.5 轻量级可视化工具Portainer

> Portainer是一款轻量级应用,它提供了图形化界面,用于方便低管理Docker环境,包括单机环境和集群环境

**安装**

docker命令安装:`docker run -d -p 8000:8000 -p 9000:9000 --name portainer --restart=always -v/var/run/docker.sock:/var/run/docker.sock -vportainer_data:/data portainer/portainer`

安装成功:
![20230313133936](https://img01.zzmr.club/img/20230313133936.png)

第一次登录需要创建admin,访问地址:xxx.xxx.xxx.xxx:9000
设置密码:我设置的一个很复杂的
选择local选项卡后本地docker详细信息展示
![20230313134229](https://img01.zzmr.club/img/20230313134229.png)

登陆成功:
![20230313134318](https://img01.zzmr.club/img/20230313134318.png)

#### 2.5.1 Portainer基本使用

感觉挺好用的,就是全是英文

没啥难的,就是我感觉构建一个镜像好像还是挺麻烦的,一会看看吧,希望今天下午能解决这个问题

算了,后面重量级监控系统缓缓再看

先把项目部署上去再说

---

### 2.6 软工课设项目部署上线

项目上线步骤:

1. 首先将vue中的localhost换成ip地址
2. mvn打包之前,更改url的ip地址
   - ```yml
      #    url: jdbc:mysql://1.14.102.11:3306/db_cms?serverTimezone=UTC
          url: jdbc:mysql://mysql:3306/db_cms?serverTimezone=UTC  # 固定写死的ip改成服务名
     ```
3. 配置文件
   ![20230313142710](https://img01.zzmr.club/img/20230313142710.png)
4. 执行build和compose对应的命令就可以了
   1. `docker build -t zzmr_cms:1.2 .`
   2. `docker-compose up -d`

现在基本可以断定,出现问题,就是配置文件的问题

部署成功,感觉用命令行也并没有特别麻烦的

Dockerfile

```
# 基础镜像使用java

FROM java:8

# 作者

MAINTAINER zzmr

# VOLUME 指定临时文件目录为/tmp，在主机/var/lib/docker目录下创建了一个临时文件并链接到容器的/tmp

VOLUME /tmp

# 将jar包添加到容器中并更名为zzyy_docker.jar

ADD cms-0.0.1-SNAPSHOT.jar zzmr_cms.jar

# 运行jar包

RUN bash -c 'touch /zzmr_cms.jar'

ENTRYPOINT ["java","-jar","/zzmr_cms.jar"]

#暴露6001端口作为微服务

EXPOSE 8888
```

docker-compose.yml

```yml
version: "3"

services:

  microService:

    image: zzmr_cms:1.2

    container_name: ms02

    ports:

      - "8888:8888"

    volumes:

      - /zzmruse/microService:/data

    networks: 

      - zzmr_net 

    depends_on: 

      # - redis

      - mysql

 

#   redis:

#     image: redis:6.0.8

#     ports:

#       - "6379:6379"

#     volumes:

#       - /app/redis/redis.conf:/etc/redis/redis.conf

#       - /app/redis/data:/data

#     networks: 

#       - atguigu_net

#     command: redis-server /etc/redis/redis.conf

 

  mysql:

    image: mysql:8.0.26

    environment:

      MYSQL_ROOT_PASSWORD: '123456'

      MYSQL_ALLOW_EMPTY_PASSWORD: 'no'

      MYSQL_DATABASE: 'db_cms'

      MYSQL_USER: 'root'

      MYSQL_PASSWORD: '123456'

    ports:

       - "3306:3306"

    volumes:

       - /zzmruse/mysql/data:/var/lib/mysql

       - /zzmruse/mysql/conf/my.cnf:/etc/my.cnf

       - /zzmruse/mysql/init:/docker-entrypoint-initdb.d # 这一行不知道干啥的

    networks:

      - zzmr_net

    command: --default-authentication-plugin=mysql_native_password #解决外部无法访问

 

networks: 

   zzmr_net: 
```

### 2.7 暂定总结

redis集群和mysql主从复制以及最后的重量级监控没看
先去看SpringCloud了

希望早日看完SpringCloud


---

又被搞了,
数据库的密码改成rntdfk1314521

