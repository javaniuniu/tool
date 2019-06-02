# 部署Django项目
## Django+uWSGI+nginx 部署
* django 一个pyhton的开源web框架。
* uWSGI 一个基于自有的uwsgi协议、WSGI协议和http服务协议的web网关
* nginx 常用的代理服务器

*WSGI*：一种实现python解析的通用接口标准/协议，是一种通用的接口标准或者接口协议，实现了python web程序与服务器之间交互的通用性。 
利用它，web.py或bottle或者django等等的python web开发框架，就可以轻松地部署在不同的web server上了；
*uwsgi*:同WSGI一样是一种通信协议 
uwsgi协议是一个uWSGI服务器自有的协议，它用于定义传输信息的类型，它与WSGI相比是两样东西。

*uWSGI* :一种python web server或称为Server/Gateway 
uWSGI类似tornadoweb或者flup，是一种python web server，uWSGI是实现了uwsgi和WSGI两种协议的Web服务器，负责响应python 的web请求。 
因为apache、nginx等，它们自己都没有解析动态语言如php的功能，而是分派给其他模块来做，比如apache就可以说内置了php模块，让人感觉好像apache就支持php一样。 
uWSGI实现了wsgi协议、uwsgi协议、http等协议。 Nginx中HttpUwsgiModule的作用是与uWSGI服务器进行交换。

## 项目流程
```
首先客户端请求服务资源，
nginx作为直接对外的服务接口,接收到客户端发送过来的http请求,会解包、分析，
如果是静态文件请求就根据nginx配置的静态文件目录，返回请求的资源，
如果是动态的请求,nginx就通过配置文件,将请求传递给uWSGI；uWSGI 将接收到的包进行处理，并转发给wsgi，
wsgi根据请求调用django工程的某个文件或函数，处理完后django将返回值交给wsgi，
wsgi将返回值进行打包，转发给uWSGI，
uWSGI接收后转发给nginx,nginx最终将返回值返回给客户端(如浏览器)。
*注:不同的组件之间传递信息涉及到数据格式和协议的转换
```

​### 作用: 
1. 第一级的nginx并不是必须的，uwsgi完全可以完成整个的和浏览器交互的流程； 
2. 在nginx上加上安全性或其他的限制，可以达到保护程序的作用； 
3. uWSGI本身是内网接口，开启多个work和processes可能也不够用，而nginx可以代理多台uWSGI完
    成uWSGI的负载均衡； 
4. django在debug=False下对静态文件的处理能力不是很好，而用nginx来处理更加高效。

