### Centos6 yum安装相关问题与处理

> 由于要使用yum下载文件，突然yum下载不了想要的文件，想更换yum源，结果得重新安装yum

#### 一、问题1
```
[root@bogon ~]# yum -v
There was a problem importing one of the Python modules
required to run yum. The error leading to this problem was:

   No module named yum

Please install a package which provides this module, or
verify that the module is installed correctly.

It's possible that the above module doesn't match the
current version of Python, which is:
2.6.6 (r266:84292, Nov 22 2013, 12:16:22) 
[GCC 4.4.7 20120313 (Red Hat 4.4.7-4)]

If you cannot solve this problem yourself, please go to 
the yum faq at:
  http://yum.baseurl.org/wiki/Faq
```

**解决方法与步骤：**

1. 清除原有RHEL的YUM及相关软件包。
```
rpm -qa | grep yum | xargs rpm -e --nodeps
rpm -qa |grep python-urlgrabber|xargs rpm -e --nodeps
```
2. 下载centos6的相关软件包。
python-urlgrabber-3.9.1-11.el6.noarch.rpm
python-iniparse-0.3.1-2.1.el6.noarch.rpm
yum-3.2.29-81.el6.centos.noarch.rpm
yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
```
wget http://mirrors.ustc.edu.cn/centos/6/os/x86_64/Packages/python-urlgrabber-3.9.1-11.el6.noarch.rpm
wget http://mirrors.ustc.edu.cn/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm
wget http://mirrors.ustc.edu.cn/centos/6/os/x86_64/Packages/yum-3.2.29-81.el6.centos.noarch.rpm
wget  http://mirrors.ustc.edu.cn/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
wget  http://mirrors.ustc.edu.cn/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
```

注意：
如果文件无法下载，就可能是版本问题，重新到http://mirrors.ustc.edu.cn/centos/6/os/x86_64/Packages/目录下载相关文件

3. 安装下载的安装包

```
rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm
rpm -ivh python-urlgrabber-3.9.1-11.el6.noarch.rpm 
rpm -ivh yum-metadata-parser-1.1.2-16.el6.x86_64.rpm               
rpm -ivh yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm  yum-3.2.29-81.el6.centos.noarch.rpm 

```
4. 检查是否安装成功

```
 rpm -qa | grep yum
 rpm -qa | grep python
```

--------------

#### 二、问题2 --  http://apt.sw.be/redhat/el6/en/x86_64/dag/repodata/repomd.xml: [Errno 14] PYCURL ERROR 6 - "Couldn't resolve host 'apt.sw.be'" 问题处理

```
[root@bogon yum.repos.d]# yum makecache
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
base                                                                       | 3.7 kB     00:00     
http://apt.sw.be/redhat/el6/en/x86_64/dag/repodata/repomd.xml: [Errno 14] PYCURL ERROR 6 - "Couldn't resolve host 'apt.sw.be'"
尝试其他镜像。
错误：Cannot retrieve repository metadata (repomd.xml) for repository: dag. Please verify its path and try again
```

>  这个问题最后明白yum源地址不行了，得更换了； 

解决方法：

1.  cd /etc/yum.repos.d 进入这个目录，里面有个文件dag.repo 文件(可能名称会不一样)里面的 baseurl=http://apt.sw.be/redhat/el$releasever/en/$basearch/dag

2.  vi 编译一下里面的地址baseurl, 把后面的地址更换
我索性网上找到了有一篇文章[https://blog.csdn.net/zhujiangm/article/details/42614105](https://blog.csdn.net/zhujiangm/article/details/42614105)有很多个地址，然后就替换进去，不怕地址都用不了。

```
[dag]
name=Dag RPM Repository for Red Hat Enterprise Linux
baseurl=http://archive.cs.uu.nl/mirror/dag.wieers/redhat/el$releasever/en/$basearch/dag
        http://apt.sw.be/redhat/el$releasever/en/$basearch/dag
        http://dag.linux.iastate.edu/dag/redhat/el$releasever/en/$basearch/dag
        http://ftp.riken.jp/Linux/dag/redhat/el$releasever/en/$basearch/dag/
        http://ftp2.lcpe.uni-sofia.bg/freshrpms/pub/dag/redhat/el$releasever/en/$basearch/dag
        http://ftp.heanet.ie/pub/freshrpms/pub/dag/redhat/el$releasever/en/$basearch/dag
        http://ftp-stud.fht-esslingen.de/dag/redhat/el$releasever/en/$basearch/dag
        http://mirror.cpsc.ucalgary.ca/mirror/dag/redhat/el$releasever/en/$basearch/dag
        http://mirrors.ircam.fr/pub/dag/redhat/el$releasever/en/$basearch/dag
        http://rh-mirror.linux.iastate.edu/pub/dag/redhat/el$releasever/en/$basearch/dag
        http://rpmfind.net/linux/dag/redhat/el$releasever/en/$basearch/dag
        http://wftp.tu-chemnitz.de/pub/linux/dag/redhat/el$releasever/en/$basearch/dag
        http://www.mirrorservice.org/sites/apt.sw.be/redhat/el$releasever/en/$basearch/dag

gpgcheck=1
enabled=1

gpgkey=http://dag.wieers.com/rpm/packages/RPM-GPG-KEY.dag.txt
```

3. 使用如下命令更新到最新系统

```
yum -y update 
# -y 代表自动回答yes来开始下载与安装
```
