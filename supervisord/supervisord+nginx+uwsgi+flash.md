mac上配置


## 配置supervisord.conf
>vi /usr/local/etc/supervisord.conf
```
[include]
files = /usr/local/etc/supervisor.d/*.ini
```

## 配置supervisord监控文件
>vi /usr/local/etc/supervisor.d/simpleserver.ini
```
[program:simpleserver]
command=python -m SimpleHTTPServer  # 执行的命令
directory=/Users/minp/Downloads  # 执行命令的路径
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

## nginx 配置
>vi /usr/local/etc/nginx/nginx.conf
```
server {
         listen 80;
         server_name 127.0.0.1;#域名或者ip地址
         charset utf-8;
         # Django 的static和 media等静态资源交给Nginx处理
         location /static {
             # 路径必须和STATIC_ROOT一样
             alias /var/www/myApp/static/;
         }
          location /media  {
              #项目下的media路径
             alias /var/www/myApp/media/;
         }
         location /{
             # 必须和uwsgi.ini中socket一样,配置了upstream可以将uwsgi_pass配置为：http:// +             upstream名称，即“http://ray”.
             uwsgi_pass 127.0.0.1:3031;
             #uwsgi_pass http://ray;
             include uwsgi_params;
         }
     }
```

## 配置uwsgi
vi /Users/minp/app/myapp.ini
```
[uwsgi]
socket=127.0.0.1:3031
wsgi-file=server.py
master=true
processes=4
threads=2
stats=127.0.0.1:9191
```

[nginx配置参考](https://www.cnblogs.com/tandaxia/p/8810648.html)


