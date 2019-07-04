## No module named PIL 错误
```
$ pip install Pillow
```

## db_config.json 放在file文件夹中，即Directory
```
Traceback (most recent call last):
  File "/Users/minp/PycharmProjects/stockm/mo/db.py", line 19, in <module>
    with open(config_file) as f:
FileNotFoundError: [Errno 2] No such file or directory: 'db_config.json'
```

## 出现 Package 错误 或者 module 错误
>创package的时候 需要在文件夹下新建__init__.py文件 才能识别问package包
