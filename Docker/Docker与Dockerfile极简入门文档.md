# Docker与Dockerfile极简入门文档
## docker的安装

docker的安装我就不多说，建议按照docker的官方安装文档一步一步来https://docs.docker.com/install/。需要注意的是，Docker的windows版本只支持Win10的专业版和企业版，像我们普通学生用的Win10家庭版它是不支持的，但是docker官方也提供一个docker toolbox（下载地址：https://docs.docker.com/toolbox/toolbox_install_windows/），通过它可以在windows上运行docker，只不过这个docker toolbox是基于一个linux虚拟机运行的，所以性能肯定是不如原生的好。

安装好docker后，不要忘了用service docker start或者systemctl start docker（如果是centos7以上版本的话）启动docker的服务

## 配置docker不需要sudo

刚装好的docker每次使用docker命令都需要sudo，比较麻烦，可以通过以下命令省去sudo：
```
sudo usermod -aG docker 当前用户名
```
然后一定要记得重新登录该用户才能生效。

## docker核心概念

要理解docker，最核心的是理解三个概念，分别是：仓库（Registry）、镜像（image）和容器（Container）。我们结合一些常见的Docker命令对这些概念进行讲解。

## 仓库（Registry）

所谓仓库，其实是个镜像仓库，里面有很多别人已经打包好的镜像，可以直接使用docker pull命令将仓库中的镜像拉到本地，默认的仓库Docker的官方仓库Docker Hub Registry。

因为墙的缘故，官方仓库的速度会比较慢，可以配一个官方的中国加速镜像，方法是：修改 /etc/docker/daemon.json，加上如下的键值:
```
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```
之后重启docker服务即可生效。

使用以下命令可以去仓库中搜索含有tutorial关键字的镜像：
```
docker search tutorial
```
输出如下：
```
NAME                                          DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
learn/tutorial                                                                                38
georgeyord/reactjs-tutorial                   This is the backend of the React comment box…   5                      [OK]
chris24walsh/flask-aws-tutorial               Runs a simple flask webapp demo, with the op…   1                      [OK]
......(以下省略)
```
可以使用docker pull命令从仓库中拉取刚刚搜索到的镜像到本地，这里为了避免learn/tutorial那个镜像被取消，我自己重建了一个一样的，这里拉取我创建的那个镜像：
```
docker pull ddfddf/tutorial
```
输出如下：
```
Using default tag: latest
latest: Pulling from ddfddf/tutorial
271134aeb542: Pull complete
483756d37259: Pull complete
Digest: sha256:4615baf428c61a6feafd8f77f91e0cadc7f8f080710cc82362ac89a5f66a329c
Status: Downloaded newer image for ddfddf/tutorial:latest
```

ddfddf/tutorial为刚刚搜索到镜像名，默认情况下会拉去最新版本的镜像，即ddfddf/tutorial:latest，你也可以用:指定某个特定版本的镜像，比如docker pull ubuntu:14.04。其实冒号后面的这个东西的官方术语叫做tag，这个tag可以是一串数字（比如unbantu:14.04的tag是14.04），也可以是一个单词（比如debian:stretch的tag是stretch），latest也只是一个普通的tag而已，只是当docker pull不去专门指定tag时，默认会去下载tag为latest的镜像，通过在Docker Hub Registry中搜索你想要的镜像，点进去之后可以查看到有哪些tag可以提供下载，如下图：

![1](./images/20190222194941611.png)

## 镜像（Image）

通过docker images命令可以看到本地已有的镜像：
```
REPOSITORY                              TAG                 IMAGE ID            CREATED             SIZE
ddfddf/tutorial                         latest              48a0196af3c3        3 hours ago         140MB
```

每个镜像都有一个IMAGE ID作为唯一标识，可以看出这个镜像的IMAGE ID为48a0196af3c3，使用镜像的id可以将它删除，命令如下：
```
docker rmi 镜像id
```
这里先不要着急把刚刚拉下来的镜像删掉，待会实验还要用。

## 容器（Container）

