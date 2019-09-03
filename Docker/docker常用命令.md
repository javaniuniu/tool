## 从容器 phpfpm 中将 PHP 源码拷贝出来
```
$ docker cp phpfpm:/app .
```
## 使用 grep 查找函数调用
```
$ grep sqrt -r app/ # 找到了 sqrt 调用
app/index.php:  $x += sqrt($x);
$ grep add_function -r app/ # 没找到 add_function 调用，这其实是 PHP 内置函数
```

## docker
-p    配置docker 到本地的端口
-itd  拉取镜像
--name 命名拉取的镜像
--network docker之间网络通讯
```
配置docker nginx端口映射本地10000端口及命名image name
$ docker run --name nginx -p 10000:80 -itd feisky/nginx
网络链接 phpfpm项目
$ docker run --name phpfpm -itd --network container:nginx feisky/php-fpm
```

## docker container run
> docker container run
说明：
创建好image后，新建容器时使用；
每次运行，都会创建一个新的容器；
新建的容器可以通过 docker container ls -a 或者 docker ps -a 查看（加 -a 表示查看所有创建好的容器）；
每次创建会返回一个【长的容器ID】；

## docker container start [containerID]
> docker container start [containerID]
说明：
只有新建好的容器（能通过命令查到的），才可以使用本命令；
使用命令的前提是该容器【已经生成】并且【已经停止运行】；
容器ID通过 docker container ls -a 或者 docker ps -a 查看；

## docker container stop [containerID]
对应 run 和 start 命令

## 镜像中 安装插件
这个命令会在docker容器中执行"apt-get install -y ping"，也就是安装一个ping命令，运行完之后容器就自动退出了
```
docker run ddfddf/tutorial apt-get install -y ping
docker commit 0299878039f0 ddfddf/ping  #0299878039f0 通过 docker ps -a 查找
```
## 复制文件到本地
```
docker cp [OPTIONS] [CONTAINER_ID]:[SRC_PATH] [DEST_PATH]
docker cp mynginx:/etc/nginx /etc/nginx
```
## 复制文件到镜像
```
docker cp /etc/nginx mynginx:/etc/nginx
```

## 从 container 创建 image
```
- 命令
  docker commit [container] [imageName]
- 实例
docker commit nginx king101125s/nginxStudy:v1
```

## push images到hub.docker.com
```
- 命令
  docker push usename/repository:TAG
- 实例
  docker push king101125s/nginxStudy:v1
```

## 终端等了 docker
```
- docker login
- docker login docker.io
- docker login hub.docker.com
```

docker top <CONTAINER_ID>     ：查看容器中运行的进程
docker diff <CONTAINER_ID>    ：查看容器中的变化
docker inspect <CONTAINER_ID> ：查看容器详细信息（输出为Json）
docker attach <CONTAINER_ID>  ：连接到启动的容器
docker logs <CONTAINER_ID>    ：输出容器日志  -f：实时输出
docker rm <CONTAINER ID>      ：删除某个Container的命令：
docker image rm <IMAGE ID>    ：删除当前的某个Image的命令: 简写方式为：docker rmi <IMAGE ID>
docker build -t debian-jdk8:v1.0 .  : debian-jdk8 镜像名称 v1.0 表示tag '.'表示当前目录下的Dockerfile
docker build https://github.com/minplemon/tool.git#:Docker/dockerfile/7.3  :（下载龟速，不知道是不是镜像资源的问题）通过 github 上的目录获 Dockerifle 文件并创建 image
docker build http://server/context.tar.gz  用给定的 tar 压缩包构建
docker save -o http://server/context.tar.gz context
docker container inspect <CONTAINER ID> :查看docker信息

docker save  -o my_ubuntu_v3.tar runoob/ubuntu:v4  保存 ubuntu 镜像到my_ubuntu_v3.tar 文件
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
docker import  my_ubuntu_v3.tar runoob/ubuntu:v4

docker run = docker create + docker start

## 参考详情
- [Docker与Dockerfile极简入门文档](./Docker与Dockerfile极简入门文档.md)
- [Docker入门教程](./Docker入门教程.md)
- [docker-image container 基本操作 -常用命令](https://www.cnblogs.com/xiadongqing/p/6144053.html)
