# mongo查看数据库空间大小

## db.stats
```
db.stats();
```

默认是bytes单位
返回
```
{
    "db" : "xxx",   //当前数据库
    "collections" : 27,  //当前数据库多少表
    "objects" : 18738550,  //当前数据库所有表多少条数据
    "avgObjSize" : 1153.54876188392, //每条数据的平均大小
    "dataSize" : 21615831152.0,  //所有数据的总大小
    "storageSize" : 23223312272.0,  //所有数据占的磁盘大小
    "numExtents" : 121,
    "indexes" : 26,   //索引数
    "indexSize" : 821082976,  //索引大小
    "fileSize" : 25691160576.0,  //预分配给数据库的文件大小
    "nsSizeMB" : 16,
    "dataFileVersion" : {
        "major" : 4,
        "minor" : 5
    },
    "extentFreeList" : {
        "num" : 1,
        "totalSize" : 65536
    },
    "ok" : 1.0
}
```

## scale参数
可以通过传参数，比如
```
db.stats(1024)      得到的是kb单位的
```


```
db.stats(1073741824) 得到的是G单位的
```


```
{
    "db" : "xxxx",
    "collections" : 27,
    "objects" : 18736680,
    "avgObjSize" : 1153.53257375373,
    "dataSize" : 20,  //所有数据的总大小
    "storageSize" : 21, //所有数据占的磁盘大小
    "numExtents" : 121,
    "indexes" : 26,
    "indexSize" : 0,
    "fileSize" : 23,  //预分配给数据库的文件大小
    "nsSizeMB" : 16,
    "dataFileVersion" : {
        "major" : 4,
        "minor" : 5
    },
    "extentFreeList" : {
        "num" : 1,
        "totalSize" : 0
    },
    "ok" : 1.0
}
```


这里的objects以及avgObjSize还是bytes为单位的，不受参数影响

## 参考文档
- [mongo查看数据库空间大小](https://www.jianshu.com/p/c9a9a43472b8)
