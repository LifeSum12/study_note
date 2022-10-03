## 目录结构

### 总体介绍

Windows系统是：分C/D/E等盘。 

Linux系统的文件系统采用层式的树状目录结构，在此结构最上层的根目录 “/” 。

![img](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209252041557.jpeg)

在Linux系统里，一切皆文件。（如：cpu，gpu等硬件，在linux是以文件存储的，在device目录下）



### 具体目录介绍

- https://www.runoob.com/linux/linux-system-contents.html

常用的：

**opt：下载的软件放这里**

>  opt 是 optional(可选) 的缩写，这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。

**root：用管理员登陆时，默认进入的目录**

> ​	桌面放在该目录。

**/usr/local ：把软件放这里，类似Windows的program File**



---


- **/bin**：
  bin 是 Binaries (二进制文件) 的缩写, 这个目录存放着最经常使用的命令。

- **/boot：**
  这里存放的是启动 Linux 时使用的一些核心文件，包括一些连接文件以及镜像文件。

- **/dev ：**
  dev 是 Device(设备) 的缩写, 该目录下存放的是 Linux 的外部设备，在 Linux 中访问设备的方式和访问文件的方式是相同的。

- **/etc：**
  etc 是 Etcetera(等等) 的缩写,这个目录用来存放所有的系统管理所需要的配置文件和子目录。

- **/home**：
  用户的主目录，在 Linux 中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的，如上图中的 alice、bob 和 eve。

- **/lib**：
  lib 是 Library(库) 的缩写这个目录里存放着系统最基本的动态连接共享库，其作用类似于 Windows 里的 DLL 文件。几乎所有的应用程序都需要用到这些共享库。

- **/lost+found**：
  这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。

- **/media**：
  linux 系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux 会把识别的设备挂载到这个目录下。

- **/mnt**：
  系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在 /mnt/ 上，然后进入该目录就可以查看光驱里的内容了。

- **/opt**：
  opt 是 optional(可选) 的缩写，这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。

- **/proc**：
  proc 是 Processes(进程) 的缩写，/proc 是一种伪文件系统（也即虚拟文件系统），存储的是当前内核运行状态的一系列特殊文件，这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。
  这个目录的内容不在硬盘上而是在内存里，我们也可以直接修改里面的某些文件，比如可以通过下面的命令来屏蔽主机的ping命令，使别人无法ping你的机器：

  ```
  echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
  ```

- **/root**：
  该目录为系统管理员，也称作超级权限者的用户主目录。

- **/sbin**：
  s 就是 Super User 的意思，是 Superuser Binaries (超级用户的二进制文件) 的缩写，这里存放的是系统管理员使用的系统管理程序。

- **/selinux**：
   这个目录是 Redhat/CentOS 所特有的目录，Selinux 是一个安全机制，类似于 windows 的防火墙，但是这套机制比较复杂，这个目录就是存放selinux相关的文件的。

- **/srv**：
   该目录存放一些服务启动之后需要提取的数据。

- **/sys**：

  这是 Linux2.6 内核的一个很大的变化。该目录下安装了 2.6 内核中新出现的一个文件系统 sysfs 。

  sysfs 文件系统集成了下面3种文件系统的信息：针对进程信息的 proc 文件系统、针对设备的 devfs 文件系统以及针对伪终端的 devpts 文件系统。

  该文件系统是内核设备树的一个直观反映。

  当一个内核对象被创建的时候，对应的文件和目录也在内核对象子系统中被创建。

- **/tmp**：
  tmp 是 temporary(临时) 的缩写这个目录是用来存放一些临时文件的。

- **/usr**：
   usr 是 unix shared resources(共享资源) 的缩写，这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于 windows 下的 program files 目录。

- **/usr/bin：**
  系统用户使用的应用程序。

- **/usr/sbin：**
  超级用户使用的比较高级的管理程序和系统守护程序。

- **/usr/src：**
  内核源代码默认的放置目录。

- **/var**：
  var 是 variable(变量) 的缩写，这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。

- **/run**：
  是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。如果你的系统上有 /var/run 目录，应该让它指向 run。

在 Linux 系统中，有几个目录是比较重要的，平时需要注意不要误删除或者随意更改内部文件。

**/etc**： 上边也提到了，这个是系统中的配置文件，如果你更改了该目录下的某个文件可能会导致系统不能启动。

**/bin, /sbin, /usr/bin, /usr/sbin**: 这是系统预设的执行文件的放置目录，比如 **ls** 就是在 **/bin/ls** 目录下的。

值得提出的是 **/bin**、**/usr/bin** 是给系统用户使用的指令（除 root 外的通用用户），而/sbin, /usr/sbin 则是给 root 使用的指令。

