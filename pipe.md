
### 管道命令(pipe)

> 管道命令使用“|”这个界定符号每个管道后接的第一个数据必定是“命令”，且该命令必须能接受Standard input的数据，列如：less ,more ,tail, head ;
不是管道命令有：ls, cp, mv

**注意：**
* 管理命令仅处理Standard output, 对于Standard error output会忽略；
* 管道命令必须先接受来自前一个命令的数据成为Standard input 继续处理才行；

#### 1、选取命令：cut，grep 
*cut*
以“行”为单位处理信息作用:将同一行里面的数据进行分解，常使用在分析一些数据或文字数据的时候

基本语法：
```
 cut -d '分隔字符' -f fields (用于分割字符)
 cut -c 字符范围( 用于排列整齐的信息) \r
 参数：
 -d : 后面接分割字符，与 -f 一起使用；
 -f : 根据-d 分割字符切割的字段为数段，用-f取出第几段的意思
 -c : 以字符为单位取出固定字符区间
```
**例子**
```
[root@bogon ~]# echo $PATH
/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/root/bin:/home/shiyanlou/mybin:/home/shiyanlou/mybin:/root/bin
```
1. 将PATHB变量以”:”分隔，取出第2段
```
[root@bogon ~]# echo $PATH | cut -d ':' -f 2
/usr/local/sbin
```
2. 将PATHB变量以”:”分隔，取出第2段,第4段
```
[root@bogon ~]# echo $PATH | cut -d ':' -f 2,4
/usr/local/sbin:/sbin
```
3. 将PATHB变量以”:”分隔，取出第2至4段
```
[root@bogon ~]# echo $PATH | cut -d ':' -f 2,4
/usr/local/sbin:/sbin
```
4. 将export输出的信息取第12字符以后的所有字符串
```
[root@bogon ~]# export
declare -x CVS_RSH="ssh"declare -x G_BROKEN_FILENAMES="1"declare -x HISTCONTROL="ignoredups".....

[root@bogon ~]# export | cut -c 12-
CVS_RSH="ssh"G_BROKEN_FILENAMES="1"HISTCONTROL="ignoredups"....
```
5. 将export输出的信息取第12至20个字符之间的所有字符串
```
[root@bogon ~]# export | cut -c 12-20
CVS_RSH="G_BROKEN_HISTCONTRHISTSIZE=....
```
6. 将export输出的信息取前12个字符的所有字符串
```
[root@bogon ~]# export | cut -c -12
declare -x Cdeclare -x Gdeclare -x H....
```
### grep

基本语法：
```
grep -[acinv] [--color=auto] '查找字符串' filename
参数：
-a : 将binary文件已text文件的方式查找数据； 其实就是默认方式显示出来
-c : 计算找到‘查找字符串’的次数；
-i : 忽略大小写的不同，所以大小视为相同；
-n : 顺便输出行号；可看着number的第一个字母n
-v : 反向选择，即显示出没有‘查找字符串’内容的那一行；
--color=auto : 可以将找到的关键字部分加上颜色显示；
```
例子：

1. 找出不是‘root’的数据
```
[root@bogon ~]# last | grep -v 'root'
reboot   system boot  2.6.32-431.el6.x Sat Jun 23 20:42 - 23:06 (19+02:24)  
reboot   system boot  2.6.32-431.el6.x Sat Jun 23 20:21 - 20:41  (00:20)   
reboot   system boot  2.6.32-431.el6.x Sat Jun 23 19:52 - 20:21  (00:28)   
...
``` 
2. 找出‘root’的数据
```
[root@bogon ~]# last | grep -v 'root'
reboot   system boot  2.6.32-431.el6.x Sat Jun 23 20:42 - 23:06 (19+02:24) 
reboot   system boot  2.6.32-431.el6.x Sat Jun 23 20:21 - 20:41  (00:20)    
reboot   system boot  2.6.32-431.el6.x Sat Jun 23 19:52 - 20:21  (00:28)   
...
``` 
  3. 找出不是‘root’的数据,取出第1列
```
[root@bogon ~]# last | grep -v 'root' | cut -d ' ' -f 1
reboot
reboot
reboot
```

#### 排序命令： sort, wc, uniq

sort命令

基本语法：
```
sort [-fbMnrtuk] [file or stdin]
参数：
-f : 忽略大小写的差异，例如A与a视为编码相同；
-b : 忽略最前面的空白部分；
-M : 以月份的名字来排序，例如 JAN, DEC等排序方法；
-n : 使用‘纯数字’进行排序（默认是以文字类型来排序的）
-r : 反向排序；
-u : 就是uniq,相同的数据中，仅出现一行代表；
-t : 分隔符，默认是用[tab]键来分割；
-k : 以那个区间（field）来进行排序的意思；
```
例子：