## 安装与配置
### 创建项目运行的虚拟环境
```
virtualenv env --python=python3.6
pip install -r requirements.txt  #安装django运行环境
```
运行开发服务器测试
```
                cd project # 进入项目 project 目录
                python manage.py runserver
            运行开发服务器测试，确保开发服务器下能正常打开网站。
```
安装uWSGI
```
                # 在普通用户下安装
                sudo apt install libpython3.6-dev
                # 虚拟环境中
                pip install uwsgi
```
测试uWSGI: 新建文件test.py，写入以下内容
```
                def application(env, start_response):
                    start_response('200 OK', [('Content-Type','text/html')])
                    return "Hello World"
```
运行
```
                # 0.0.0.0可以省略 
                sudo uwsgi --http 0.0.0.0:8000 --wsgi-file test.py --processes 4 --threads 3
      如果提示端口已经被占用，这时可以把相关的进程 kill 掉。
            probably another instance of uWSGI is running on the same address (:8002).
            bind(): Address already in use [core/socket.c line 764]
```
```
按照端口进行查询进程
lsof -i :8002
      可以查出：
           COMMAND  PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
            uwsgi   2208   tu    4u  IPv4 0x53492abadb5c9659      0t0  TCP *:teradataordbms (LISTEN)
            uwsgi   2209   tu    4u  IPv4 0x53492abadb5c9659      0t0  TCP *:teradataordbms (LISTEN)
      这时根据 PID 可以用下面的命令 kill 掉相关程序：
sudo kill -9 2208 2209
```
1. 运行django项目
```
      # --chdir 项目目录 --home 虚拟环境目录 project.wsgi 指的是 project/wsgi.py 文件
      uwsgi --http :8000 --chdir=/path/to/project  --home=/path/to/env --module project.wsgi
```
2. 配置文件运行
```
            上面这样使用一行命令太长了，我们使用 ini 配置文件来搞定，比如项目在 /home/ray/project 这个位置，在其中新建一个 uwsgi.ini 全路径为 /home/ray/project/uwsgi.ini
                [uwsgi]
                #socket 为上线使用，http为直接作为服务器使用。
                socket = 127.0.0.1:8080 #ip和端口号可以改
                http = 127.0.0.1:8000
                #项目目录
                chdir=/home/ray/project
                module=project.wsgi
                #虚拟环境目录
                #home = home/ray/MxOnline/mxonlineEnv
                master = true         
                processes=4
                threads=2
                # 下面的参数不一定要加
                # pidfile=uwsgi.pid   uwsgi.pid 和uwsgi.log会在启动uwsgi时自动生成在项目目录下。
                # daemonize=uswgi.log
                # max-requests=2000    
                # chmod-socket=664
                # vacuum=true
```
```
# uwsgi启动
uwsgi --ini uwsgi.ini
#uwsgi 停止
uwsgi --stop uwsgi.pid
```

3. 安装nginx,在普通用户下安装。

                # 安装
                sudo apt install nginx 
                #重载
                sudo /etc/init.d/nginx reload
                sudo nginx -s reload
                # 启动
                sudo /etc/init.d/nginx start
                # 停止
                sudo /etc/init.d/nginx stop
                # 重启
                sudo /etc/init.d/nginx restart
                #查看nginx是否启动
                ps -ef | grep nginx
            root     24956     1  0 19:41 ?        00:00:00 nginx: master process /usr/local/nginx/sbin/nginx
            nobody   24957 24956  0 19:41 ?        00:00:00 nginx: worker process
            root     24959 10533  0 19:41 pts/0    00:00:00 grep --color=auto nginx
4. 配置 nginx
```
        ##### sites-enable 和 sites-available
  These directories are used to define configurations for your websites. Files are generally created in the "sites-available" directory, and thensymbolically linked to the "sites-enabled" directory when they are ready to go live.
​ 都是在nginx.conf作修改，因为nginx.conf include指令已经包括了sites-enabled的内容，在site-enabled作修改就相当于在nginx.conf作修改，可维护性高。

include /etc/nginx/conf.d/*.conf;

include /etc/nginx/sites-enabled/*;

ites-available是存放当前的server配置, 在这里修改。

sites-enabled是激活并使用的server配置（从sites_available的文件创建快捷方式到sites-enabled）
```

新建一个网站 test
```
# 不用sudo没有权限修改
sudo vim /etc/nginx/sites-available/test.conf
 #配置负载均衡
     # upstream ray {
     #    server 127.0.0.1:8000; # for a web port socket
     #}

     server {
         listen 80;
         server_name www.helloray.cn;#域名或者ip地址
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
             uwsgi_pass 127.0.0.1:8080; 
             #uwsgi_pass http://ray; 
             include uwsgi_params;
         }
     }
```
激活网站：建立软链接
```
sudo ln -s /etc/nginx/sites-available/test.conf /etc/nginx/sites-enabled/test.conf
```

nginx创建静态文件目录，并更改权限
```
 sudo mkdir -vp /var/www/myApp/static/
 sudo chmod 777 /var/www/myApp/static/
 ```
在项目下setting.py 文件中
```
 STATIC_URL = 'static'
 STATIC_ROOT = '/var/www/myApp/static/'
 STATICFILES_DIRS = [
   os.path.join(BASE_DIR,'static'),
 ]
 MEDIA_URL = '/media/'
 MEDIA_ROOT = os.path.join(BASE_DIR,'media')
 ```
