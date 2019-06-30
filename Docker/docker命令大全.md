# 本文是基于docker v18.03 稳定版基础来整理翻译

docker命令集
命令    说明
## 容器生命周期管理
run    创建一个新容器并运行
start    开启一个容器并使其在后台运行
stop    停止一个容器
restart    重启一个容器
kill    杀掉一个容器进程
rm    删除容器
pause    暂停容器
unpause    恢复暂停容器
create    从镜像中创建一个容器
exec    对一个容器执行shell命令
容器操作
ps    列出容器
inspect    获取容器或镜像的元数据
top    查看正在运行中的容器进程信息
attach    链接正在运行的容器
events    从docker服务器获取事件
logs    获取docker日志
wait    让一个容器进入等待，使其进入阻塞状态
export    讲一个容器的文件系统打包至tar
port    列出一个容器的端口映射情况
container    管理已经运行的容器的
deploy    部署新的堆栈或更新已有堆栈的
update    更新容器
rename    重命名容器
volume    卷管理
容器文件系统操作
commit    提交一个容器的文件系统，使之生成一个新的镜像
cp    向一个正在运行的容器复制文件，或将容器中的文件复制出来
diff    检查一个容器文件系统更改情况
镜像仓库操作
login    docker登入
logout    docker登出
pull    拉取镜像
push    推送镜像至服务器
search    在docker hub上查询镜像
镜像管理
images    列出镜像
rmi    删除镜像
tag    修改本地某一镜像的标记，使其镜像属于某一仓库
build    通过指定Dockerfile文件编译镜像
history    查看镜像历史
save    将制定镜像保存成tar文件
load    从tar中恢复镜像
import    从tar中创建一个新镜像
checkpoint    设置checkpoint，类似于恢复点，可以让镜像撤销到曾经设置的某一个checkpoint上
image    docker镜像管理
manifest    docker镜像清单管理
trust    docker可信镜像管理
集群管理
swarm    docker集群管理工具
node    docker集群节点控制
stack    docker集群堆栈管理
其他命令
info    查询docker信息
version    查询docker版本
system    docker系统管理
stats    docker容器资源使用统计
config    管理docker配置
network    docker网络管理
plugin    docker插件管理
secret    docker敏感信息管理
service    docker服务管理
run命令
docker run命令是创建一个新容器并运行，若本地不存在该容器镜像会直接去镜像仓库中查找并自动下载。

语法：

docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
1



## 命令参数说明：

