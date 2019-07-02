
### 查看终端进程
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
scp username@servername:/path/filename /var/www/local_dir（本地目录）

例如scp root@192.168.0.101:/var/www/test.txt  #把192.168.0.101上的/var/www/test.txt 的文件下载到/var/www/local_dir（本地目录）

2. 上传本地文件到服务器
scp /path/filename username@servername:/path

例如scp /var/www/test.php root@192.168.0.101:/var/www/  #把本机/var/www/目录下的test.php文件上传到192.168.0.101这台服务器上的/var/www/目录中

3. 从服务器下载整个目录
scp -r username@servername:/var/www/remote_dir/（远程目录） /var/www/local_dir（本地目录）

例如:scp -r root@192.168.0.101:/var/www/test  /var/www/

4. 上传目录到服务器
scp  -r local_dir username@servername:remote_dir
例如：scp -r test  root@192.168.0.101:/var/www/   #把当前目录下的test目录上传到服务器的/var/www/ 目录

 
注：目标服务器要开启写入权限。
