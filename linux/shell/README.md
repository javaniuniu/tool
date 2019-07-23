### 查看终端进程 查看全部进程
```
$ ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root     12392 12391  0 7月02 pts/2   00:00:00 bash
eubc     12694  9514  0 7月03 pts/3   00:00:00 bash
root     12702 12694  0 7月03 pts/3   00:00:00 sudo su

$ ps -aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      1583  0.0  0.2 1329376 22868 ?       Ssl  7月01   1:16 /usr/bin/containerd
root      1601  0.0  0.0  72296  2848 ?        Ss   7月01   0:00 /usr/sbin/sshd -D
root      1633  0.0  0.0 308296  5388 ?        Ssl  7月01   0:00 /usr/sbin/gdm3

$ ps -ef | grep mongodb     #通过管道 ‘|’ 查找包含特定字符串的进程
root     24238  9401  0 7月04 ?       00:05:04 /usr/local/mongodb/bin/mongod --port 27020 --dbpath=/www/mongoDB/shard/s0 --logpath=/www/mongoDB/shard/log/s0.log --logappend --fork
root     24276  9401  0 7月04 ?       00:05:02 /usr/local/mongodb/bin/mongod --port 27021 --dbpath=/www/mongoDB/shard/s1 --logpath=/www/mongoDB/shard/log/s1.log --logappend --fork
root     24302  9401  0 7月04 ?       00:05:03 /usr/local/mongodb/bin/mongod --port 27022 --dbpath=/www/mongoDB/shard/s2 --logpath=/www/mongoDB/shard/log/s2.log --logappend --fork
root     24330  9401  0 7月04 ?       00:05:03 /usr/local/mongodb/bin/mongod --port 27023 --dbpath=/www/mongoDB/shard/s3 --logpath=/www/mongoDB/shard/log/s3.log --logappend --fork

```
### 查看终端进程 通过端口号查询
```
$ sudo lsof -i :9000

COMMAND   PID    USER   FD      TYPE             DEVICE                      SIZE/OFF      NODE       NAME
java              716      a           313u   IPv6               0x1111111111111     0t0                    TCP        *:cslistener (LISTEN)
```
### 根据PID杀进程
```
$ sudo kill -9 716
```

查看实时日志
```
tail -f test.log
tail -n 1000            显示最后1000行
```

### 查端口号
```
netstat -tunlp |grep    端口号
netstat -tunlp          查所有端口
```

### Mac 上传文件到Linux。
```
scp -r /Users/minp/PycharmProjects/flask_v1 root@192.168.2.115:mysite
```

### 查看端口
```
$ netstat -apn | grep 8000
```
### 软件安装
```
apt install vim 安装vim
```

### 立刻关机
```
poweroff 立刻关机
```

### SSH上传本地文件到linux服务器
在linux下一般用scp这个命令来通过ssh传输文件。

1. 从服务器上下载文件
>scp username@servername:/path/filename /var/www/local_dir（本地目录）

例如scp root@192.168.0.101:/var/www/test.txt  #把192.168.0.101上的/var/www/test.txt 的文件下载到/var/www/local_dir（本地目录）

2. 上传本地文件到服务器
>scp /path/filename username@servername:/path

例如scp /var/www/test.php root@192.168.0.101:/var/www/  #把本机/var/www/目录下的test.php文件上传到192.168.0.101这台服务器上的/var/www/目录中

3. 从服务器下载整个目录
>scp -r username@servername:/var/www/remote_dir/（远程目录） /var/www/local_dir（本地目录）

例如:scp -r root@192.168.0.101:/var/www/test  /var/www/

4. 上传目录到服务器
>scp  -r local_dir username@servername:remote_dir
例如：scp -r test  root@192.168.0.101:/var/www/   #把当前目录下的test目录上传到服务器的/var/www/ 目录

注：目标服务器要开启写入权限。


### 查看文件大小及数量
```
du -sh /data/*
ls /data |wc -w
```

## 参考文档
[Linux入门](http://c.biancheng.net/view/2943.html)
