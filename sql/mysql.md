
### ubuntu 安装mysql
```
sudo apt-get install mysql-server

apt-get isntall mysql-client

sudo apt-get install libmysqlclient-dev
```
### 进入mysql
```
/usr/local/mysql/bin/mysql -u root -p
```

## mysql简单命令
```
sudo service mysql status    //查mysql的状态
sudo service mysql start     //启动mysql服务
sudo service mysql stop　　  //关闭mysql服务
select version()　　　　　　  //查看mysql版本
mysql -u root -p 　　　　　　 //root用户登录
show databases　　　　　　　　//查看当前数据库
use database_name　　　　　　 //选择某一个数据库
show tables　　　　　　　　　　//查看数据库中的表单
show columns from table_name //查看表结构
exit;　　　　　　　　　　　　　//退出mysql
```

### 参考链接
[Ubuntu 安装mysql和简单操作](https://www.cnblogs.com/zhuyp1015/p/3561470.html)