在项目目录下迁移静态文件
```
python manage.py collectstatic
```

Django中settings.py中的五个设置参数的一些故事：

1. MEDIA_ROOT与MEDIA_URL

事实上MEDIA_ROOT和MEDIA_URL代表的是用户上传后的文件一般保存的地方。我的理解是，可变文件的文件夹。

与这两个参数有联系的，是在Django的FileField和ImageField这样的Model类中，有upload_to参数可选。当upload_to设置相关的地址后，如：upload_to="username"；文件上传后将自动保存到 os.path.join(MEDIA_ROOT, upload_to)。

而MEDIA_URL,，则代表用户通过URL来访问这个本地地址的URL。如本机http://127.0.0.1/， MEDIA_URL设置为"/site_media/"，那么通过 http://127.0.0.1/site_media/***  就可以访问相关的上传图片或者其他资源。

2. STATIC_ROOT与STATIC_URL

STATIC_ROOT和STATIC_URL则是网站中，用于网站显示的静态图片、CSS、JS等文件的保存地址。我的理解是，运行中不会再变文件的文件夹（即不会删除或者新增）

2.1  STATIC_URL

 同MEDIA_URL类似；STATIC_URL为"/static/"时候，通过http://127.0.0.1/static/***就可以访问相关的静态文件了。

2.2 STATIC_ROOT

STATIC_ROOT是一个比较特殊的文件夹。这是区别Django的开发模式和部署模式下最大的地方了。

通常我们在开发模式下，可以在我们所在的project下建立相应的app， 然后每个app下都建立相应的static文件夹。在开发模式下（Debug=True），Django将为我们自动查找这些静态文件（每个app）并在网页上显示出来。然而，在部署模式下，Django认为这些工作交由web服务器来运行会更有效率。

因此，在部署时，我们需要运行一下python manage.py collectstatic 这个命令。这个命令将会把每个app里的static目录下的文件copy到STATIC_ROOT这个文件夹下，这时候如果在部署模式下（Debug=False），网页中相关的，如： http://127.0.0.1/static/*** 的访问，将不会访问Django下各个App中的static，而是STATIC_ROOT中所指定的文件夹。

3. Debug=False后，为何无法访问图片和js等文件了？

其实这个问题，是在于web服务器没有对STATIC_ROOT以及MEDIA_ROOT这两个文件夹进行映射所导致的。

以apache为例，假定：

STATIC_ROOT="/home/user/static/" 

STATIC_URL="/static/"

 MEDIA_ROOT="/home/user/media/"

MEDIA_URL="/media/"

那么可以在apache的配置文件中，增加以下：


<Location "/static/">
Order deny,allow
Allow from all
Satisfy Any
</Location>
Alias /static/     "/home/user/static"
<Location "/media/">
Order deny,allow
Allow from all
Satisfy Any
</Location>
Alias /media/      "/home/user/media/"


4. STATICFILES_DIRS：和TEMPLATE_DIRS的含义差不多，就是除了各个app的static目录以外还需要管理的静态文件，添加到这里的文件会在collectstatic时 copy到STATIC_ROOT中

## 负载均衡的设置
网站的访问量越来越大，服务器的服务模式也得进行相应的升级，比如分离出数据库服务器、分离出图片作为单独服务，这些是简单的数据的负载均衡，将压力分散到不同的机器上。有时候来自web前端的压力，也能让人十分头痛。怎样将同一个域名的访问分散到两台或更多的机器上呢？这其实就是另一种负载均衡了，nginx自身就可以做到，只需要做个简单的配置就行。

　　nginx不单可以作为强大的web服务器，也可以作为一个反向代理服务器，而且nginx还可以按照调度规则实现动态、静态页面的分离，可以按照轮询、ip哈希、URL哈希、权重等多种方式对后端服务器做负载均衡，同时还支持后端服务器的健康检查。

