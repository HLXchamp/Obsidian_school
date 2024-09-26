## Docker 的安装

### Windows

接下来对 Docker 进行安装，以 Windows 系统为例，访问 Docker 的官网：

![安装 Docker](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-install-windows.png)

然后点击`Get Started`：

![安装 Docker](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-install-windows-download.png)

在此处点击`Download for Windows`即可进行下载。

如果你的电脑是`Windows 10 64位专业版`的操作系统，则在安装 Docker 之前需要开启一下`Hyper-V`，开启方式如下。打开控制面板，选择程序：

![开启 Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv.png)

点击`启用或关闭Windows功能`：

![开启 Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-enable.png)

勾选上`Hyper-V`，点击确定即可：

![开启 Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-check.png)

完成更改后需要重启一下计算机。

开启了`Hyper-V`后，我们就可以对 Docker 进行安装了，打开安装程序后，等待片刻点击`Ok`即可：

![安装 Docker](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-install.png)

安装完成后，我们仍然需要重启计算机，重启后，若提示如下内容：

![安装 Docker](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-wsl2.png)

它的意思是询问我们是否使用 WSL2，这是基于 Windows 的一个 Linux 子系统，这里我们取消即可，它就会使用我们之前勾选的`Hyper-V`虚拟机。

因为是图形界面的操作，这里就不介绍 Docker Desktop 的具体用法了。

### Mac

直接使用 Homebrew 安装即可

```
brew install --cask docker
```

### Linux

下面来看看 Linux 中如何安装 Docker，这里以 CentOS7 为例。

在测试或开发环境中，Docker 官方为了简化安装流程，提供了一套便捷的安装脚本，执行这个脚本后就会自动地将一切准备工作做好，并且把 Docker 的稳定版本安装在系统中。

```
curl -fsSL get.docker.com -o get-docker.sh
```

```
sh get-docker.sh --mirror Aliyun
```

安装完成后直接启动服务：

```
systemctl start docker
```

推荐设置开机自启，执行指令：

```
systemctl enable docker
```

## Docker 初体验

下面我们来对 Docker 进行一个初步的使用，这里以下载一个 MySQL 的镜像为例`(在CentOS7下进行)`。