然后使用docker run来运行这个镜像（运行之后镜像就变成一个容器）：
```
docker run ddfddf/tutorial apt-get install -y ping
```
这个命令会在docker容器中执行"apt-get install -y ping"，也就是安装一个ping命令，运行完之后容器就自动退出了。之后使用docker ps命令可以查看所有当前正在运行的容器，输出如下：
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
空的，没有任何一个容器正在运行，之所以会这样是因为刚刚容器执行完命令后就退出了。使用docker ps -a命令可以查看到所有容器，不管它正在运行还是已经退出，输出如下：
```
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS                      PORTS               NAMES
0299878039f0        ddfddf/tutorial       "apt-get install -y …"   1 minutes ago      Exited (0) 11 minutes ago                       peaceful_wozniak
```
每个容器也会有一个ID作为唯一标识，从上面的输出中可以看出CONTAINER ID是0299878039f0.

我们尝试一下如下的命令：
```
docker run ddfddf/tutorial ping www.baidu.com
```
这个命令会报如下的错误：
```
docker: Error response from daemon: OCI runtime create failed: container_linux.go:348: starting container process caused "exec: \"ping\": executable file not found in $PATH": unknown.
```
从"ping": executable file not found in $PATH这句话中可以看出是因为ping命令没有装导致的，* 刚刚明明装过ping命令了，为什么没有呢？这是因为在容器中的修改并不会影响镜像，通过docker commit命令可以将这个容器提交成一个新的镜像 * 。命令如下：
```
docker commit 0299878039f0 ddfddf/ping
```
命令中0299878039f0是之前用docker ps -a查询出来的容器ID。

试一下我们新提交的镜像learn/ping:
```
docker run ddfddf/ping ping www.baidu.com
```
输出如下：
```
PING www.a.shifen.com (111.13.100.92) 56(84) bytes of data.
64 bytes from promote.cache-dns.local (111.13.100.92): icmp_req=1 ttl=127 time=60.3 ms
64 bytes from promote.cache-dns.local (111.13.100.92): icmp_req=2 ttl=127 time=61.8 ms
......(以下省略)
```
可见ping命令可以使用了。在上面这个现象的背后，容器其实只是在镜像上面添加一个可写层，每当对这个容器进行修改都会在可写层标明与原本镜像的不同之处，当你使用docker commit命令时，只是提交了一个可写层，将它变成一个不可写的镜像层，而这个新的镜像和原本的镜像共享原本镜像的所有层，这就是所谓的docker分层机制，其实每个docker镜像都是由好多层构成的，这个机制能极大地缩小镜像占用的硬盘空间，如下图：

![2](./images/20190222195112903.png)

刚刚我们运行ping命令的时候都是在前台运行的，在它运行的时候我们命令行做不了别的事情，只能眼睁睁地看它输出，使用如下命令可以让它后台运行：
```
docker run -d ddfddf/ping ping www.baidu.com
```
这个时候在控制台就看不到它的输出，使用docker ps可以看到这个容器的id，它是目前唯一正在运行的容器，如果想看它的输出的话可以使用docker logs 容器ID的方式查看。

使用docker stop命令可以停止这个后台运行的容器：
```
docker stop 2f371d67d92d
```
2f371d67d92d为这个容器在我的电脑上的ID

如果你想继续让这个容器运行，可以使用docker start命令：
```
docker start 2f371d67d92d
```
这个容器又会从刚才停止的地方重新运行，在初学的时候总是会搞混docker start与docker run命令，其实在理解了容器与镜像的区别之后，就很容易理解了，docker run使用来启动镜像的，而docker start是用来重新启动被停止的容器的。
只要镜像被执行了一次啊，都会生成一个新的容器，所以此时用docker -a会看到好多废弃的容器，可以使用docker rm 容器ID的方式将他们删除掉，也可以使用如下的小技巧一次性删除所有容器：
```
docker rm $(docker ps -a -q)
```
不用担心，这里只是删除所有容器而已，镜像还完好无损的保留在那里。