1.以文字类型排序
```
[root@bogon ~]# head -5 /etc/passwd | sort -n
adm:x:3:4:adm:/var/adm:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
root:x:0:0:root:/root:/bin/bash
```
2.以“:”分隔，第3个字段排序
```
[root@bogon ~]# head -5 /etc/passwd | sort -t ':' -k 4
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

#### uniq命令

基本语法：
```
uniq [-ic]

参数
-i : 忽略大小写字符的不同；
-c ：进行计数；
```
例子：

1.列出账号，取出第一个字段，排序，去重复
```
[root@bogon ~]# last | cut -d ' ' -f 1 | sort | uniq

reboot
root
```
2.进行去重排序
```
[root@bogon ~]# last | cut -d ' ' -f 1 | sort | uniq -c
      1
      7 reboot
     50 root
      1 wtmp
```
####wc命令 -- 计算输出信息的整体数据

基本语法：
```
wc [-lwm]
参数：
-l : 仅列出行；
-w : 仅列出多少字符（英文单字）；
-m : 多少字符；
```
例子：

1.分别列出行，字数，字符数
```
[root@bogon ~]# cat /etc/man.config | wc
    152     765    4940
```
2. 行数
```
[root@bogon ~]# cat /etc/man.config | wc -l
152
```
3.字数
```
[root@bogon ~]# cat /etc/man.config | wc -w
765
```
4. 字符数
```
[root@bogon ~]# cat /etc/man.config | wc -m
4940
```
5. 计算登录账号人数
```
[root@bogon ~]# last  | wc
     59     584    4427
```
6. 去除空白行和‘wtmp’行，后才是登录账号数 
```
[root@bogon ~]# last | grep [a-zA-Z] | grep -v 'wtmp' | wc
     57     577    4389
```

####双重定向：tee
--将数据输入到文件同时在屏幕上显示

基本语法：
```
tee [-a] file
参数:
-a : 以累加（append）的方式，将数据加入到file中
```

例子：

1.输出账号到list.list文件，同时显示第一列
```
[root@bogon ~]# last | tee list.list  | cut -d ' ' -f 1
root
root
root
root
root
......
[root@bogon ~]# cat list.list
root     pts/2        192.168.0.107    Fri Jul 13 19:45   still logged in   
root     pts/2        192.168.0.107    Thu Jul 12 23:21 - 00:37  (01:15)    
root     pts/3        192.168.0.107    Thu Jul 12 23:03 - 20:31  (21:27)    
root     pts/1        192.168.0.107    Thu Jul 12 21:57 - 20:09  (22:11)    
root     pts/0        :0.0             Thu Jul 12 21:43   still logged in   
root     pts/0        :0.0             Thu Jul 12 21:30 - 21:34  (00:04)    
```
2.显示/home/ 目录下文件 ，同时输出到homefile文件中
```
[root@bogon ~]# ls -l /home/ | tee ~/homefile | more
总用量 8
drwx------. 5 hjh   hjh   4096 1月   7 2018 hjh
drwx------. 6 user1 user1 4096 7月   9 22:36 user1
[root@bogon ~]# cat homefile
总用量 8
drwx------. 5 hjh   hjh   4096 1月   7 2018 hjh
drwx------. 6 user1 user1 4096 7月   9 22:36 user1
```
3.显示/ 目录下文件 ，同时以累加的方式输出到homefile文件中
```
[root@bogon ~]# ls -l / | tee -a ~/homefile | more
总用量 112
dr-xr-xr-x.   2 root root  4096 6月  22 20:12 bin
dr-xr-xr-x.   4 root root  4096 12月 17 2016 boot
-rw-r--r--.   1 root root     0 12月 25 2016 conf
...
[root@bogon ~]# cat ~/homefile
总用量 8
drwx------. 5 hjh   hjh   4096 1月   7 2018 hjh
drwx------. 6 user1 user1 4096 7月   9 22:36 user1
总用量 112
dr-xr-xr-x.   2 root root  4096 6月  22 20:12 bin
dr-xr-xr-x.   4 root root  4096 12月 17 2016 boot
-rw-r--r--.   1 root root     0 12月 25 2016 conf
...
```



####字符转换命令： tr，col, join, paste , expand

tr命令

```
tr [-ds] set1 ..
参数
-d : 删除信息当中set1这个字符串
-s : 替换掉重复的字符
```
例子：

1.将小写字母转为大写字母
```
[root@bogon ~]# last | tr '[a-z]' '[A-Z]'
ROOT     PTS/3        192.168.0.107    FRI JUL 13 22:52   STILL LOGGED IN   
ROOT     PTS/1        192.168.0.107    FRI JUL 13 22:17   STILL LOGGED IN   
ROOT     PTS/2        192.168.0.107    FRI JUL 13 19:45   STILL LOGGED IN   
ROOT     PTS/2        192.168.0.107    THU JUL 12 23:21 - 00:37  (01:15)    
...
```
2.去除‘:’冒号
```
[root@bogon ~]# last | tr -d ':'
root     pts/3        192.168.0.107    Fri Jul 13 2252   still logged in   
root     pts/1        192.168.0.107    Fri Jul 13 2217   still logged in   
root     pts/2        192.168.0.107    Fri Jul 13 1945   still logged in   
root     pts/2        192.168.0.107    Thu Jul 12 2321 - 0037  (0115)    
...
```


####col命令
将tab键换成对等的空格键，常用来将man page转存为纯文本文件 以方便查阅的功能
```
col [-xb]
参数:
-x : 将tab键转换成对等的空格键
-b : 当文字内有反斜杠（/）时，仅保留反斜杠最后接的那个字符
```

####join命令

将两个文件当中相同数据的哪一行加在一起

基本语法：
```
join [-ti12] file1 file2
参数：
-t : join默认以空格符分隔数据，并且对比"第一个字段"的数据，如果两个文件相同，则将两条数据连成一行，且第一个字段放在第一个；
-i : 忽略大小写的差异；
-1 ：这个是数字的 1, 代表第一个文件要用哪一个字段来分析的意思；
-2 ： 代表第二个文件要用哪个字段来分析的意思
```
例子：

1.将/etc/passwod 和/etc/shadow 相关的数据合在一起
```
[root@bogon ~]# head -n 3 /etc/passwd /etc/shadow
==> /etc/passwd <==
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin

