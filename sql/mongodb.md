## 安装mongodb
```
# 进入 /usr/local
cd /usr/local

# 下载
sudo curl -O https://fastdl.mongodb.org/osx/mongodb-osx-ssl-x86_64-4.0.9.tgz

# 解压
sudo tar -zxvf mongodb-osx-ssl-x86_64-4.0.9.tgz

# 重命名为 mongodb 目录

sudo mv mongodb-osx-x86_64-4.0.9/ mongodb
```

## 配置mongo
```
vim ~/.bash_profile
source ~/.bash_profile
```
```
export PATH=/usr/local/mongodb/bin:$PATH
```

## 运行mogodb
```
1. 首先我们创建一个数据库存储目录 /data/db
sudo mkdir -p /data/db

2. 启动 mongodb，默认数据库目录即为 /data/db
sudo mongod

# 如果没有创建全局路径 PATH，需要进入以下目录
cd /usr/local/mongodb/bin
sudo ./mongod
```

## 进入mongo cli 可执行mongo命令
```
$ cd /usr/local/mongodb/bin
$ ./mongo
MongoDB shell version v4.0.9
connecting to: mongodb://127.0.0.1:27017/?gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("3c12bf4f-695c-48b2-b160-8420110ccdcf") }
MongoDB server version: 4.0.9
……
> 1 + 1
2
>
```