这里把容器和镜像容易混淆的命令总结在了下表中：

      删除	       启动
镜像	docker rmi	docker run
容器	docker rm	  docker start
上文中一直使用容器ID来标明容器，其实在启动的时候可以通过–name选项来指定一个别名，之后就可以使用这个别名来代替容器ID使用，注意这个别名必须在本台机器中唯一，示例如下：
```
docker run -d --name=test ddfddf/ping ping www.baidu.com
docker stop test
docker rm test
```
上面的命令中给容器起了一个叫做test的别名，然后使用它的别名将其停止与删除。

如果有些情况下不得已要进入容器内部进行操作的话，可以使用如下命令进入容器内部的shell：
```
docker run -d --name=test ddfddf/tutorial ping www.baidu.com
docker exec -ti test /bin/bash
```
docker exec用于在正在运行的容器中执行命令，-ti选项表示分配一个虚拟终端。注意docker exec只能在“正在运行”的容器中执行命令，所以在docker run的时候执行ping就是为了让这个容器一直运行，而不是立即退出。

## 再聊聊仓库（Registry）

之前讲过，Docker Hub Registry是Docker的官方仓库，其实这是一个有点类似于Github的地方，任何人都可以在上面提交与下载镜像，我们可以先去上面注册一个账户（地址：https://hub.docker.com/），注册账户时会有谷歌的人机认证系统，所以需要一些科学上网技巧。
注册完成后可以用如下命令在shell中登录：
```
docker login -u 用户名 -p 密码
```
在将镜像push到自己新建的账户之前，要用docker tag重命名一下，将镜像命名你的用户名/镜像名这种形式，不然会push认证不通过，代码如下：
```
docker tag ddfddf/tutorial 你的用户名/tutorial
```
docker tag并没有干太多的事情，只是创建了一个到ddfddf/tutorial镜像的引用。之后就可以使用docker push命令将自己的镜像推送到账户中去了，方便自己和别人的使用：
```
docker push 你的用户名/tutorial
```
搞定之后就可以使用以下命令登出了：
```
docker logout
```
登录上Docker Hub，你将能看到刚刚你push上去的镜像。

总结

最后我再总结一下Docker的三个核心概念间的关系：

![3](./images/20190222195156153.png)

## Dockerfile

如果你想要从一个基础镜像开始建立一个自定义镜像，可以选择一步一步进行构建，也可以选择写一个配置文件，然后一条命令（docker build）完成构建，显然配置文件的方式可以更好地应对需求的变更，这个配置文件就是Dockerfile。
学习Dockerfile的最好方式就是阅读别人写的Dockerfile，遇到不会的指令就查一查Dockerfile的文档，文档地址如下：
https://docs.docker.com/engine/reference/builder/
大家遇到不知道的指令多去里面翻一翻，下面我带着大家读几个开源Dockerfile，在读的过程中学习相关知识。

第一个Dockerfile以阿里中间件大赛给的debian-jdk8镜像为例，Dockerfile文件如下：
```
FROM debian:stretch

ARG DEBIAN_FRONTEND=noninteractive
ARG JAVA_VERSION=8
ARG JAVA_UPDATE=172
ARG JAVA_BUILD=11
ARG JAVA_PACKAGE=jdk
ARG JAVA_HASH=a58eab1ec242421181065cdc37240b08

ENV LANG C.UTF-8
ENV JAVA_HOME=/opt/jdk
ENV PATH=${PATH}:${JAVA_HOME}/bin

RUN set -ex \
 && apt-get update \
 && apt-get -y install ca-certificates wget unzip \
 && wget -q --header "Cookie: oraclelicense=accept-securebackup-cookie" \
         -O /tmp/java.tar.gz \
         http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD}/${JAVA_HASH}/${JAVA_PACKAGE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz \
 && CHECKSUM=$(wget -q -O - https://www.oracle.com/webfolder/s/digest/${JAVA_VERSION}u${JAVA_UPDATE}checksum.html | grep -E "${JAVA_PACKAGE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64\.tar\.gz" | grep -Eo '(sha256: )[^<]+' | cut -d: -f2 | xargs) \
 && echo "${CHECKSUM}  /tmp/java.tar.gz" > /tmp/java.tar.gz.sha256 \
 && sha256sum -c /tmp/java.tar.gz.sha256 \
 && mkdir ${JAVA_HOME} \
 && tar -xzf /tmp/java.tar.gz -C ${JAVA_HOME} --strip-components=1 \
 && wget -q --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
         -O /tmp/jce_policy.zip \
         http://download.oracle.com/otn-pub/java/jce/${JAVA_VERSION}/jce_policy-${JAVA_VERSION}.zip \
 && unzip -jo -d ${JAVA_HOME}/jre/lib/security /tmp/jce_policy.zip \
 && rm -rf ${JAVA_HOME}/jar/lib/security/README.txt \
       /var/lib/apt/lists/* \
       /tmp/* \
       /root/.wget-hsts
```