==> /etc/shadow <==
root:$6$wsWkzkEH1qpTdIKq$i.DVI.QQYVeTRCEZezhrVzbvTXqW6MK/vNcYB0Qztzr9YzXevm8Bjjr0FOHmbj4xM9uftwO.2pTKbYe262QAx.:17152:0:99999:7:::
bin:*:15980:0:99999:7:::
daemon:*:15980:0:99999:7:::

[root@bogon ~]# join -t ':' /etc/passwd /etc/shadow
root:x:0:0:root:/root:/bin/bash:$6$wsWkzkEH1qpTdIKq$i.DVI.QQYVeTRCEZezhrVzbvTXqW6MK/vNcYB0Qztzr9YzXevm8Bjjr0FOHmbj4xM9uftwO.2pTKbYe262QAx.:17152:0:99999:7:::
bin:x:1:1:bin:/bin:/sbin/nologin:*:15980:0:99999:7:::
daemon:x:2:2:daemon:/sbin:/sbin/nologin:*:15980:0:99999:7:::
...
```
2.将文件相同的字段移至最前面，然后再拼接
```
[root@bogon ~]# head -n 3 /etc/passwd /etc/group
==> /etc/passwd <==
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin

==> /etc/group <==
root:x:0:
bin:x:1:bin,daemon
daemon:x:2:bin,daemon

[root@bogon ~]# join -t ':' -1 4 /etc/passwd -2 3 /etc/group
0:root:x:0:root:/root:/bin/bash:root:x:
1:bin:x:1:bin:/bin:/sbin/nologin:bin:x:bin,daemon
2:daemon:x:2:daemon:/sbin:/sbin/nologin:daemon:x:bin,daemon
4:adm:x:3:adm:/var/adm:/sbin/nologin:adm:x:adm,daemon
join: 文件1 没有被正确排序
7:lp:x:4:lp:/var/spool/lpd:/sbin/nologin:lp:x:daemon
join: 文件2 没有被正确排序
...
```
####paste命令
直接将两行贴在一起，且中间以【tab】键隔开而已
基本语法：
```
paste [-d] file1 file2
参数：
-d : 后面可以接分割符，默认以[tab]来分隔
-  : 如果file部分写成 - ，表示来时standard input 的数据意思
  
