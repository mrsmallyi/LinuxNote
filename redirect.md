### 数据流重定向
**定义：** 将某个命令执行后应该要出现在屏幕上的数据传输到其他的地方，如文件或设备(打印机)
#### 什么事数据流重定向
 **file**   ---Standard input -->   **Command**  --- Standard output --> **file/device**
 <p style="margin-left: 200px">|
Standard error
|
**file/device**										     
</p>

Standard output (标准输出)：命令执行所回传的正确的信息

Standard error output (标准错误输出)：命令执行失败后，所回传的错误信息 

传送所用**特殊字符**：

 1. **标准输入(stdin):** 代码为0,使用<或<<;
 2. **标准输出(stdout):** 代码为1,使用>或>>;
 3. **标准错误输出(stderr):** 代码为3,使用2>或2>>.

**例子：**
```
[root@bogon ~]# ll / > ~/rootfile
[root@bogon ~]# ll rootfile 
-rw-r--r-- 1 root root 1297 7月   9 21:20 rootfile
```
rootfile : 该文件名可以自己随便取

**创建方式：**

1.	该文件rootfile若不存在，系统会自动创建出来；
2.	当该文件存在时，系统会先将文件内容清空，然后将数据写入；
3.	即文件存在时，以>输出到一个已存在文件时，那个文件的内容会被覆盖
4.	若不想文件内容被覆盖掉，则使用>> 两个大于号，数据则会累加进入



**Standard output文件输出操作:**   
1. **1>** :  以 **覆盖** 的方法将 **正确** 的数据出到指定文件或设备上；
2. **1>>** : 以 **累加** 的方法将 **正确** 的数据出到指定文件或设备上；
3. **2>** :  以 **覆盖** 的方法将 **错误** 据出到指定文件或设备上；
4. **2>>** :  以 **累加**的方法将 **错误** 的数据出到指定文件或设备上；

![Alt text](/images/redirect/1531144622928.png)

* 将正面数据跟错误信息分别输出到不同文件
	
![Alt text](/images/redirect/1531144733318.png)

* /dev/null垃圾桶黑洞设备与特性写法
 /dev/null 可以吃到任何导入该设备的信息
 
![Alt text](/images/redirect/1531144913220.png)

* 将错误信息跟正确信息输出到同一个文件
1> list   ** 2>&1**
$> list
![Alt text](/images/redirect/1531146509099.png)

**Standard input 文件输入操作 <与 << ：**

需要ctrl + d 结束数据输入
```
[user1@bogon ~]$ cat > catfile
testing
cat file test 
[user1@bogon ~]$ 
```
使用 << "eof" 代替 ctrl + d 结束输入
![Alt text](/images/redirect/1531147579094.png)

使用stdin 代替键盘输入以创建新文件
![Alt text](/images/redirect/1531147357899.png)


使用命令輸出重定向的好处？
*	可以将屏幕重要输出信息保存
*	后台程序不会干扰屏幕正常输出
*	例行命令执行结果可以保存
*	执行错误信息可以2>/dev/null丢掉
*	错误跟正确信息可以分开输出

#### 命令执行的判断依据：；， && ， ||
*	cmd ; cmd
*	$?(命令回传码) 与 && 或 ||

|     命令执行情况    | 说明 |
| :-----------------| :----------------------: |
|  cmd1 && cmd2	  	| cmd1 执行正确 ( $? = 0 ) , 则执行cmd2 |
|  cmd1 && cmd2     | cmd1 执行错误( $? ≠ 0)  , 则cmd2不执行 |
|  cmd1 \|\|  cmd2  | cmd1 执行正确 ( $? = 0 ) , 则cmd2不执行 |
|  cmd1 \|\|  cmd2  | cmd1 执行错误( $?  ≠  0 ) , 则cmd2执行 |

**注意：** Linux下面的命令都是由左向右执行的
例子：
```
ls /tmp/abc && touch /tmp/abc/hehe
```
![Alt text](/images/redirect/1531227991010.png)

```
ls /tmp/abc || mkdir  /tmp/abc
```
![Alt text](/images/redirect/1531228101341.png)

```
ls /tmp/abc || mkdir /tmp/abc && touch /tmp/abc/hehe
```
执行过程：先判断是否有/tmp/abc 目录，没有则创建/tmp/abc目录，返回$?=0 然后与&& 判断为正确，则创建hehe文件 
![Alt text](/images/redirect/1531228167584.png)

```
ls /tmp/vbirding || echo "no exist" && echo "exist"
```
![Alt text](/images/redirect/1531228569218.png)
