
# shell 常见命令
c :打包
f :文件
x :解压
z :zip文件类型
j :bz2文件类型
-C:指定文件路径
-R:文件夹及子文件下内容

## 文件打包 对打包文件压缩  打包并压缩
```
tar cf /tmp/etc-backup.tar /etc   对etc文件夹打包成etc-backup.tar文件，并放在tmp文件夹下
tar czf /tmp/etc-backup.tar.gz /etc  打包并压缩
```
## 解压
```
tar zxvf /tmp/etc-backup.tar.gz -C /root  指定压缩文件 解压到root目录下
```
## 查看文件夹下所有文件 包括子文件夹
```
ls -al -R
```
