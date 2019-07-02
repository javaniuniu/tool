## uwsgi命令转成配置件
$ sudo uwsgi --http 0.0.0.0: 8000 --wsgi-file server.py --master --processes 4 --threads 3  --stats 127.0.0.1:9191    0.0.0.0可以省略
转化成uwsgi.ini 配置文件
```
[uwsgi]
socket=0.0.0.0:8000
wsgi-file=server.py
master=true
processes=4
threads=2
stats=127.0.0.1:9191

```

### uwgi配置说明
```
[uwsgi]
chmod-socket=660 #在linux下，socket的启动方式，套接字类似文件，你必须保证有权限去读取它
socket=0.0.0.0:8000
wsgi-file=server.py
callable = app
master=true
processes=4
threads=2
stats=127.0.0.1:9191
daemonize = /home/APIParse/flask.log #日志文件
pidfile = project-master.pid
```

#### 启动命令
uwsgi uwsgi.ini
#### 查看进程是否启动成功
ps -ef | grep uwsgi

## 在调试项目中出现过的坑
### uwsgi 启动出现python版本的问题
pip install uwsgi 当用uwsgi启动用的是python2.0+
pip3 install uwsgi 当用uwsgi启动用的是python3.0+
如果已经安装uwsgi到python2.0+
pip uninstall uwsgi
在安装pip3 install uwsgi

###
