
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

## vim-正常模式(对文本修改)
```
u         对刚才修改的命令撤销
ctrl + r  当撤销多了 可以返回撤销
r         替换当前光标下的文字
```
##  vim-命令模式(在末行进行操作)
```
:set nu    显示行号
:set nonu  不显示行号
w /root/a.txt       把文件保存到root目录下的a.txt文件当中
q!                  不保存直接退出
:! + 命令          临时执行其他命令 如 :! config 临时查看ip
/ + 文件里面某内容    查找
:s/old/new          替换文件-当前光标下（当前行）
:%s/old/new         替换文件（全部文本）
:%s/old/new/g       替换文件（全部文本）
：3,5s/old/new/g    替换3-5行文件

```
