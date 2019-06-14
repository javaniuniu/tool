# 常见报错

## 注意事项
安装使用root权限安装，不然容易报一些错
如出现权限问题可以使用下面命令
```
$ sudo chmod 777 /var/run
$ sudo chmod 777 /etc/supervisor
```

## 1.
```
Traceback (most recent call last):
  File "/usr/local/bin/supervisorctl", line 7, in <module>
    from supervisor.supervisorctl import main
  File "/usr/local/lib/python2.7/dist-packages/supervisor/supervisorctl.py", line 36, in <module>
    from supervisor.options import ClientOptions
  File "/usr/local/lib/python2.7/dist-packages/supervisor/options.py", line 26, in <module>
    from supervisor.datatypes import process_or_group_name
ImportError: cannot import name process_or_group_name #这是在执行supervisor相关命令报的错，基本可以看出是安装的问题 
#解决方式 
easy_install  supervisor
```

## 2.
```
root@ubuntu:~# supervisorctl
unix:///var/run/supervisor.sock no such file
supervisor> 
#这有可能是由于supervisor服务没有启动 #解决办法
service  supervisor  start
```

## 3.
```
error: <class ‘socket.error’>, [Errno 2] No such file or directory: file:/usr/lib/python2.7/socket.py line: 224

#错误原因可能是 supervisor 服务未启动 #解决办法   service  supervisor  start 

#也有可能是配置文件中没有指定用户 #解决办法 
进入/etc/supervisor/conf.d
找到指定的配置文件，加入  user=root
```

## 4.
```
Supervisor只能管理非daemon的进程，也就是说Supervisor不能管理守护进程。否则提示Exited too quickly (process log may have details)异常。例子中的Tomcat默认是以守护进程启动的，所以我们改成了catalina.sh run，以前台进程的方式运行
```

