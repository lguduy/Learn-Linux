## 第七章、Linux 文件与目录管理

### 7.1 目录与路径

* 相对路径
* 绝对路径

#### 7.1.2 目录的相关操作

```
.         代表此层目录
..        代表上一层目录
-         代表前一个工作目录
~         代表『目前使用者身份』所在的家目录
~account  代表 account 这个使用者的家目录(account是个帐号名称)
```

常见的处理目录的命令：
* cd：变换目录,Change Directory.如果仅输入 cd 时，代表的 *cd ~*
* pwd：显示目前的目录.Print Working Directory
* mkdir：创建一个新的目录
* rmdir：删除一个**空的目录**

**mkdir:**
```
[root@www ~]# mkdir [-mp] 目录名称
选项与参数：
-m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色～
-p ：帮助你直接将所需要的目录(包含上一级目录)递回创建起来！

[root@www tmp]# mkdir -p test1/test2/test3/test4
# 加了这个 -p 的选项，可以自行帮你创建多层目录！

范例：创建权限为rwx--x--x的目录
[root@www tmp]# mkdir -m 711 test2
```

**rmdir:**

```
[root@www ~]# rmdir [-p] 目录名称
选项与参数：
-p ：连同上一级『空的』目录也一起删除

[root@www tmp]# rmdir -p test1/test2/test3/test4
```

#### 7.1.3 关於运行档路径的变量： PATH

**echo $PATH:** echo有『显示、印出』的意思，而 PATH 前面加的 \$ 表示后面接的是变量，所以会显示出目前的 PATH ！

### 7.2 文件与目录管理

#### 7.2.1 文件与目录的检视： ls

```
[root@www ~]# ls [-aAdfFhilnrRSt] 目录名称

选项与参数：
-a  ：全部的文件，连同隐藏档( 开头为 . 的文件) 一起列出来(常用)
-d  ：仅列出目录本身，而不是列出目录内的文件数据(常用)
-l  ：长数据串列出，包含文件的属性与权限等等数据；(常用)
```

**ll代表ls -al**

#### 7.2.2 复制、删除与移动： cp, rm, mv

**cp：**
```
[root@www ~]# cp [-adfilprsu] 来源档(source) 目标档(destination)
[root@www ~]# cp [options] source1 source2 source3 .... directory
选项与参数：
-a  ：相当於 -pdr 的意思，至於 pdr 请参考下列说明；(常用)
-i  ：若目标档(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
-p  ：连同文件的属性一起复制过去，而非使用默认属性(备份常用)
-r  ：递回持续复制，用於目录的复制行为(常用)
-d  ：若来源档为连结档的属性(link file)，则复制连结档属性而非文件本身
```
```
范例一：用root身份，将家目录下的 .bashrc 复制到 /tmp 下，并更名为 bashrc
[root@www ~]# cp ~/.bashrc /tmp/bashrc
[root@www ~]# cp -i ~/.bashrc /tmp/bashrc
cp: overwrite `/tmp/bashrc'? n  <==n不覆盖，y为覆盖
# 重复作两次动作，由於 /tmp 底下已经存在 bashrc 了，加上 -i 选项后，
# 则在覆盖前会询问使用者是否确定！可以按下 n 或者 y 来二次确认呢！

范例二：变换目录到/tmp，并将/var/log/wtmp复制到/tmp且观察属性：
[root@www ~]# cd /tmp
[root@www tmp]# cp /var/log/wtmp . <==想要复制到目前的目录，最后的 . 不要忘
[root@www tmp]# ls -l /var/log/wtmp wtmp
-rw-rw-r-- 1 root utmp 96384 Sep 24 11:54 /var/log/wtmp
-rw-r--r-- 1 root root 96384 Sep 24 14:06 wtmp
# 注意上面的特殊字体，在不加任何选项的情况下，文件的某些属性/权限会改变；
# 这是个很重要的特性！要注意喔！还有，连文件创建的时间也不一样了！
# 那如果你想要将文件的所有特性都一起复制过来该怎办？可以加上 -a 喔！如下所示：

[root@www tmp]# cp -a /var/log/wtmp wtmp_2
[root@www tmp]# ls -l /var/log/wtmp wtmp_2
-rw-rw-r-- 1 root utmp 96384 Sep 24 11:54 /var/log/wtmp
-rw-rw-r-- 1 root utmp 96384 Sep 24 11:54 wtmp_2
# 整个数据特性完全一模一样！这就是 -a 的特性！
```
**如果你想要复制文件给其他的使用者， 也必须要注意到文件的权限(包含读、写、运行以及文件拥有者等等)， 否则，其他人还是无法针对你给予的文件进行修订的动作喔！**

```
[vbird@www ~]$ cp -a /var/log/wtmp /tmp/vbird_wtmp
[vbird@www ~]$ ls -l /var/log/wtmp /tmp/vbird_wtmp
-rw-rw-r-- 1 vbird vbird 96384  9月 24 11:54 /tmp/vbird_wtmp
-rw-rw-r-- 1 root  utmp  96384  9月 24 11:54 /var/log/wtmp
```
由於vbird的身份并不能随意修改文件的**拥有者与群组**，因此虽然能够复制wtmp的相关权限与时间等属性， 但是与拥有者、群组相关的，原本vbird身份无法进行的动作，即使加上 -a 选项.

**rm:**

```
[root@www ~]# rm [-fir] 文件或目录
选项与参数：
-f  ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；
-i  ：互动模式，在删除前会询问使用者是否动作
-r  ：递回删除啊！最常用在目录的删除了！这是非常危险的选项！！！

