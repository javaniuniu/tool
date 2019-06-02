
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
tail -n 1000：显示最后1000行
```

### 查端口号
```
netstat -tunlp |grep 端口号
netstat -tunlp  查所有端口
```

### Mac 上传文件到Linux。
```
scp -r /Users/minp/PycharmProjects/flask_v1 root@192.168.2.115:mysite
```