在解释含义之前，我们先体验一下如何用Dockerfile打包一个镜像，新建一个空目录，假设就是~/debian-jdk8吧，cd进这个目录，新建一个Dockerfile，然后把上面的内容copy进去，然后执行下面的命令：
```
docker build -t debian-jdk8:v1.0 .
```
其中-t debian-jdk8:v1.0表示打包的镜像名为debian-jdk，tag为v1.0（前面说过，tag是可以任意命名的，不一定要是这种格式），注意命令的最后有一个.，这个表示打包的上下文（其实就是Dockerfile所在目录）是在当前目录，然后目录下的Dockerfile就会被编译执行。

执行完毕后运行docker images就会发现多了一个debian-jdk8镜像。

下面来解释一下Dockerfile的结构，那些字母全部大写的每行第一个单词都是Dockerfile的指令，可以看出这个Dockefile中包括的指令有FROM、ARG、ENV、RUN，下面的表格中我对其含义进行了解释：

指令	含义解释
FROM	FROM debian:stretch表示以debian:stretch作为基础镜像进行构建
RUN	  可以看出RUN后面跟的其实就是一些shell命令，通过&&将这些脚本连接在了一行执行，这么做的原因是为了减少镜像的层数，每多一行RUN都会给镜像
      增加一层，所以这里选择将所有命令联结在一起执行以减少层数
ARG	  特地将这个指令放在RUN之后讲解，这个指令可以进行一些宏定义，比如我定义ENV JAVA_HOME=/opt/jdk，之后RUN后面的shell命令中的${JAVA_HOME}
      都会被/opt/jdk代替
ENV	  可以看出这个指令的作用是在shell中设置一些环境变量（其实就是export）

### 多阶段构建（multi-stage build）

这个功能是在Docker17.05版本及以上才出现的，主要用于解决docker镜像构建的中间冗余文件的处理，这里只做简要介绍，更详细的解释请看官方文档：
https://docs.docker.com/develop/develop-images/multistage-build/

有的时候会看见一些古怪，里面会有多个FROM指令或者FROM...AS...指令（这都是多阶段构建的标志），比如如下的dockerfile（来源于阿里中间件大赛的agent-demp，完整项目请见：https://github.com/DQinYuan/Agent-demo）：
```
# Builder container
FROM registry.cn-hangzhou.aliyuncs.com/aliware2018/services AS builder

COPY . /root/workspace/agent
WORKDIR /root/workspace/agent
RUN set -ex && mvn clean package



# Runner container
FROM registry.cn-hangzhou.aliyuncs.com/aliware2018/debian-jdk8

COPY --from=builder /root/workspace/services/mesh-provider/target/mesh-provider-1.0-SNAPSHOT.jar /root/dists/mesh-provider.jar
COPY --from=builder /root/workspace/services/mesh-consumer/target/mesh-consumer-1.0-SNAPSHOT.jar /root/dists/mesh-consumer.jar
COPY --from=builder /root/workspace/agent/mesh-agent/target/mesh-agent-1.0-SNAPSHOT.jar /root/dists/mesh-agent.jar

COPY --from=builder /usr/local/bin/docker-entrypoint.sh /usr/local/bin
COPY start-agent.sh /usr/local/bin

RUN set -ex && mkdir -p /root/logs

ENTRYPOINT ["docker-entrypoint.sh"]
```