范例二：透过万用字节*的帮忙，将/tmp底下开头为bashrc的档名通通删除：
[root@www tmp]# rm -i bashrc*
# 注意那个星号，代表的是 0 到无穷多个任意字节喔！很好用的东西！

范例三：
[root@www tmp]# rm -r /tmp/etc
rm: descend into directory `/tmp/etc'? y
....(中间省略)....
# 因为身份是 root ，默认已经加入了 -i 的选项，所以你要一直按 y 才会删除！
# 如果不想要继续按 y ，可以按下『 [ctrl]-c 』来结束 rm 的工作。
# 这是一种保护的动作，如果确定要删除掉此目录而不要询问，可以这样做：
[root@www tmp]# \rm -r /tmp/etc
# 在命令前加上反斜线，可以忽略掉 alias 的指定选项
```

**mv:**
**移动文件与目录，或更名**

```
[root@www ~]# mv [-fiu] source destination
[root@www ~]# mv [options] source1 source2 source3 .... directory
选项与参数：
-f  ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
-i  ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！
-u  ：若目标文件已经存在，且 source 比较新，才会升级 (update)
```

#### 7.2.3 取得路径的文件名称与目录名称

```
[root@www ~]# basename /etc/sysconfig/network
network         <== 很简单！就取得最后的档名～
[root@www ~]# dirname /etc/sysconfig/network
/etc/sysconfig  <== 取得的变成目录名了！
```

### 7.3 文件内容查阅

* cat  由第一行开始显示文件内容
* tac  从最后一行开始显示，可以看出 tac 是 cat 的倒著写！
* nl   显示的时候，顺道输出行号！
* less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
* head 只看头几行
* tail 只看尾巴几行
* od   以二进位的方式读取文件内容！

**less:**

* /字串     ：向下搜寻『字串』的功能；
* ?字串     ：向上搜寻『字串』的功能；
* n         ：重复前一个搜寻 (与 / 或 ? 有关！)
* N         ：反向的重复前一个搜寻 (与 / 或 ? 有关！)

**head:**
```
[root@www ~]# head [-n number] 文件
选项与参数：
-n  ：后面接数字，代表显示几行的意思
```

**tail:**
```
[root@www ~]# tail [-n number] 文件
选项与参数：
-n  ：后面接数字，代表显示几行的意思
```

**touch:**

* 创建一个空的文件；
* 将某个文件日期修订为目前 (mtime 与 atime)

### 7.4 文件与目录的默认权限与隐藏权限

#### 7.4.1 文件默认权限：umask

umask:
目前使用者在创建文件或目录时候的权限默认值

在默认权限的属性上，目录与文件是不一样的。 **x 权限** 对於目录是非常重要的, 一般文件的创建则不应该有运行的权限，

```
[root@www ~]# umask
0022             <==后面三个数字
[root@www ~]# umask -S
u=rwx,g=rx,o=rx
```
umask 的分数指的是 **该默认值需要减掉的权限！** 扣分制

* 创建为文件 默认没有 **x权限**，也就是最大为 666 分
* 创建目录，则由於 x 与是否可以进入此目录有关，因此默认为所有权限均开放，亦即为 777 分

```
[root@www ~]# umask 002 #更改默认权限
```

#### 7.4.2 文件隐藏属性

**chattr (配置文件隐藏属性):**

```
[root@www ~]# chattr [+-=][ASacdistu] 文件或目录名称
选项与参数：
+   ：添加某一个特殊参数，其他原本存在参数则不动。
-   ：移除某一个特殊参数，其他原本存在参数则不动。
=   ：配置一定，且仅有后面接的参数

a  ：当配置 a 之后，这个文件将只能添加数据，而不能删除也不能修改数据，只有root
     才能配置这个属性。

i  ：他可以让一个文件不能被删除、改名、配置连结也无法写入或新增数据,对於系统安全性有相当大的助益！只有 root 能配置此属性

注意：属性配置常见的是 a 与 i 的配置值，而且很多配置值必须要身为root才能配置
```

**lsattr (显示文件隐藏属性):**

