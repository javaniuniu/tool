## 从容器 phpfpm 中将 PHP 源码拷贝出来
```
$ docker cp phpfpm:/app .
```
## 使用 grep 查找函数调用
```
$ grep sqrt -r app/ # 找到了 sqrt 调用
app/index.php:  $x += sqrt($x);
$ grep add_function -r app/ # 没找到 add_function 调用，这其实是 PHP 内置函数
```

## docker
-p    配置docker 到本地的端口
-itd  拉取镜像
--name 命名拉取的镜像
--network docker之间网络通讯
```
配置docker nginx端口映射本地10000端口及命名image name
$ docker run --name nginx -p 10000:80 -itd feisky/nginx
网络链接 phpfpm项目
$ docker run --name phpfpm -itd --network container:nginx feisky/php-fpm
```

## 参考文档
[dockerinfo](http://www.dockerinfo.net/document)
