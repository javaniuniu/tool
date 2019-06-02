

### nginx常用命令：
```
　　nginx　　启动nginx
　　nginx -s stop/reload　　停止nginx/重载配置文件
　　nginx -v　　查看版本
　　nginx -t　　测试配置文件是否有语法上的错误等
```

### ubuntu 下命令
```
Nginx 重启 /etc/init.d/nginx restart
            状态。 service nginx status
        nginx -s stop  :快速停止nginx
        启动nginx: nginx -c /path/to/nginx.conf
                 sudo nginx -t   检查错误
sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled  将文件链接到sites-enabled目录
```


### nginx 配置uwsgi
```
server {
        listen       80;         //默认的web访问端口
        server_name  xxxxxx;     //服务器名
        #charset koi8-r;
        access_log  /home/wyz/flask/logs/access.log;    //服务器接收的请求日志，logs目录若不存在需要创建，否则nginx报错
        error_log  /home/wyz/flask/logs/error.log;         //错误日志

        location / {

            include        uwsgi_params;     //这里是导入的uwsgi配置

            uwsgi_pass     127.0.0.1:5051;   //需要和uwsgi的配置文件里socket项的地址
                                             //相同,否则无法让uwsgi接收到请求。

            uwsgi_param UWSGI_CHDIR  /home/wyz/flask;     //项目根目录

            uwsgi_param UWSGI_SCRIPT manage:app;     //启动项目的主程序(在本地上运行
                                                     //这个主程序可以在flask内置的
                                                     //服务器上访问你的项目)
```