```
[root@www ~]# lsattr [-adR] 文件或目录
选项与参数：
-a ：将隐藏档的属性也秀出来；
-d ：如果接的是目录，仅列出目录本身的属性而非目录内的档名；
-R ：连同子目录的数据也一并列出
```
#### 7.4.3 文件特殊权限： SUID, SGID, SBIT ???

* 观察文件类型：file

### 7.5 命令与文件的搜寻

#### 7.5.1 命令名的搜寻

* which (寻找命令名)

#### 7.5.2 文件名的搜寻


* find 直接搜寻硬盘
* whereis (寻找特定文件), 搜索数据库

```
[root@www ~]# whereis [-bmsu] 文件或目录名
选项与参数：
-b    :只找 binary 格式的文件，可运行档( binary )
-m    :只找在说明档 manual 路径下的文件
-s    :只找 source 来源文件
-u    :搜寻不在上述三个项目当中的其他特殊文件

[root@www ~]# whereis ifconfig
ifconfig: /sbin/ifconfig /usr/share/man/man8/ifconfig.8.gz
[root@www ~]# su - vbird        <==切换身份成为 vbird
[vbird@www ~]$ whereis ifconfig <==找到同样的结果喔！
ifconfig: /sbin/ifconfig /usr/share/man/man8/ifconfig.8.gz
[vbird@www ~]$ exit              <==回归身份成为 root 去！
# 注意看，明明 which 一般使用者找不到的 ifconfig 却可以让 whereis 找到！
# 这是因为系统真的有 ifconfig 这个『文件』，但是使用者的 PATH 并没有加入 /sbin
# 所以，未来你找不到某些命令时，先用文件搜寻命令找找看再说！
```

* locate 也是搜索数据库

```
[root@www ~]# locate [-ir] keyword
选项与参数：
-i  ：忽略大小写的差异；
-r  ：后面可接正规表示法的显示方式

范例一：找出系统中所有与 passwd 相关的档名
[root@www ~]# locate passwd
```

**注意：**
数据库的创建默认是在每天运行一次，所以当你新创建起来的文件， 却还在数据库升级之前搜寻该文件，那么 locate 和 whereis 会搜索不到

**updatedb**：根据 /etc/updatedb.conf 的配置去搜寻系统硬盘内的档名，并升级 /var/lib/mlocate 内的数据库文件

* find

```
[root@www ~]# find [PATH] [option] [action]
选项与参数：
1. 与时间有关的选项：共有 -atime, -ctime 与 -mtime ，以 -mtime 说明
   -mtime  n ：n 为数字，意义为在 n 天之前的『一天之内』被更动过内容的文件；
   -mtime +n ：列出在 n 天之前(不含 n 天本身)被更动过内容的文件档名；
   -mtime -n ：列出在 n 天之内(含 n 天本身)被更动过内容的文件档名。
   -newer file ：file 为一个存在的文件，列出比 file 还要新的文件档名

范例一：将过去系统上面 24 小时内有更动过内容 (mtime) 的文件列出
[root@www ~]# find / -mtime 0
# 那个 0 是重点！0 代表目前的时间，所以，从现在开始到 24 小时前，
# 有变动过内容的文件都会被列出来！那如果是三天前的 24 小时内？
# find / -mtime 3 有变动过的文件都被列出的意思！

范例二：寻找 /etc 底下的文件，如果文件日期比 /etc/passwd 新就列出
[root@www ~]# find /etc -newer /etc/passwd
```
![find](http://cn.linux.vbird.org/linux_basic/0220filemanager_files/find_time.gif)

```
范例三：搜寻 /home 底下属於 vbird 的文件
[root@www ~]# find /home -user vbird

范例四：搜寻系统中不属於任何人的文件
[root@www ~]# find / -nouser

范例五：找出档名为 passwd 这个文件
[root@www ~]# find / -name passwd

范例六：找出 /var 目录下，文件类型为 Socket 的档名有哪些？
[root@www ~]# find /var -type s

范例八：将上个范例找到的文件使用 ls -l 列出来～
[root@www ~]# find / -perm +7000 -exec ls -l {} \;
# 注意到，那个 -exec 后面的 ls -l 就是额外的命令，命令不支持命令别名，
# 所以仅能使用 ls -l 不可以使用 ll

利用万用字节来找寻档名
[root@www ~]# find /etc -name '*httpd*'
```
find 后面可以接多个目录来进行搜寻！另外， find 本来就会**搜寻次目录**

* {} 代表的是由 find 找到的内容
* -exec 一直到 \\; 是关键字，代表 find 额外动作的开始 (-exec) 到结束 (\\;) ，在这中间的就是 find 命令内的额外动作。
* 在本例中就是『 ls -l {} 』罗！
因为『 ; 』在 bash 环境下是有特殊意义的，因此利用反斜线来跳脱。

### 7.6 极重要！权限与命令间的关系
