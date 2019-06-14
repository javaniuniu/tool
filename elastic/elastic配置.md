### 配置
```
[program:logstash]
directory = /var/app/logstash-7.1.1/bin
command = /var/app/logstash-7.1.1/bin/logstash -f /var/app/logstash-7.1.1/logstash-simple.conf -l /var/app/logstash-7.1.1/logs/test-logstash.log
autostart = false
startsecs = 5
autorestart = true
startretries = 3
user = hylexus
redirect_stderr = true
stdout_logfile_backups = 20
stdout_logfile=/var/log/logstash/logstash.log
stdout_logfile_maxbytes=10MB
stderr_logfile=/var/log/logstash/logstash-err.log
stderr_logfile_maxbytes=10MB
; environment=PYTHONPATH=$PYTHONPATH:/path/to/somewhere
```