**/var**： 这是一个非常重要的目录，系统上跑了很多程序，那么每个程序都会有相应的日志产生，而这些日志就被记录到这个目录下，具体在 /var/log 目录下，另外 mail 的预设放置也是在这里。



### 文件常用操作

接下来我们就来看几个常见的处理目录的命令吧：

- ls（英文全拼：list files）: 列出目录及文件名
- cd（英文全拼：change directory）：切换目录
- pwd（英文全拼：print work directory）：显示目前的目录
- mkdir（英文全拼：make directory）：创建一个新的目录
- rmdir（英文全拼：remove directory）：删除一个空的目录
- cp（英文全拼：copy file）: 复制文件或目录
- rm（英文全拼：remove）: 删除文件或目录
- mv（英文全拼：move file）: 移动文件与目录，或修改文件与目录的名称



### 其他常用命令

- sudo：命令以系统管理者的身份执行指令

​	例如：sudo yum install glibc.i686  #使用管理身份yum安装软件。



## 远程管理

### 介绍

实际开发中，Linux服务器是开发小组共享的，放到公网上(即所有国家人民都能访问)。

因此，程序员需要进行远程登陆到Linux进行项目管理或开发。

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528180334715.png" alt="image-20220528180334715" style="zoom:67%;" />

远程登陆：**xshell技术** （输命令操作等）

远程传输(上传or下载)：**xftp技术**



###  远程登陆

**Xshell6 技术**

![image-20220528180649036](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528180649036.png)

配置步骤：

![image-20220528182636709](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528182636709.png)

- 名称随意填，主机必须填连接服务器的ip地址

​		ifconfig: 查看网络信息(IP地址)

​		ping + IP地址: 检查两台设备网络是否连通（检查当前ip地址与**该ip地址**网络是否连通） 



### 远程传输

Xftp7

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528195504968.png" alt="image-20220528195504968" style="zoom:67%;" />

同样步骤，新建连接。输入ip地址，账号密码。

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528195301995.png" alt="image-20220528195301995" style="zoom:67%;" />

左边为主机，右边为Linux。文件右键即可传输。



## Vi和Vim介绍

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528200404747.png" alt="image-20220528200404747" style="zoom:80%;" />

如： vim hello.java



### Vi和Vim常用的三种模式

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528200443896.png" alt="image-20220528200443896" style="zoom:80%;" />



### Vi和Vim各个模式的切换

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528200540524.png" alt="image-20220528200540524" style="zoom: 80%;" />



### Vi和Vim快捷键

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528200731019.png" alt="image-20220528200731019" style="zoom:80%;" />

https://www.bilibili.com/video/BV1Sv411r7vd?p=17&spm_id_from=pageDriver



## 常用指令



#### 关机&重启命令

![image-20220528201053884](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528201053884.png)

shutdown -h （halt）

shutdown -r  （reboot）



#### 用户登录和注销

![image-20220528201734055](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528201734055.png)

若：在普通用户切到root，输入logout命令，系统会退回到普通用户界面，再次logout退出系统(要在shell执行才有效)。





## 解压/安装/下载相关

### 解压解压/打包类

**压缩格式**

​	在Windows下最常见的压缩文件就只有两种，一是**.zip**，另一个是.rar。

​	但Linux就不同 了，它有**.gz**、**.tar.gz**、**tgz**、**bz2**、**.Z**、**.tar**等众多的压缩文件名，此外windows下的.zip和.rar也可以在Linux下使用，不过在Linux使用.zip和.rar的人就太少了。

**打包和压缩**

​	打包是指将一大堆文件或目录什么的变成一个总的文件，压缩则是将一个大的文件通过一些压缩算法变成一个小文件。

​	如：Linux中的很多压缩程序只能针对一个文件进行压缩，这样当你想要压缩一大堆文件时，你就得先借助另外的工具将这一大堆文件先打 成一个包，然后再就原来的压缩程序进行压缩。



**1. gzip/gunzip 指令**

```linux
#压缩文件，将文件压缩为.gz格式
gzip 文件
#例如： gzip /home/hello.txt

#解压文件
gunzip 文件.gz
#例如： gzip /home/hello.txt.gz
```

**2. zip/unzip 指令**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209252026051.png" alt="image-20220925202647977"  />



**3.tar 指令**

tar是打包+压缩指令，最后生成的是.tar.gz文件。

**常用指令**（f是必须带的，且必须最后写）

```linux
#解压
tar -xvf 文件名 //用于解压.tar后缀结尾的
tar -zxvf 文件名 //用于解压.tar.gz 后缀结尾的
```

![image-20220925203316876](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209252033971.png)







### rpm