如果你懒得点进去我刚刚给出的完整项目地址，看下面的截图也大概知道是什么样子了：

![4](./images/20190222195248955.png)

可以看出这是一个Java的Maven项目。

先解释几个之前没解释过的指令：

指令	       含义解释
FROM…AS…	  这是Docker 17.05及以上版本新出来的指令，其实就是给这个阶段的镜像起个别名：FROM ...(基础镜像) AS ...(别名)，在后面引用这个
            阶段的镜
            像时直接使用别名就可以了
COPY	      顾名思义，就是用来来回复制文件的，COPY . /root/workspace/agent表示将当前文件夹（.表示当前文件夹，即Dockerfile所在文件夹）的
            所以文件拷贝到容器的/root/workspace/agent文件夹中。通过--from参数也可以从前面阶段的镜像中拷贝文件过来，比如--from=builder表示文件来源不是本地文件系统，而是之前的别名为builder的容器
WORKDIR	    在执行RUN后面的shell命令前会先cd进WORKDIR后面的目录
ENTRYPOINT	这个参数表示镜像的“入口”，镜像打包完成之后，使用docker run命令运行这个镜像时，其实就是执行这个ENTRYPOINT后面的可执行文件（一般是一个shell脚本文件），也可以通过["可执行文件", "参数1", "参数2"]这种方式来赋予可执行文件的执行参数，这个“入口”执行的工作目录也是WORKDIR后面的那个目录
知道上面这些指令后尝试阅读一下之前给出的Dockerfile。按照官方文档的说法，多阶段构建中每一个FROM指令表示开始一个阶段，第一阶段从第一个FROM开始，在第二个FROM之前结束，片段如下：
```
# Builder container
FROM registry.cn-hangzhou.aliyuncs.com/aliware2018/services AS builder

COPY . /root/workspace/agent
WORKDIR /root/workspace/agent
RUN set -ex && mvn clean package
```
可以看出第一阶段做的事情其实就是把项目拷贝到镜像（COPY . /root/workspace/agent）中用maven打包(mvn clean package)一下。

第二阶段的片段如下：
```
# Runner container
FROM registry.cn-hangzhou.aliyuncs.com/aliware2018/debian-jdk8

COPY --from=builder /root/workspace/services/mesh-provider/target/mesh-provider-1.0-SNAPSHOT.jar /root/dists/mesh-provider.jar
COPY --from=builder /root/workspace/services/mesh-consumer/target/mesh-consumer-1.0-SNAPSHOT.jar /root/dists/mesh-consumer.jar
COPY --from=builder /root/workspace/agent/mesh-agent/target/mesh-agent-1.0-SNAPSHOT.jar /root/dists/mesh-agent.jar

COPY --from=builder /usr/local/bin/docker-entrypoint.sh /usr/local/bin
COPY start-agent.sh /usr/local/bin

RUN set -ex && mkdir -p /root/logs

ENTRYPOINT ["docker-entrypoint.sh"]
```

这个阶段的核心就是大量的COPY指令，从上一阶段别名为builder的镜像中拷贝一些执行文件过来。

这两个阶段构建结束后，第一阶段生成的镜像会被抛弃，只有最后一个阶段构建的镜像会被构建成为最终的镜像。

因为在第二阶段中只是拷贝了一些必要的执行文件过去，第一阶段在maven打包过程中产生的大量冗余文件就被抛弃了，所以说善于使用多阶段构建可以大大减小镜像的大小。

更多的关于Dockerfile的最佳实践请参考官网的一篇文章：
https://docs.docker.com/develop/develop-images/dockerfile_best-practices/


---------------------
## 参考文档
[Docker与Dockerfile极简入门文档](https://blog.csdn.net/qq_33256688/article/details/80319673)
[dockerinfo](http://www.dockerinfo.net/document)
