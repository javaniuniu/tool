##  如何自动生成和安装requirements.txt依赖
```
$ pip freeze > requirements.txt     生成文件
$ pip install -r requirements.txt   安装文件
```
## 对于列出的过期库，pip提供了单个库的更新命令
```
pip install --upgrade 库名
```

## 使用 pipreqs 生产当前项目中的依赖
```
pip install pipreqs 安装pipreqs
pipreqs ./          生成 requirements.txt 依赖文件
```
```
File "c:\users\devtao\appdata\local\programs\python\python36-32\lib\site-packages\pipreqs\pipreqs.py", line 341, in init
    extra_ignore_dirs=extra_ignore_dirs)
  File "c:\users\devtao\appdata\local\programs\python\python36-32\lib\site-packages\pipreqs\pipreqs.py", line 75, in get_all_imports
    contents = f.read()
UnicodeDecodeError: 'gbk' codec can't decode byte 0xa6 in position 186: illegal multibyte sequence
```
若出现类似上边的报错UnicodeDecodeError: 'gbk' codec can't decode byte 0xa6 in position 186: illegal multibyte sequence
直接修改pipreqs.py 的75行，将encoding改为utf-8
