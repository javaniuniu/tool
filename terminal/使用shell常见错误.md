## 问题：无法获得锁 /var/lib/dpkg/lock-frontend - open (11: 资源暂时不可用)
> 问题原由 在安装git时出现的错误提示
> 处理方法 rm 对于的锁
```
网上处理方法
sudo rm /var/cache/apt/archives/lock 
sudo rm /var/lib/dpkg/lock

我的处理方法
sudo rm /var/lib/dpkg/lock-frontend
```