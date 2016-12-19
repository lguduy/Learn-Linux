## 第九章、文件与文件系统的压缩与打包

### 8.1 压缩文件的用途与技术

目前我们使用的计算机系统中都是使用所谓的 **bytes** 单位来计量的. 事实上，计算机最小的计量单位应该是 **bits**
**1 byte = 8 bits**

### 8.2 Linux 系统常见的压缩命令

* .gz        gzip 程序压缩的文件；
* .bz2       bzip2 程序压缩的文件；
* .tar       tar 程序打包的数据，并没有压缩过；
* .tar.gz    tar 程序打包的文件，其中并且经过 gzip 的压缩；
* .tar.bz2   tar 程序打包的文件，其中并且经过 bzip2 的压缩.

#### gzip, zcat

```
[root@www ~]# gzip [-cdtv#] 档名
[root@www ~]# zcat 档名.gz 读取
选项与参数：
-c  ：将压缩的数据输出到萤幕上，可透过数据流重导向来处理；
-d  ：解压缩；
-t  ：可以用来检验一个压缩档的一致性～看看文件有无错误；
-v  ：可以显示出原文件/压缩文件的压缩比等资讯；
-#  ：压缩等级，-1 最快，但是压缩比最差、-9 最慢，但是压缩比最好！默认是 -6
```

zcat 则可以读取纯文字档被压缩后的压缩档

#### bzip2, bzcat 最常用的压缩命令
```
[root@www ~]# bzip2 [-cdkzv#] 档名
[root@www ~]# bzcat 档名.bz2
选项与参数：
-d  ：解压缩
-k  ：保留原始文件，而不会删除原始的文件喔！
-z  ：压缩的参数
-v  ：可以显示出原文件/压缩文件的压缩比等资讯；
-#  ：与 gzip 同样的，都是在计算压缩比的参数， -9 最佳， -1 最快！
```

### 8.3 打包命令： tar

**tar**

* 压　缩：tar -jcv -f filename.tar.bz filename
* 查　询：tar -jtv -f filename.tar.bz
* 解压缩：tar -jxv -f filename.tar.bz -C 欲解压缩的目录

-j, --bzip2 压缩文件夹内文件
-c， --create
-t， --list
-v, --verbose



### 8.4 完整备份工具：dump