参数名    默认    说明    特殊示例
–add-host        为容器添加一个host to ip的映射关系，可以添加若干个，且容器重启后不会消失。若直接修改容器中的/etc/hosts文件，容器重启后会消失    docker run –add-host localhost:127.0.0.1 ubuntu:latest
--attach(-a)        将容器的stdin,stdout,stderr【标准输入，标准输出，错误输出】关联到本地shell中，在执行docker run时，将所有输入输出指定到本地shell中，若执行时携带此参数，可以指定将stdin,stdout,stderr的某一个或某几个关联到本地shell    
--blkio-weight    0    限制容器读写权重，当宿主机有1个以上容器时，可以设置容器的读写优先权，权重值在10～1000之间，0为关闭权重（该参数默认为0）    
--blkio-weight-device    0    设置针对指定设备的权重，权重值在10～1000之间，且优先级高于blkio.weight    docker run --blkio-weight-device "/dev/sda:100" ubuntu:latest
--cap-add        增强linux能力，在docker容器内限制了大部分的linux能力，在之前，需要开启这些功能需要结合--privileged开启特权模式才能使用这些参数，考虑到安全性，可以通过该参数来开启指定的linux功能【默认开启的功能及全部定义详见docker runc】，若参数为all则默认开启所有linux能力    
--cap-drop        移除linux能力    
--cgroup-parent        配置容器的控制组，继承该控制组的资源限制模式。扩展阅读:linux cgroup    
--cidfile        创建一个容器，并将该容器的pid输出到某一文件中，若该文件存在，则会返回一个错误    docker run --cidfile /tmp/docker_test.cid ubuntu echo "test"
--cpu-count    0    设置容器cpu数量仅限Windows版本    
--cpu-percent    0    设置容器CPU使用百分比仅限Windows版本    
--cpu-period    0    与参数--cpu-quota配合使用，用于设定cpu从新分配资源的时间周期,时间周期结束后，会对cpu进行重新分配。    
--cpu-quota    0    与参数--cpu-period配合使用，用于设定该容器在资源分配周期内占用cpu的时间，若容器设定--cpu-quota=1000000 --cpu-period=500000，则该容器在这个时间周期内权重为50%，这两个参数主要是提升宿主机内某一容器的权重比，可以用来解决宿主机内若干容器的资源抢占导致重要容器cpu性能不足的场景。该模式应用于Linux 的CFS模式，扩展阅读：完全公平调度程序(CFS)    
--cpu-rt-period    0    --cpu-period的微秒版    
--cpu-rt-runtime    0    在一个cpu资源分配周期内，优先保证某容器的cpu使用的最大微秒数。例如，默认周期为 1000000 微秒（1秒），设置 --cpu-rt-runtime=950000 可确保使用实时调度程序的容器每 1000000 微秒可运行 950000 微秒，并保留至少 50000 微秒用于非实时任务。    
--cpu-shares(-c)    0    设置容器CPU权重，可以用于调整容器在单位分配周期内的资源优先使用比    
--cpus        设置容器使用cpu的数量，该参数为1.3新增参数用于快速设置容器对于cpu的占用，例如示例，可以直接设置该容器使用多少cpu    docker run --cpus=".5" ubuntu:latest
--cpuset-cpus        设置容器允许在哪个cpu上执行该进程，譬如--cpuset-cpus="1,3"为指定在cpu 1 和cpu 3上执行，--cpuset-cpus="0-2"为指定在cpu0,cpu1,cpu2上执行    docker run --cpuset-cpus="1,3" ubuntu:latest
docker run --cpuset-cpus="0-2" ubuntu:latest
--cpuset-mems        同参数--cpuset-cpus，但该参数是作用于NUMA 架构的 CPU    docker run --cpuset-mems="1,3" ubuntu:latest
docker run --cpuset-mems="0-2" ubuntu:latest
--detach(-d)    false    指定该容器运行于前台还是后台    
--detach-keys        设置容器的键盘映射键位，在容器被链接到前台时，若宿主机的键盘键位与容器键位冲突，可以使用该指令对容器的键位进行重新映射    
--device        将宿主机的设备映射至容器    
--device-cgroup-rule        将宿主机的设备添加到cgroup规则列表中    
--device-read-bps        以字节数/每秒的形式限制设备的读取速率    docker run --device-read-bps /dev/sda:100KB ubuntu:latest
--device-read-iops        以每秒所执行IO操作的次数的形式限制设备的读取速率    docker run --device-read-iops /dev/sda:10 ubuntu:latest
--device-write-bps        以字节数/每秒的形式限制设备的写入速率    
--device-write-iops        以每秒所执行IO操作的次数的形式限制设备的写入速率    
--disable-content-trust    true    忽略镜像的校验    
--dns        指定容器使用的DNS服务器，默认和宿主一致    
--dns-opt        设置DNS选项，同修改/etc/resolv.conf文件    
--dns-option        设置DNS选项，同修改/etc/resolv.conf文件    
--dns-search        指定容器DNS搜索域名，默认和宿主一致    
--entrypoint        覆盖Dockerfile中设置的entrypoint命令    
--env(-e)        设置/新增环境变量    
--env-file        读取本地环境变量文件，并加载到容器中    
--expose        开放一个端口或一组端口    
--group-add        为容器添加用户组    
--health-cmd        执行一个健康检查命令    
--health-interval    0    配合--health-cmd参数，设置健康检查的执行的间隔时间（ms | s | m | h）    
--health-retries    0    配合--health-cmd参数，设置健康检查命令失败重试的次数    
--health-start-period    0    配合--health-cmd参数，设置健康检查的启动时间（ms | s | m | h）    
--health-timeout    0    配合--health-cmd参数，设置健康检查命令超时时间（ms | s | m | h）    
--help    false    帮助    
--hostname(-h)        指定容器的hostname    
--init    false    使用tini工具在容器中新增一个守护进程，来预防该容器出现僵尸进程的可能性    
--interactive (-i)    false    以交互模式运行容器，并将容器的STDIN（标准输入）打开，通常与参数--tty (-t)配合使用    
--io-maxbandwidth    0    限制容器IO最大速率仅限Windows版本    
--io-maxiops    0    以每秒所执行IO操作的次数的形式限制设备的读取速率仅限Windows版本    
--ip        设置容器的IPv4地址    
--ip6        设置容器的IPv6地址    
--ipc        启用ipc命名空间。在docker容器中进程交互采用了Linux常见的进程间交互方法(interprocess communication - IPC), 包括信号量、消息队列和共享内存等。容器的进程间交互实际上还是host上具有相同pid命名空间中的进程间交互，因此需要在IPC资源申请时加入命名空间信息，每个IPC资源有一个唯一的32位id。    
--isolation        使用容器隔离。该参数拥有三个值
(1)default 即与使用dockerd --exec-opt的参数默认效果相同
(2)process 使用linux内核命名空间进行隔离，该参数不支持windows环境。
（3）使用微软的Hyper-V虚拟技术进行隔离，该参数仅限windows环境    
--kernel-memory    0    限制该容器内核的内存使用    
--label (-l)        设置该容器的元数据    
--label-file        通过本地文件导入元数据至该容器    
--link        指定容器间的关联，使用其他容器的IP、env等信息    
--link-local-ip        设置本地链路地址（link-local address）ip    
--log-driver        设置一个指定日志接受工具，用于动态收集日志。扩展阅读日志处理与log-driver实现    
--log-opt        配合参数--log-driver使用，用于设置日志输出参数    
--mac-address        设置该容器mac地址    
--memory -m    0    限制该容器内存使用    
--memory-reservation    0    软限制该容器的内存使用，当宿主机内存空闲时，该容器的内存使用可以一定比例超出限制，但当宿主机内存紧张时，会强制该容器内存使用限制在该参数之内    
--memory-swap    0    内存交换分区大小限制。配合参数--memory使用，且最小内存交换限制应该大于内存限制。该参数有4种情况：
(1)不设置--memory与该参数:则该容器默认可以用完宿舍机的所有内存和 宿主机 swap 分区。
(2)设置--memory 50MB 不设置--memory-swap（默认为0）:则--memory-swap值等于限制内存大小，即该容器能够申请的最大内存为100MB。
(3)设置--memory 50MB --memory-swap为-1:则该容器最大可以申请的内存为50MB+宿主机swap分区大小
(4)设置--memory 50MB --memory-swap 100MB：则该容器可以申请的最大内存为100MB-50MB=50MB    
--memory-swappiness    -1    用于调整虚拟内存的控制行为，为0～100之间的整数。在linux内存管理中，将内存中不活跃的页交换至硬盘中，以缓解内存紧张，该参数设置为0则认定该容器所有内存中的内容均不允许交换至硬盘，用以保障最大性能，若设置为100，则认为该容器所有内存中的数据均可以交换至硬盘。扩展阅读Linux内存管理    
--mount        将文件系统挂载附加到容器    
--name        设置该容器的名称    
--net        将容器连接到网络，支持bridge/host/none/container四种类型    
--net-alias        设置该容器在网络上的别名    
--network        将容器连接到网络，支持bridge/host/none/container四种类型    
--network-alias        设置该容器在网络上的别名    
--no-healthcheck    false    禁止一切健康检查行为    
--oom-kill-disable    false    设置是否禁止oom kill行为，若该容器因为需要大量请求内存，导致宿主机内存不足或触发到内存限制，导致杀死该容器进程，若设置该参数为true则会关闭这个检查    
--oom-score-adj    0    调整主机的OOM首选项（从-1000到1000）此处需要注意的是，非专业人士docker官方是不建议用户修改--oom-score-adj--oom-kill-disable这两个参数的    
--pid        自定义设置该容器的pid    
--pids-limit    0    该参数值为整数，为限制该容器所能创建的最大进程数。    
--privileged    false    在该容器上开启特权模式，让该容器拥有所有的linux能力    
--publish -p        将容器的端口映射到宿主机上    docker run -p 8000:8000 ubuntu
--publish-all (-P)    false    将该容器的所有端口均随机映射至宿主机    
--read-only    false    设置该容器只读    
--restart    no    在退出该容器时重启该容器    
--rm    false    当退出该容器时自动删除该容器资源    
--runtime        指定该容器关联一个runtime的容器，在使用该参数时注意runtime specified必须在dockerd --add-runtime注册过。扩展阅读什么是容器的runtime与Docker、Containerd、RunC之间的进程关系    
--security-opt        设置安全属性，在windows上使用CredentialSpec模块来执行身份识别。    
--shm-size    0    设置/dev/shm/目录的大小    
--sig-proxy    true    代理进程所接收的所有字符,当指定--sig-proxy=false时，ctrl+c和ctrl+d 不会传递信号给docker进程而关闭容器。    
--stop-signal    SIGTERM    停止带有信号的容器，在linux环境下输入kill -l,就可以看到所有信号名称，可以指定容器发出某种信号时停止该容器，譬如SIGKILL    
--stop-timeout    0    设置容器调用命令超时后自动退出。该参数可以设置容器在调用命令时导致超时后多少秒退出，0为永远不退出，该参数单位为秒    
--storage-opt        设定该容器的存储空间，可以分别指定dm.basesize、dm.loopdatasize、dm.loopmetadatasize等项,指定单个容器可用数据空间、docker可用数据空间、元数据可用数据空。    docker run --storage-opt dm.basesize=20G ubuntu
--sysctl    map[]    修改内核参数，对应修改容器中的/etc/sysctl.conf文件    
--tmpfs        指定挂载一个tmpfs目录，tmpfs是一种虚拟内存文件系统。可以不经由镜像直接创建一个容器    docker run -d --tmpfs /run:rw,noexec,nosuid,size=65536k my_image
--tty (-t)    false    为容器重新分配一个伪输入终端，通常与参数--interactive (-i)同时使用    
--ulimit        设置容器的ulimit选项，扩展阅读ulimit命令    
--user (-u)        在该容器下添加新用户    
--userns        指定该容器运行在指定host user namespace中    
--uts        使用uts命名空间。扩展阅读Linux命名空间入门    
--volume (-v)        在该容器下挂载新卷    
--volume-driver        挂载一个卷容器，该卷可以是一个本地共享卷或者一个远程服务，配合docker volume create命令创建一个卷    
--volumes-from        可以将任意文件夹反挂载到卷中，从而快速实现对该容器的备份与迁移。    docker run --rm --volumes-from dbstore -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata
--workdir (-w)        指定容器的工作目录与Dockerfile中的WORKDIR作用相同    
start命令
docker start是用于启动容器的命令

语法：

docker start [OPTIONS] CONTAINER [CONTAINER...]
1



## 命令参数说明：

参数名    默认    说明    特殊示例
--attach（-a）            
--checkpoint            
--checkpoint-dir            
--detach-keys            
--interactive（-i）            
dockerd命令集
---------------------
## 参考文档
[docker命令行大全详解](https://blog.csdn.net/talkxin/article/details/83061973