```
例子：

1. 将/etc/password /etc/shadow 黏贴在一起
```
[root@bogon ~]# paste /etc/passwd /etc/shadow
root:x:0:0:root:/root:/bin/bash    root:$6$wsWkzkEH1qpTdIKq$i.DVI.QQYVeTRCEZezhrVzbvTXqW6MK/vNcYB0Qztzr9YzXevm8Bjjr0FOHmbj4xM9uftwO.2pTKbYe262QAx.:17152:0:99999:7:::
bin:x:1:1:bin:/bin:/sbin/nologin    bin:*:15980:0:99999:7:::
daemon:x:2:2:daemon:/sbin:/sbin/nologin    daemon:*:15980:0:99999:7:::
adm:x:3:4:adm:/var/adm:/sbin/nologin    adm:*:15980:0:99999:7:::
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin    lp:*:15980:0:99999:7:::
sync:x:5:0:sync:/sbin:/bin/sync    sync:*:15980:0:99999:7:::
```
2. 先将/etc/group 读出，然后与范例一粘在一起，且仅取出前三行， 主要考察 - 
```
[root@bogon ~]# cat /etc/group | paste /etc/passwd /etc/group -  | head -n 3
root:x:0:0:root:/root:/bin/bash    root:x:0:    root:x:0:
bin:x:1:1:bin:/bin:/sbin/nologin    bin:x:1:bin,daemon    bin:x:1:bin,daemon
daemon:x:2:2:daemon:/sbin:/sbin/nologin    daemon:x:2:bin,daemon    daemon:x:2:bin,daemon
```

####expand 命令
将[tab] 按键转成空格键
基本语法
```
expand [-t] file
参数
-t : 后面可以接数字，一般来说，一个[tab]按键可以用8个空格键替换，我们也可以自行定义一个[tab]按键代表多少个字符；
```
例子：
```
[root@bogon ~]# grep '^MANPATH' /etc/man.config  | head -n 3
MANPATH    /usr/man
MANPATH    /usr/share/man
MANPATH    /usr/local/man

[root@bogon ~]# grep '^MANPATH' /etc/man.config  | head -n 3 | cat -A
MANPATH^I/usr/man$
MANPATH^I/usr/share/man$
MANPATH^I/usr/local/man$

[root@bogon ~]# grep '^MANPATH' /etc/man.config  | head -n 3 | expand -t 6 | cat -A
MANPATH     /usr/man$
MANPATH     /usr/share/man$
MANPATH     /usr/local/man$
[root@bogon ~]# grep '^MANPATH' /etc/man.config  | head -n 3 | expand -t 6 -| cat -A
MANPATH     /usr/man$
MANPATH     /usr/share/man$
MANPATH     /usr/local/man$
```

####切割命令： split
将一个文件依据文件大小或行数切割成小文件了
基本语法：
```
split [-bl] file PERFIX
参数：
-b : 后面可以接切割成的文件大小，可加单位，例如b 、k 、m等
-l : 以行数进行切割；
PERFIX : 代表前导符，即可以作为切割文件的前导文字
```
例子

1.以一个小文件大小200k分隔/etc/tpvmlp.conf 前导符为tpconf
```
[root@bogon ~]# cd /tmp ; split -b 200k  /etc/tpvmlp.conf  tpconf
[root@bogon tmp]# ls tpconf*
tpconfaa
```
2.以10行分隔/  前导符为lsroot
```
[root@bogon tmp]# ls -al / | split -l 10 - lsroot
[root@bogon tmp]# ls lsroota*
lsrootaa  lsrootab  lsrootac  lsrootad
[root@bogon tmp]# ls -l lsroota*
-rw-r--r-- 1 root root 486 7月  15 23:23 lsrootaa
-rw-r--r-- 1 root root 517 7月  15 23:23 lsrootab
-rw-r--r-- 1 root root 512 7月  15 23:23 lsrootac
-rw-r--r-- 1 root root  50 7月  15 23:23 lsrootad
```

####参数代换：xargs
产生某个命令的意思
基本语法
```
xargs [-0epn] command
参数：
-0 ： 如果输入stdin含有特殊字符，如`,\,空格键等字符时，这个参数可将它还原成一般字符，这个参数可以用于特殊状态
-e : 这个EOF (end of file)的意思。后面可以接一个字符串，当xargs分析到这个字符串时，就会停止继续工作
-p : 在执行每个命令的参数时，都会询问用户的意思
-n :  后面接次数，每次command命令执行时，要使用几个参数的意思，当xargs后面没接任何的命令时，默认是echo来进行输出
```
例子：
```
[root@bogon ~]# find /sbin -perm +7000 | xargs ls -l
-rwsr-xr-x. 1 root root 123776 11月 22 2013 /sbin/mount.nfs
-rwxr-sr-x. 1 root root   8792 11月 23 2013 /sbin/netreport
-rwsr-xr-x. 1 root root  10272 11月 22 2013 /sbin/pam_timestamp_check
-rwsr-xr-x. 1 root root  34840 11月 22 2013 /sbin/unix_chkpwd
```



