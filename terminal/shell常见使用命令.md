
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

## 新建用户
```
useradd minp 新建用户
id minp      查看uid gid 组
passwd minp  给minp用户创建秘密或修改秘密
userdel minp 删除用户名 其未删除的用户文件夹只有root用户能查看使用
userdel  -r minp 删除用户并删除用户文件夹
usermod -d /home/minp/m minp 修改用户登陆位置）
man usermod 查看更多usermod命令
man chage   查看更多chage命令(可修改用户的生命周期，秘密有效期等)
```
### 创新的家目录说明
```
如果是创建用户，指定了新的位置，会自动创建
如果使用usermod 要手动移动家目录，属主如果不一致要记得修改
例如：
修改了 user1 的家目录为 /home/userhome1
记得要 移动家目录 mv /home/user1 /home/userhome1
还要设置属主（如果需要）属组
chown -R user1:user1 /home/userhome1
chown -R
```


## 用户组 相同用户组的用户可以共享文件
```
useradd user1
groupadd group1
usermod -g groud1 user1
id user1  #可以看到user1切换到 group1 组下
或者更简单的命令
useradd -g group1 user2
```

## 用户切换
```
su - minp # '-' 可以直接切换到 /home/minp 目录下
```

```
su 切换用户
sudo 授权普通用户执行某个命令
```
