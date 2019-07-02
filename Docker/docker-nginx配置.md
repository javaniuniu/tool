

## docker nginx 配置
```
docker container run \
  --rm \ #nginx 停止后删除容器
  --name mynginx \ #命名image
  --volume "$PWD/html":/usr/share/nginx/html \ #本地nginx文件地址同步docker nginx 文件地址
  --volume "$PWD/nginx":/etc/nginx \  #本地nginx配置地址同步docker nginx 配置地址
  --network host \ #docker内置ip同步宿主机ip
  -d \ #后台运行
  nginx
```


## 配置完重新加载
```
docker exec -it mynginx service nginx reload
```

## 参考文档
[Docker容器访问宿主机网络](https://jingsam.github.io/2018/10/16/host-in-docker.html)
