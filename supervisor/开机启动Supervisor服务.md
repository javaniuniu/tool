# 开机启动Supervisor服务

## 配置systemctl服务
1. > 进入/lib/systemd/system目录，并创建supervisor.service文件
```
[Unit]
Description=supervisor
After=network.target

[Service]
Type=forking
ExecStart=/usr/bin/supervisord -c /etc/supervisor/supervisord.conf
ExecStop=/usr/bin/supervisorctl $OPTIONS shutdown
ExecReload=/usr/bin/supervisorctl $OPTIONS reload
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
```

2. > 设置开机启动
```
systemctl enable supervisor.service
systemctl daemon-reload
```

3. >修改文件权限为766
```
chmod 766 supervisor.service
```

## 配置service类型服务
```
#!/bin/bash
#
# supervisord   This scripts turns supervisord on
#
# Author:       Mike McGrath <mmcgrath@redhat.com> (based off yumupdatesd)
#
# chkconfig:    - 95 04
#
# description:  supervisor is a process control utility.  It has a web based
#               xmlrpc interface as well as a few other nifty features.
# processname:  supervisord
# config: /etc/supervisor/supervisord.conf
# pidfile: /var/run/supervisord.pid
#

# source function library
. /etc/rc.d/init.d/functions

RETVAL=0

start() {
    echo -n $"Starting supervisord: "
    daemon "supervisord -c /etc/supervisor/supervisord.conf "
    RETVAL=$?
    echo
    [ $RETVAL -eq 0 ] && touch /var/lock/subsys/supervisord
}

stop() {
    echo -n $"Stopping supervisord: "
    killproc supervisord
    echo
    [ $RETVAL -eq 0 ] && rm -f /var/lock/subsys/supervisord
}

restart() {
    stop
    start
}

case "$1" in
  start)
    start
    ;;
  stop) 
    stop
    ;;
  restart|force-reload|reload)
    restart
    ;;
  condrestart)
    [ -f /var/lock/subsys/supervisord ] && restart
    ;;
  status)
    status supervisord
    RETVAL=$?
    ;;
  *)
    echo $"Usage: $0 {start|stop|status|restart|reload|force-reload|condrestart}"
    exit 1
esac

exit $RETVAL

```

将上述脚本内容保存到/etc/rc.d/init.d/supervisor文件中，修改文件权限为755，并设置开机启动
```
chmod 755 /etc/rc.d/init.d/supervisor
chkconfig supervisor on
```
注意：修改脚本中supervisor配置文件路径为你的supervisor的配置文件路径

其它Linux发行版开机启动脚本：https://github.com/Supervisor/initscripts