**介绍**

![image-20220925195716581](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209251957663.png)

![image-20220925200635556](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209252006751.png)



**查询相关命令**

查询所有已安装的rpm列表，通过grep过滤

```linux
rpm -qa
rpm -qa | grep firefox #例子：查询火狐浏览器
```

查看软件包信息

```linux
rpm -qi 软件包名
rpm -qi firefox #例子：查询火狐浏览器信息
```

查看软件包中的文件

```linux
rpm -ql 软件包名
rpm -ql firefox #例子：查询火狐浏览器所有文件
```

查看文件所属的软件包

```linux
rpm -qf 文件全路径名
rpm -qf /root/install.log #例子：查询该文件所属的软件包名
```

**卸载相关命令**

```linux
rpm -e rpm包的名称	// 普通删除模式　
rpm -e --nodeps rpm包的名称　　// 强力删除模式，如果使用上面命令删除时，提示有依赖的其它文件，则用该命令可以对其进行强力删除
```

**安装相关命令**

```linux
rpm -ivh RPM包的全路径名称（安装的文件.rpm）
```

![image-20220925201003252](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209252010380.png)



### yum

（无需本地安装包，可从远程服务器下载）

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209252012840.png" alt="image-20220925201214706" style="zoom:80%;" />



**基本指令** 

查看远程yum服务器的软件，显示该软件所有可下载版本。

```linux
yum list|grep 软件名
```

从服务器下载软件，还会自动下载所需的依赖。

```linux
yum install 软件名
```



### wget方式

![image-20220926133830695](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209261338745.png)





## Web项目**部署**

**软件安装四种方式：**

![image-20220913200425116](C:/Users/10275/AppData/Roaming/Typora/typora-user-images/image-20220913200425116.png)

Linux需要的软件可从Window电脑通过Xftp上传，或者Linux火狐浏览器下载。

### 软件准备

JDK1.8和JDK8是同一个版本 ,最开始 命名为 JDK1、JDK2....后来就 命名为 JDK1.7、JDK1.8....

从JDK 5.0开始，开始分三个版本 SE、EE、ME来更新。EE和ME则是在基础上扩展新功能，SE则为标准库。

- **推荐使用 JDK1.8 版本，数据库 推荐使用5.7版本，该版本比较稳定，兼容性较好。**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209252056825.png" alt="image-20220925205651757" style="zoom:67%;" />

步骤一：**安装JDK**。

配置环境变量JAVA_HOME，直到可以在Linux命令行验证java版本号。

（为了能在任何目录都能使用java javac命令，配置环境变量。不然只能在安装的目录使用，每次都需要打目录前缀）

```
安装命令: tar -zxvf	文件名.tar.gz	-C /usr/local
```

<img src="C:/Users/10275/AppData/Roaming/Typora/typora-user-images/image-20220913203931977.png" alt="image-20220913203931977" style="zoom: 50%;" />

- JAVA_HOME 自行添加jdk位置
- PATH是系统本来就有的，需要把配置的 JAVA_HOME 添加进入
  - 末尾的 $PATH 是指把原先的目录命令也加进来。不然以后系统查找命令只在jdk目录找了。

测试：

​	javac -version 编译工具（把.java文件.class）

​	java -version 运行工具（把.class文件运行）



步骤二：**安装Web服务器(Tomcat)**，打开启动文件，通过查看进程来看tomcat是否启动成功。

**注意：由于springboot集成了tomcat，所以无需安装。因此springboot项目打包的时候不再使用war，而是使用jar。**

​				Linux防火墙默认是关闭的，需要设置对8080端口进行开放，直到其他电脑可以访问到tomcat首页。

![image-20220914174702627](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209141747701.png)

步骤三：**安装MySql数据库服务**，通过Linux命令来启动MySql服务，并设置开机启动。

​				登录MySql查看初始密码，修改密码后，登录MySql查看。

​				防火墙设置开启3360/tcp端口，直到其他电脑可以通过SQLYog、Navicat等软件进行连接。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209261336275.png" alt="image-20220926133631194" style="zoom:80%;" />

- wget指令：通过网络获取

### 项目部署

**手动部署：**

​	在Idea通过Maven package命令，将Springboot项目编译、打包成 Jar包。

​	将jar包上传到Linux服务器，通过Linux命令`java -jar xxx.jar` 来运行该项目。

**自动部署**：

​	Linux安装软件：

​		**安装Git，用来拉取项目文件。**可通过Linux命令在线安装。

​		**安装Maven，用来编译打包项目文件**。需要下载压缩包安装，安装后需要配置环境变量。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209131336063.png" alt="image-20220913133625959" style="zoom:67%;" />





Linux目录
