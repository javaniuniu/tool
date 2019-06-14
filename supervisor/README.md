# supervisor 终端操作帮助文档

## Supervisor介绍
>*Supervisor* (http://supervisord.org) 是一个用Python写的进程管理工具，可以很方便的用
>来启动、重启、关闭进程（不仅仅是Python进程）。除了对单个进程的控制，还可以同时启动、关闭多
>个进程，比如很不幸的服务器出问题导致所有应用程序都被杀死，此时可以用 supervisor同时启动所
>有应用程序而不是一个一个地敲命令启动。Supervisor是一款Linux下的进程管理软件，最主要的两个
>功能是：
>1）将非daemon程序变成deamon方式运行，对于daemon程序则不能监控。
>2）对程序进行监控，当程序退出时，可以自动拉起程序。


## 基本说明
```
supervisor                                      supervisord，运行 Supervisor 时会启动一个进程 supervisord，它负责启动所管理的进程，并将所管理的进程作为自己的子进程来启动，而且可以在所管理的进程出现崩溃时自动重启
supervisorctl                                   client端 是命令行管理工具，可以用来执行 stop、start、restart 等命令，来对这些子进程进行管理
/etc/supervisord.conf                           配置文件默认地址
supervisord                                     启动默认为默认配置
supervisord -c /usr/local/etc/supervisord.conf  指定配置路径启动  建议使用
```



## supervisor 常用操作命令
```
supervisorctl help              查看帮助文档
supervisorctl status            查看任务状态
supervisorctl reload            重新加载

ps -aux |grep supervisord       查看进程
```


## 注意事项
1. 安装配置完成后，易出现权限问题（一开始不懂，花了大半时间处理因权限造成的报错）
```
$ sudo chmod -R 777 某一目录
```

2. 或者使用root权限 安装和操作supervisord

#注意
Supervisor只能管理非daemon的进程，也就是说Supervisor不能管理守护进程。否则提示Exited too quickly (process log may have details)异常。例子中的Tomcat默认是以守护进程启动的，所以我们改成了catalina.sh run，以前台进程的方式运行


