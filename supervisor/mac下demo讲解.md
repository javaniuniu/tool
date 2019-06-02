#supervisord mac下安装简单demo

## 安装
1. 因为是用Python实现的，所以最简单的方式是pip install supervisor
2. ubuntu系统上也可以直接使用sudo apt-get install supervisor

## 配置
```
To have launchd start supervisor now and restart at login:
  brew services start supervisor # 自动启动
Or, if you don't want/need a background service you can just run:
  supervisord -c /usr/local/etc/supervisord.ini
==> Summary # 安装位置
/usr/local/Cellar/supervisor/3.3.3: 541 files, 6.5MB
.......
```
1. 生成配置文件
```
$ echo_supervisord_conf > /etc/supervisord.conf
```
2. 指定监控文件
```
$ vi /etc/supervisord.conf
修改最后行
[include]
files = /etc/supervisord.d/*.ini
```

###  supervisord.conf 配置参考
默认的配置文件是下面这样的，但是这里有个坑需要注意，supervisord.pid 以及 supervisor.sock 是放在 /tmp 目录下，但是 /tmp 目录是存放临时文件，里面的文件是会被 Linux 系统删除的，一旦这些文件丢失，就无法再通过 supervisorctl 来执行 restart 和 stop 命令了，将只会得到 unix:///tmp/supervisor.sock 不存在的错误
```
[unix_http_server]
;file=/tmp/supervisor.sock   ; (the path to the socket file)
;修改为 /var/run 目录，避免被系统删除
file=/var/run/supervisor.sock   ; (the path to the socket file)
;chmod=0700                 ; socket file mode (default 0700)
;chown=nobody:nogroup       ; socket file uid:gid owner
;username=user              ; (default is no username (open server))
;password=123               ; (default is no password (open server))

;[inet_http_server]         ; inet (TCP) server disabled by default
;port=127.0.0.1:9001        ; (ip_address:port specifier, *:port for ;all iface)
;username=user              ; (default is no username (open server))
;password=123               ; (default is no password (open server))
...

[supervisord]
;logfile=/tmp/supervisord.log ; (main log file;default $CWD/supervisord.log)
;修改为 /var/log 目录，避免被系统删除
logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
logfile_maxbytes=50MB        ; (max main logfile bytes b4 rotation;default 50MB)
logfile_backups=10           ; (num of main logfile rotation backups;default 10)
loglevel=info                ; (log level;default info; others: debug,warn,trace)
;pidfile=/tmp/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
;修改为 /var/run 目录，避免被系统删除
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
...
;设置启动supervisord的用户，一般情况下不要轻易用root用户来启动，除非你真的确定要这么做
;user=chrism                 ; (default is current user, required if root)
...

[supervisorctl]
; 必须和'unix_http_server'里面的设定匹配
;serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
;修改为 /var/run 目录，避免被系统删除
serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket
;serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
;username=chris              ; should be same as http_username if set
;password=123                ; should be same as http_password if set
```



## 待运行任务文件配置
### 配置文件1
```
[program:simpleserver]
command=python -m SimpleHTTPServer  # 执行的命令 ，若是虚拟环境则需要注意命令的路径，见配置2
directory=/Users/minp/Downloads  # 执行命令的路径
user=minp  #  执行命令的用户
autorestart=true  # 出错后自动重启
redirect_stderr=true  # 错误日志重定向
stdout_logfile=/Users/minp/log/SimpleHTTPServer.log  # 日志的路径
loglevel=info  # 日志的级别
```
### 配置文件2
```
; 设置进程的名称，使用 supervisorctl 来管理进程时需要使用该进程名
[program:testpy] 
command=python test.py --port=9000
;numprocs=1                 ; 默认为1
;process_name=%(program_name)s   ; 默认为 %(program_name)s，即 [program:x] 中的 x
directory=/User/minp/app ; 执行 command 之前，先切换到工作目录
user=minp                 ; 使用 oxygen 用户来启动该进程
; 程序崩溃时自动重启，重启次数是有限制的，默认为3次
autorestart=true            ; 程序崩溃时自动重启
redirect_stderr=true        ; 重定向输出的日志
stdout_logfile = /var/log/supervisord/tornado_server.log
loglevel=info ;设置日志级别
```
### test.py
```
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"] # python3
```



## 启动
supervisord -c /usr/local/etc/supervisord.conf #指定文件启动

## 可能出现的错误
Unlinking stale socket /tmp/supervisor.sock
unix:///tmp/supervisor.sock no such file
unix:///var/run/supervisor.sock no such file
http://localhost:9001 refused connection
Error: Cannot open an HTTP server: socket.error reported errno.EACCES (13)

### 处理方法
经验告诉我，我用root权限就就都好了，或者用 777 修改文件写权限

## 
```
supervisorctl stop program_name  # 停止某一个进程，program_name 为 [program:x] 里的 x

supervisorctl start program_name  # 启动某个进程

supervisorctl restart program_name  # 重启某个进程

supervisorctl stop groupworker:  # 结束所有属于名为 groupworker 这个分组的进程 (start，restart 同理)

supervisorctl stop groupworker:name1  # 结束 groupworker:name1 这个进程 (start，restart 同理)

supervisorctl stop all  # 停止全部进程，注：start、restartUnlinking stale socket /tmp/supervisor.sock
、stop 都不会载入最新的配置文件

supervisorctl reload  # 载入最新的配置文件，停止原有进程并按新的配置启动、管理所有进程

supervisorctl update  # 根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受影响而重启
```

参考链接： [supervisord安装使用简记](https://www.cnblogs.com/wswang/p/5795766.html)
详细说明可参考：[Python 进程管理工具 Supervisor 使用教程](https://www.cnblogs.com/restran/p/4854623.html)
github项目参考 [mlazarov/supervisord-monitor](https://github.com/mlazarov/supervisord-monitor)
        [TAKEALOT/nodervisor](https://github.com/TAKEALOT/nodervisor)
