# Docker
### Docker
基于GO语言开发，优点
1. 更高效的利用系统资源
2. 更快速的启动时间
3. 一致的运行环境
4. 持续交付和部署
5. 更轻松的迁移
6. 对比传统虚拟机：

| feature |         sss                   | VM |
|:------- |:---------------------------|:---|
|启动     |       秒级                   | 分钟级|
|硬盘占用  |  MB      | GB           |
|性能      |   接近原生系统           | 弱于|
|系统支持量 | thousands of container | dozen|

## Docker 包括三个基本概念
1. image
2. container
3. repo
***
## Image:
Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:18.04 就包含了完整的一套 Ubuntu 18.04 最小系统的 root 文件系统。
Docker 镜像 是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像 不包含 任何动态数据，其内容在构建之后也不会被改变.
***
## Container:
镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。这种特性使得容器封装的应用比直接在宿主运行更加安全。也因为这种隔离的特性，很多人初学 Docker 时常常会混淆容器和虚拟机.
***
## Repo:
镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。
一个 Docker Registry 中可以包含多个 仓库（Repository）；每个仓库可以包含多个 标签（Tag）；每个标签对应一个镜像。
安装指南：参考官网： [Docker](https://yeasy.gitbook.io/docker_practice/install#:~:text=%E5%90%84%E7%A7%8D%E7%8E%AF%E5%A2%83%E4%B8%8B%E7%9A%84-,%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97,-%EF%BC%8C%E8%BF%99%E9%87%8C%E4%B8%BB%E8%A6%81%E4%BB%8B%E7%BB%8D)
***
## 使用镜像：
Docker 运行容器前需要本地存在对应的镜像，如果本地不存在该镜像，Docker 会从镜像仓库下载该镜像。
相关命令：
1. 从 Docker 镜像仓库获取镜像的命令是 docker pull。其命令格式为：
   比如： docker pull ubuntu:18.04
运行
2. pull 过镜像之后，即可开始运行， 以ubuntu为例
  docker run -it --rm ubuntu:18.04 bash
  docker run 就是运行容器的命令，具体格式我们会在 容器 一节进行详细讲解，我们这里简要的说明一下上面用到的参数。
  -it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
  --rm：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可   以避免浪费空间。
  ubuntu:18.04：这是指用 ubuntu:18.04 镜像为基础来启动容器。
  bash：放在镜像名后的是 命令，这里我们希望有个交互式 Shell，因此用的是 bash
3. 列出镜像: docker image ls
4. 列出部分镜像: docker image 命令支持不同参数, 比如:
   | 命令                  | 显示        |
   |-----|------|
   | docker image ls ubuntu | 列出ubuntu镜像 |
   | docker image ls -q |    列出 image ID|
   |$ docker image ls --format "{{.ID}}: {{.Repository}}"  自定显示参数| 列出ID + Repo 名称 |
5. 删除 image: 
   1. 可以用镜像的完整 ID，也称为 长 ID，来删除镜像。使用脚本的时候可能会用长 ID，但是人工输入就太累了，所以更多的时候是用 短 ID 来删除镜像。docker image ls 默认列出的就已经是短 ID 了，一般取前3个字符以上，只要足够区分于别的镜像就可以了。
   2. 也可以用镜像名，也就是 <仓库名>:<标签>，来删除镜像。
6. 用 docker image ls 命令来配合
      ```
      比如，我们需要删除所有仓库名为 redis 的镜像：
      $ docker image rm $(docker image ls -q redis)
      或者删除所有在 mongo:3.2 之前的镜像：
      $ docker image rm $(docker image ls -q -f before=mongo:3.2)
      ```
7. ngix 示例
   ```
   $ docker run --name webserver -d -p 80:80 nginx
   这条命令会用 nginx 镜像启动一个容器，命名为 webserver，并且映射了 80 端口，这样我们可以用浏览器去访问这个 nginx 服务器。
   如果是在本机运行的 Docker，那么可以直接访问：http://localhost ，如果是在虚拟机、云服务器上安装的 Docker，则需要将 localhost 换为虚拟机地址或者实际云服务器地址。
   直接用浏览器访问的话，我们会看到默认的 Nginx 欢迎页面.
   假设我希望改成欢迎 Docker 的文字，我们可以使用 docker exec 命令进入容器，修改其内容。
   $ docker exec -it webserver bash
   root@3729b97e8226:/# echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
   root@3729b97e8226:/# exit
   exit
   刷新浏览器之后，内容有所改变

   原因是以上命令修改了容器的文件，也就是改动了容器的存储层。我们可以通过 docker diff 命令看到具体的改动
   $ docker diff webserver
   C /root
   A /root/.bash_history
   C /run
   C /usr
   C /usr/share
   C /usr/share/nginx
   C /usr/share/nginx/html
   C /usr/share/nginx/html/index.html
   C /var
   C /var/cache
   C /var/cache/nginx
   A /var/cache/nginx/client_temp
   A /var/cache/nginx/fastcgi_temp
   A /var/cache/nginx/proxy_temp
   A /var/cache/nginx/scgi_temp
   A /var/cache/nginx/uwsgi_temp

   现在我们定制好了变化，我们希望能将其保存下来形成镜像。
   要知道，当我们运行一个容器的时候（如果不使用卷的话），我们做的任何文件修改都会被记录于容器存储层里。而 Docker 提供了一个 docker commit 命令，可以将容器的存储层保存下来成  为镜像。换句话说，就是在原有镜像的基础上，再叠加上容器的存储层，并构成新的镜像。以后我们运行这个新镜像的时候，就会拥有原有容器最后的文件变化。
   docker commit 的语法格式为：
   docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]
   我们可以用下面的命令将容器保存为镜像：
   $ docker commit \
       --author "Tao Wang <twang2218@gmail.com>" \
       --message "修改了默认网页" \
       webserver \
       nginx:v2
   sha256:07e33465974800ce65751acc279adc6ed2dc5ed4e0838f8b86f0c87aa1795214