nginx 的 upstream目前支持 4 种方式的分配 

轮询：将请求依次轮询发给每个服务器，如果后端服务器down掉，能自动剔除。

最少链接：将请求发送给持有最少活动链接的服务器。

ip哈希：通过ip的哈希函数结果决定请求发送给哪个服务器。这样每个访客固定访问一个后端服务器，可以解决session的问题。

权重：服务器的权重越高，处理请求的概率越大。用于后端服务器性能不均的情况。

轮询负载均衡
在nginx.conf配置文件中添加如下配置，此配置有三台服务器提供支付服务。

1. 缺省配置就是轮询策略;
2. nginx负载均衡支持http和https协议，只需要修改 proxy_pass后面的协议即可;
    nginx支持FastCGI, uwsgi, SCGI,memcached的负载均衡,只需将 proxy_pass改为uwsgi_pass, fastcgi_pass, scgi_pass,memcached_pass即可。
3. 此策略适合服务器配置相当，无状态且短平快的服务使用。
```
http {
    upstream CashServers {
        server CashServers1.com;
        server CashServers2.com;
        server CashServers3.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://CashServers;
        }
    }
}
```

### 最少链接负载均衡
1. 最少链接负载均衡通过least_conn指令定义;
2. 此负载均衡策略适合请求处理时间长短不一造成服务器过载的情况;
```
http {
    upstream CashServers {
      least_conn;
        server CashServers1.com;
        server CashServers2.com;
        server CashServers3.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://CashServers;
        }
    }
}
```

### ip哈希负载均衡
1. ip哈希负载均衡使用ip_hash指令定义;
1. nginx使用请求客户端的ip地址进行哈希计算，确保使用同一个服务器响应请求;
3. 此策略适合有状态服务，比如session;
```
http {
    upstream CashServers {
      ip_hash;
        server CashServers1.com;
        server CashServers2.com;
        server CashServers3.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://CashServers;
        }
    }
}
```

### 权重负载均衡
1. 权重负载均衡需要使用weight指令定义;
2. 权重越高分配到需要处理的请求越多;
3. 此策略可以与最少链接负载和ip哈希策略结合使用;
4. 此策略比较适合服务器的硬件配置差别比较大的情况;
```
http {
    upstream CashServers {      
        server CashServers1.com weight=3;
        server CashServers2.com weight=2;
        server CashServers3.com weight=1;
    }

    server {
        listen 80;
        location / {
            proxy_pass http://CashServers;
        }
    }
}
```
附录：参数说明
```
   >----------------附录:uwsgi参数说明----------------
   >
   >- http ： 协议类型和端口号
   >- processes ： 开启的进程数量
   >- workers ： 开启的进程数量，等同于processes（官网的说法是spawn the specified number ofworkers / processes）
   >- chdir ： 指定运行目录（chdir to specified directory before apps loading）
   >- wsgi-file ： 载入wsgi-file（load .wsgi file）
   >- stats ： 在指定的地址上，开启状态服务（enable the stats server on the specified address）
   >- threads ： 运行线程。由于GIL的存在，我觉得这个真心没啥用。（run each worker in prethreaded mode with the specified number of threads）
   >- master ： 允许主进程存在（enable master process）
   >- daemonize ： 使进程在后台运行，并将日志打到指定的日志文件或者udp服务器（daemonize uWSGI）。实际上最常
   >  用的，还是把运行记录输出到一个本地文件上。
   >- daemonize ： 使进程在后台运行，并将日志打到指定的日志文件或者udp服务器（daemonize uWSGI）。实际上最常
   >  用的，还是把运行记录输出到一个本地文件上。
   >- vacuum ： 当服务器退出的时候自动清理环境，删除unix socket文件和pid文件（try to remove all of the ge
```



参考文档：
[Django+uWSGI+nginx 部署](https://segmentfault.com/a/1190000016108576)