和 GitHub 一样，Docker 也提供了一个 DockerHub 用于查询各种镜像的地址和安装教程，为此，我们先访问 DockerHub：[https://hub.docker.com/open in new window](https://hub.docker.com/)

![DockerHub](https://oss.javaguide.cn/github/javaguide/tools/docker/dockerhub-com.png)

在左上角的搜索框中输入`MySQL`并回车：

![DockerHub 搜索 MySQL](https://oss.javaguide.cn/github/javaguide/tools/docker/dockerhub-mysql.png)

可以看到相关 MySQL 的镜像非常多，若右上角有`OFFICIAL IMAGE`标识，则说明是官方镜像，所以我们点击第一个 MySQL 镜像：

![MySQL 官方镜像](https://oss.javaguide.cn/github/javaguide/tools/docker/dockerhub-mysql-official-image.png)

右边提供了下载 MySQL 镜像的指令为`docker pull MySQL`，但该指令始终会下载 MySQL 镜像的最新版本。

若是想下载指定版本的镜像，则点击下面的`View Available Tags`：

![查看其他版本的 MySQL](https://oss.javaguide.cn/github/javaguide/tools/docker/dockerhub-mysql-view-available-tags.png)

这里就可以看到各种版本的镜像，右边有下载的指令，所以若是想下载 5.7.32 版本的 MySQL 镜像，则执行：

```
docker pull MySQL:5.7.32
```

然而下载镜像的过程是非常慢的，所以我们需要配置一下镜像源加速下载，访问`阿里云`官网，点击控制台：

![阿里云镜像加速](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-aliyun-mirror-admin.png)

然后点击左上角的菜单，在弹窗的窗口中，将鼠标悬停在产品与服务上，并在右侧搜索容器镜像服务，最后点击容器镜像服务：

![阿里云镜像加速](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-aliyun-mirror-admin-accelerator.png)

点击左侧的镜像加速器，并依次执行右侧的配置指令即可。

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://679xpnpz.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Docker 镜像指令

Docker 需要频繁地操作相关的镜像，所以我们先来了解一下 Docker 中的镜像指令。

若想查看 Docker 中当前拥有哪些镜像，则可以使用 `docker images` 命令。

```
[root@izrcf5u3j3q8xaz ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
MySQL         5.7.32    f07dfa83b528   11 days ago     448MB
tomcat        latest    feba8d001e3f   2 weeks ago     649MB
nginx         latest    ae2feff98a0c   2 weeks ago     133MB
hello-world   latest    bf756fb1ae65   12 months ago   13.3kB
```

其中`REPOSITORY`为镜像名，`TAG`为版本标志，`IMAGE ID`为镜像 id(唯一的)，`CREATED`为创建时间，注意这个时间并不是我们将镜像下载到 Docker 中的时间，而是镜像创建者创建的时间，`SIZE`为镜像大小。

该指令能够查询指定镜像名：

```
docker image MySQL
```

若如此做，则会查询出 Docker 中的所有 MySQL 镜像：

```
[root@izrcf5u3j3q8xaz ~]# docker images MySQL
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
MySQL        5.6       0ebb5600241d   11 days ago     302MB
MySQL        5.7.32    f07dfa83b528   11 days ago     448MB
MySQL        5.5       d404d78aa797   20 months ago   205MB
```

该指令还能够携带`-q`参数：`docker images -q` ， `-q`表示仅显示镜像的 id：

```
[root@izrcf5u3j3q8xaz ~]# docker images -q
0ebb5600241d
f07dfa83b528
feba8d001e3f
d404d78aa797
```

若是要下载镜像，则使用：

```
docker pull MySQL:5.7
```

`docker pull`是固定的，后面写上需要下载的镜像名及版本标志；若是不写版本标志，而是直接执行`docker pull MySQL`，则会下载镜像的最新版本。

一般在下载镜像前我们需要搜索一下镜像有哪些版本才能对指定版本进行下载，使用指令：

```
docker search MySQL
```

![](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-search-mysql-terminal.png)

不过该指令只能查看 MySQL 相关的镜像信息，而不能知道有哪些版本，若想知道版本，则只能这样查询：

```
docker search MySQL:5.5
```

若是查询的版本不存在，则结果为空：

![](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-search-mysql-404-terminal.png)

删除镜像使用指令：

```
docker image rm MySQL:5.5
```

若是不指定版本，则默认删除的也是最新版本。

还可以通过指定镜像 id 进行删除：

```
docker image rm bf756fb1ae65
```

然而此时报错了：

```
[root@izrcf5u3j3q8xaz ~]# docker image rm bf756fb1ae65
Error response from daemon: conflict: unable to delete bf756fb1ae65 (must be forced) - image is being used by stopped container d5b6c177c151
```

这是因为要删除的`hello-world`镜像正在运行中，所以无法删除镜像，此时需要强制执行删除：

```
docker image rm -f bf756fb1ae65
```

该指令会将镜像和通过该镜像执行的容器全部删除，谨慎使用。

Docker 还提供了删除镜像的简化版本：`docker rmi 镜像名:版本标志` 。

此时我们即可借助`rmi`和`-q`进行一些联合操作，比如现在想删除所有的 MySQL 镜像，那么你需要查询出 MySQL 镜像的 id，并根据这些 id 一个一个地执行`docker rmi`进行删除，但是现在，我们可以这样：

```
docker rmi -f $(docker images MySQL -q)
```

首先通过`docker images MySQL -q`查询出 MySQL 的所有镜像 id，`-q`表示仅查询 id，并将这些 id 作为参数传递给`docker rmi -f`指令，这样所有的 MySQL 镜像就都被删除了。

## Docker 容器指令

掌握了镜像的相关指令之后，我们需要了解一下容器的指令，容器是基于镜像的。

若需要通过镜像运行一个容器，则使用：

```
docker run tomcat:8.0-jre8
```

当然了，运行的前提是你拥有这个镜像，所以先下载镜像：

```
docker pull tomcat:8.0-jre8
```

下载完成后就可以运行了，运行后查看一下当前运行的容器：`docker ps` 。

![](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-ps-terminal.png)

其中`CONTAINER_ID`为容器的 id，`IMAGE`为镜像名，`COMMAND`为容器内执行的命令，`CREATED`为容器的创建时间，`STATUS`为容器的状态，`PORTS`为容器内服务监听的端口，`NAMES`为容器的名称。

通过该方式运行的 tomcat 是不能直接被外部访问的，因为容器具有隔离性，若是想直接通过 8080 端口访问容器内部的 tomcat，则需要对宿主机端口与容器内的端口进行映射：

```
docker run -p 8080:8080 tomcat:8.0-jre8
```

解释一下这两个端口的作用(`8080:8080`)，第一个 8080 为宿主机端口，第二个 8080 为容器内的端口，外部访问 8080 端口就会通过映射访问容器内的 8080 端口。

此时外部就可以访问 Tomcat 了：

![](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-run-tomact-8080.png)

若是这样进行映射：

```
docker run -p 8088:8080 tomcat:8.0-jre8
```

则外部需访问 8088 端口才能访问 tomcat，需要注意的是，每次运行的容器都是相互独立的，所以同时运行多个 tomcat 容器并不会产生端口的冲突。

容器还能够以后台的方式运行，这样就不会占用终端：

```
docker run -d -p 8080:8080 tomcat:8.0-jre8
```

启动容器时默认会给容器一个名称，但这个名称其实是可以设置的，使用指令：

```
docker run -d -p 8080:8080 --name tomcat01 tomcat:8.0-jre8
```

此时的容器名称即为 tomcat01，容器名称必须是唯一的。

再来引申一下`docker ps`中的几个指令参数，比如`-a`：

```
docker ps -a
```

该参数会将运行和非运行的容器全部列举出来。

`-q`参数将只查询正在运行的容器 id：`docker ps -q` 。

```
[root@izrcf5u3j3q8xaz ~]# docker ps -q
f3aac8ee94a3
074bf575249b
1d557472a708
4421848ba294
```

若是组合使用，则查询运行和非运行的所有容器 id：`docker ps -qa` 。

```
[root@izrcf5u3j3q8xaz ~]# docker ps -aq
f3aac8ee94a3
7f7b0e80c841
074bf575249b
a1e830bddc4c
1d557472a708
4421848ba294
b0440c0a219a
c2f5d78c5d1a
5831d1bab2a6
d5b6c177c151
```

接下来是容器的停止、重启指令，因为非常简单，就不过多介绍了。

```
docker start c2f5d78c5d1a
```

通过该指令能够将已经停止运行的容器运行起来，可以通过容器的 id 启动，也可以通过容器的名称启动。

```
docker restart c2f5d78c5d1a
```

该指令能够重启指定的容器。

```
docker stop c2f5d78c5d1a
```

该指令能够停止指定的容器。

```
docker kill c2f5d78c5d1a
```

该指令能够直接杀死指定的容器。

以上指令都能够通过容器的 id 和容器名称两种方式配合使用。

---

当容器被停止之后，容器虽然不再运行了，但仍然是存在的，若是想删除它，则使用指令：

```
docker rm d5b6c177c151
```

需要注意的是容器的 id 无需全部写出来，只需唯一标识即可。

若是想删除正在运行的容器，则需要添加`-f`参数强制删除：

```
docker rm -f d5b6c177c151
```

若是想删除所有容器，则可以使用组合指令：

```
docker rm -f $(docker ps -qa)
```

先通过`docker ps -qa`查询出所有容器的 id，然后通过`docker rm -f`进行删除。

---

当容器以后台的方式运行时，我们无法知晓容器的运行状态，若此时需要查看容器的运行日志，则使用指令：

```
docker logs 289cc00dc5ed
```

这样的方式显示的日志并不是实时的，若是想实时显示，需要使用`-f`参数：

```
docker logs -f 289cc00dc5ed
```

通过`-t`参数还能够显示日志的时间戳，通常与`-f`参数联合使用：

```
docker logs -ft 289cc00dc5ed
```

---

查看容器内运行了哪些进程，可以使用指令：

```
docker top 289cc00dc5ed
```

若是想与容器进行交互，则使用指令：

```
docker exec -it 289cc00dc5ed bash
```

此时终端将会进入容器内部，执行的指令都将在容器中生效，在容器内只能执行一些比较简单的指令，如：ls、cd 等，若是想退出容器终端，重新回到 CentOS 中，则执行`exit`即可。

现在我们已经能够进入容器终端执行相关操作了，那么该如何向 tomcat 容器中部署一个项目呢？

```
docker cp ./test.html 289cc00dc5ed:/usr/local/tomcat/webapps
```

通过`docker cp`指令能够将文件从 CentOS 复制到容器中，`./test.html`为 CentOS 中的资源路径，`289cc00dc5ed`为容器 id，`/usr/local/tomcat/webapps`为容器的资源路径，此时`test.html`文件将会被复制到该路径下。

```
[root@izrcf5u3j3q8xaz ~]# docker exec -it 289cc00dc5ed bash
root@289cc00dc5ed:/usr/local/tomcat# cd webapps
root@289cc00dc5ed:/usr/local/tomcat/webapps# ls
test.html
root@289cc00dc5ed:/usr/local/tomcat/webapps#
```

若是想将容器内的文件复制到 CentOS 中，则反过来写即可：

```
docker cp 289cc00dc5ed:/usr/local/tomcat/webapps/test.html ./
```

所以现在若是想要部署项目，则先将项目上传到 CentOS，然后将项目从 CentOS 复制到容器内，此时启动容器即可。

---

虽然使用 Docker 启动软件环境非常简单，但同时也面临着一个问题，我们无法知晓容器内部具体的细节，比如监听的端口、绑定的 ip 地址等等，好在这些 Docker 都帮我们想到了，只需使用指令：

```
docker inspect 923c969b0d91
```

![](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-inspect-terminal.png)

## Docker 数据卷

学习了容器的相关指令之后，我们来了解一下 Docker 中的数据卷，它能够实现宿主机与容器之间的文件共享，它的好处在于我们对宿主机的文件进行修改将直接影响容器，而无需再将宿主机的文件再复制到容器中。

现在若是想将宿主机中`/opt/apps`目录与容器中`webapps`目录做一个数据卷，则应该这样编写指令：

```
docker run -d -p 8080:8080 --name tomcat01 -v /opt/apps:/usr/local/tomcat/webapps tomcat:8.0-jre8
```

然而此时访问 tomcat 会发现无法访问：

![](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-data-volume-webapp-8080.png)

这就说明我们的数据卷设置成功了，Docker 会将容器内的`webapps`目录与`/opt/apps`目录进行同步，而此时`/opt/apps`目录是空的，导致`webapps`目录也会变成空目录，所以就访问不到了。

此时我们只需向`/opt/apps`目录下添加文件，就会使得`webapps`目录也会拥有相同的文件，达到文件共享，测试一下：

```
[root@centos-7 opt]# cd apps/
[root@centos-7 apps]# vim test.html
[root@centos-7 apps]# ls
test.html
[root@centos-7 apps]# cat test.html
<h1>This is a test html!</h1>
```

在`/opt/apps`目录下创建了一个 `test.html` 文件，那么容器内的`webapps`目录是否会有该文件呢？进入容器的终端：

```
[root@centos-7 apps]# docker exec -it tomcat01 bash
root@115155c08687:/usr/local/tomcat# cd webapps/
root@115155c08687:/usr/local/tomcat/webapps# ls
test.html
```

容器内确实已经有了该文件，那接下来我们编写一个简单的 Web 应用：

```java
public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().println("Hello World!");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}
```

这是一个非常简单的 Servlet，我们将其打包上传到`/opt/apps`中，那么容器内肯定就会同步到该文件，此时进行访问：

![](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-data-volume-webapp-8080-hello-world.png)

这种方式设置的数据卷称为自定义数据卷，因为数据卷的目录是由我们自己设置的，Docker 还为我们提供了另外一种设置数据卷的方式：

```
docker run -d -p 8080:8080 --name tomcat01 -v aa:/usr/local/tomcat/webapps tomcat:8.0-jre8
```

此时的`aa`并不是数据卷的目录，而是数据卷的别名，Docker 会为我们自动创建一个名为`aa`的数据卷，并且会将容器内`webapps`目录下的所有内容复制到数据卷中，该数据卷的位置在`/var/lib/docker/volumes`目录下：

```
[root@centos-7 volumes]# pwd
/var/lib/docker/volumes
[root@centos-7 volumes]# cd aa/
[root@centos-7 aa]# ls
_data
[root@centos-7 aa]# cd _data/
[root@centos-7 _data]# ls
docs  examples  host-manager  manager  ROOT
```

此时我们只需修改该目录的内容就能能够影响到容器。

---

最后再介绍几个容器和镜像相关的指令：

```
docker commit -m "描述信息" -a "镜像作者" tomcat01 my_tomcat:1.0
```

该指令能够将容器打包成一个镜像，此时查询镜像：

```
[root@centos-7 _data]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
my_tomcat           1.0                 79ab047fade5        2 seconds ago       463MB
tomcat              8                   a041be4a5ba5        2 weeks ago         533MB
MySQL               latest              db2b37ec6181        2 months ago        545MB
```

若是想将镜像备份出来，则可以使用指令：

```
docker save my_tomcat:1.0 -o my-tomcat-1.0.tar
```

```
[root@centos-7 ~]# docker save my_tomcat:1.0 -o my-tomcat-1.0.tar
[root@centos-7 ~]# ls
anaconda-ks.cfg  initial-setup-ks.cfg  公共  视频  文档  音乐
get-docker.sh    my-tomcat-1.0.tar     模板  图片  下载  桌面
```

若是拥有`.tar`格式的镜像，该如何将其加载到 Docker 中呢？执行指令：

```
docker load -i my-tomcat-1.0.tar
```

```
root@centos-7 ~]# docker load -i my-tomcat-1.0.tar
b28ef0b6fef8: Loading layer [==================================================>]  105.5MB/105.5MB
0b703c74a09c: Loading layer [==================================================>]  23.99MB/23.99MB
......
Loaded image: my_tomcat:1.0
[root@centos-7 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
my_tomcat           1.0                 79ab047fade5        7 minutes ago       463MB
```


## 实战面试题

### 在Docker中，如何管理和查看容器日志?

在Docker中，管理和查看容器日志主要通过`docker logs`命令来实现。这个命令会显示指定容器的日志输出。详情见[在 Docker 中，如何管理和查看容器日志？](https://www.mianshiya.com/bank/1812067352871829505/question/1811361114546544641)。

### 在Docker 中，如何进行数据卷管理?

在Docker中，数据卷（Volumes）是Docker推荐的用于持久化数据的机制。数据卷管理主要包括创建、挂载、查看、备份和删除等操作。简单来说:

1. 创建数据卷：使用`docker volume create`命令。
2. 挂载数据卷：在启动容器时，用`-v`或者`--mount`选项挂载数据卷。
3. 查看数据卷：使用`docker volume ls`查看当前所有的卷。
4. 查看特定数据卷详情：使用`docker volume inspect` 。
5. 删除数据卷：停止使用该卷的容器后，使用`docker volume rm`删除数据卷。

详情见[在 Docker 中，如何进行数据卷管理？](https://www.mianshiya.com/bank/1812067352871829505/question/1811361114840145922)。

### 在Docker中，如何配置容器的网络?

我们可以通过以下几种方式配置Docker容器的网络:

1. 桥接网络(Bridge Network)：这是Docker默认使用的网络模式，会创建一个名为`bridge`的虚拟网络,所有容器默认都会连接到这个`bridge`网络上。
2. 主机网络(Host Network)：让容器和宿主机共享IP地址，如果容器需要高性能的网络通信，可以考虑使用这种模式。
3. 无网络(None Network)︰使容器没有网络接口，适用于需要完全隔离网络的场景。
4. 覆盖网络(Overlay Network)：适用于`Docker Swarm`或`Kubernetes`，这种方式可以连接多台Docker主机上的容器。
5. macvlan网络(Macvlan Network)︰容器会获取一个唯一的MAC地址，并且可以通过宿主机的物理网络接口来发送和接收数据包。

我们可以使用`docker network create`命令来创建自定义网络，然后使用`docker run --network=<network-name>`将容器连接到指定的网络。详情见[在 Docker 中，如何配置容器的网络？](https://www.mianshiya.com/bank/1812067352871829505/question/1811361115121164289)。

### 在Docker中，如何优化容器启动时间?

在Docker 中，要优化容器启动时间，可以采取以下几种措施：

1. 使用较小的基础镜像：选择精简的基础镜像，例如`alpine`，可以显著减少下载和加载时间。
2. 减少镜像层数；每一层都会增加容器启动的开销，精简 Dockerfile，合并多个`RUN`命令，将有助于减少层数。
3. 利用缓存：在构建镜像时尽量利用 Docker 的缓存功能，避免每次都重建镜像。
4. 适当配置健康检查：配置适当的健康检查策略，让容器可以尽快转为运行状态，而不是卡在启动过程中。
5. 预启动依赖服务︰提前启动容器运行所需的依赖服务，如数据库等，可降低容器启动后的等待时间。
6. 本地化镜像：将常用的容器镜像保存在本地镜像库中，避免每次启动时从远程仓库拉取。

详情见[在 Docker 中，如何优化容器启动时间？](https://www.mianshiya.com/bank/1812067352871829505/question/1811361115406376962)。

### 在Docker中，如何实现容器之间的通信?

在Docker中，容器之间的通信可以通过以下几种方式来实现:

1. 使用同一个网络：将多个容器连接到同一个 Docker 网络中，这样容器之间可以通过容器名称进行互相通信。
2. 端口映射：将容器的端口映射到宿主机的端口，通过宿主机的 IP 和映射的端口进行通信。
3) Docker Compose：使用 Docker Compose 来编排多个服务，可以为每个服务定义网络，并对网络进行配置。
4) 共享网络命名空间：通过创建共享网络命名空间的方式，使多个容器共享网络设置。

详情见[在 Docker 中，如何实现容器之间的通信？](https://www.mianshiya.com/bank/1812067352871829505/question/1811361115620286466)。

### 请解释什么是Docker Swarm,并描述其主要功能。

`Docker Swarm`是 Docker 平台内置的集群管理和编排具。它允许你将多个 Docker 主机集合成一个虚拟的 Docker 主机，从而实现容器的集群管理和自动化部署。Swarm 提供了服务发现、负载均衡、扩展和滚动更新等功能，使得集群管理变得更加简单和高效。详情见[请解释什么是 Docker Swarm，并描述其主要功能。](https://www.mianshiya.com/bank/1812067352871829505/question/1811361115830001666)

### 在Docker中，如何配置和管理环境变量?

在Docker中配置和管理环境变量有几种主要方式，这些方式可以帮助我们在不同场景下灵活使用环境变量：

1. 在 Dockerfile 中使用`ENV` 指令。
2. 通过`docker run`命令的`-e`标志传递环境变量。
3. 使用 Docker Compose 文件中的`environment` 段。
4. 在外部`.env`文件中定义环境变量，并在 Docker Compose文件中引用。

详情见[在 Docker 中，如何配置和管理环境变量？ ](https://www.mianshiya.com/bank/1812067352871829505/question/1811361116060688386)

