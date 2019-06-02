# program自用配置

```
[program:simpleserver]
command=python -m SimpleHTTPServer      # 执行的命令
directory=/Users/minp/Downloads         # 执行命令的路径
priority  = 1                           # 权重为1 ，第一个启动
autostart = true                        # 跟着supervisor 一起启动
startsecs = 5
autorestart = true
startretries = 3
user = minp
redirect_stderr = true
stdout_logfile_backups = 20
stdout_logfile=/usr/local/var/log/simpleserver.log      #.log 该文件没有泽启动后自动生成
stdout_logfile_maxbytes=10MB
stderr_logfile=/usr/local/var/log/simpleserver-err.log  # .log 该文件没有泽启动后自动生成
stderr_logfile_maxbytes=10MB
loglevel=info                           # 日志的级别
```