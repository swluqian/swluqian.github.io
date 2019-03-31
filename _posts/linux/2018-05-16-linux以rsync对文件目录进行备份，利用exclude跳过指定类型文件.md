---
layout: post
comments: true
categories: linux
---

# linux以rsync对文件目录进行备份，利用exclude跳过指定类型文件

有时会有备份某些文件(例如脚本)的需求，但又不想把脚本执行的日志或一些结果文件备份，可以使用rsync中的exclude来解决。

> 将oldfile文件夹拷贝至oldfile_bak文件夹，但需要过滤掉其中的.log文件

```
du -ha ./oldfile/ 
4.0K    ./oldfile/a/2222.csv
4.0K    ./oldfile/a/aabb.log
4.0K    ./oldfile/a/4321.sh
4.0K    ./oldfile/a/log/aabb2.log
4.0K    ./oldfile/a/log/1234.sh
12K     ./oldfile/a/log
4.0K    ./oldfile/a/55511.sh
4.0K    ./oldfile/a/data
36K     ./oldfile/a
4.0K    ./oldfile/b/uuss.sh
8.0K    ./oldfile/b
4.0K    ./oldfile/data
52K     ./oldfile/
```

执行命令：


```
rsync -av --exclude "*.log" ./oldfile/ ./oldfile_bak
```

执行结果：


```
sending incremental file list
./
data
a/
a/2222.csv
a/4321.sh
a/55511.sh
a/data/
a/log/
a/log/1234.sh
b/
b/uuss.sh
 
sent 568 bytes  received 145 bytes  1426.00 bytes/sec
total size is 43  speedup is 0.06
```

并没有复制


```
./oldfile/a/log/aabb2.log
./oldfile/a/log/aabb2.log
```

> 将oldfile文件夹拷贝至oldfile_bak文件夹，但需要过滤掉其中的.log、.csv文件、data文件夹及其子文件

建立一个txt文件，将需要过滤掉的文件类型写入

```
vi exclude.txt
*.log
*.csv
data
执行命令：
rsync -av --exclude-from "exclude.txt" ./oldfile/ ./oldfile_bak  
执行结果：  
sending incremental file list
./
a/
a/4321.sh
a/55511.sh
a/log/
a/log/1234.sh
b/
b/uuss.sh
 
sent 397 bytes  received 103 bytes  1000.00 bytes/sec
total size is 26  speedup is 0.05
```

.log、.csv以及data目录都没有被复制。
