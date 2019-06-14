# ubuntu 下安装过程

###supervisor安装
>安装使用root权限安装
```
ubuntu安装： 
sudo apt-get install supervisor 
centos安装： 
yum install -y supervisor 
基于python库安装
pip2 install supervisor
easy_install supervisor
```

### 配置文件
安装成功后，会在/etc/supervisor目录下，生成supervisord.conf配置文件
你也可以使用echo_supervisord_conf > supervisord.conf命令，生成默认的配置文件（不建议，内容比较多）

### 配置supervisord.conf
>vi /etc/supervisord.conf
```
[inet_http_server]         ; inet (TCP) server disabled by default
port=127.0.0.1:9001        ; (ip_address:port specifier, *:port for ;all iface)
username=minp              ; (default is no username (open server))
password=123456            ; (default is no password (open server))
.....
[supervisord]
logfile=/tmp/supervisord.log    tmp为零时文件夹，会被清除，建议换个地址，/usr/local/...
.....
修改最后行
[include]
files = /etc/supervisor/conf.d/*.ini
```

### program 文件举例
>vi /etc/supervisor/conf.d/simpleserver.ini
```
[program:simpleserver]
command=python -m SimpleHTTPServer  # 执行的命令
directory=/usr/local  # 执行命令的路径
autostart = true
startsecs = 5
autorestart = true
startretries = 3
user = minp
redirect_stderr = true
stdout_logfile_backups = 20
stdout_logfile=/usr/local/var/log/simpleserver.log
stdout_logfile_maxbytes=10MB
stderr_logfile=/usr/local/var/log/simpleserver-err.log
stderr_logfile_maxbytes=10MB
loglevel=info  # 日志的级别
```

### 查看supervisorctl和启动子进程方式
```
supervisorctl status #查看supervisorctl状态
supervisorctl start nginx #启动子进程nginx
supervisorctl stop nginx  #关闭子进程nginx
supervisorctl restart nginx #重启子进程nginx
```