## linux命令入门

1.命令格式
  命令 [-选项] [-参数]
如 ls  -la  /etc

1. 多个选项可以合并 -a -l -> -la

2. 简化选项与完整选项 -a --all 

   

### linux常用命令 文件处理命令

1. #### 目录处理命令ls

1) 命令名称ls (list)

2) 命令所在路径 /bin/ls

3) 执行权限 所有用户

4) 功能 显示目录文件

5) 语法 ls [-aldih] [文件或目录]
	-a:显示所有文件(包括.隐藏文件) -l 详细信息显示 -d 查看目录属性
	(all)				(long)		(dir?)

6)使用 -l 后出现的一些概念 
引用记数 -l结果前的数字

所有者 root 创建该文件的人,且只有一个,但是可以变更

所属者 用户组集  只有一个,授权相同类型的用户可以有特殊权限

其他人 即非所有者,非所属者

文件大小 默认单位为Byte -h选项(统计文件大小通用命令)：homo 人性化选项

linux的三个时间(统称为更改时间)：数据修改 状态修改 最后一次访问时间

-rw-r--r--的含义:
 第一个字符 文件类型 (-二进制文件 d目录 l软链接文件)
 rw- r-- r--
 代表u(所有者) g(所属组) o(其他人)的可操作权限
 r读 w写 x执行(execute) -空权限

 ls -i 获取文件inode(系统用于检索文件的手段)

 2. #### 目录处理命令 mkdir

 创建目录 make directories
  命令所在路径 /bin/mkdir
  执行权限 所有用户
  语法 mkdir -p [目录名] [目录名]... (-p 递归创建)
  特点 可以递归创建多个目录 可以同时创建多个目录

 3. #### 目录处理命令 cd

 进入到指定目录 change directory
  命令所在路径 shell内置命令
  执行权限 所有用户
  语法 cd [目录名]
  $ cd /tmp/game 切换到指定目录

  $ cd ~ 回到当前用户家目录

  $ cd .. 回到上一级

 4. #### 目录处理命令 pwd
    

  打印当前目录完整路径

 5. #### 目录处理命令 rmdir (应用不多)
    

  remove empty directories 删除空目录
    rmdir [空目录名]

 6. #### 目录处理命令 cp

 复制文件或目录
  执行权限 所有用户
  cp [-r/-rp/-p] [原文件或目录] [目标目录]
  复制文件可以不加选项，复制目录必须加-r
  -r 复制目录  -p 保留文件属性  -rp 前二都保留

 特点 可以选择多个文件或目录复制到目标目录
	 -p 如保留最后更改时间时,可以使用

 复制的同时进行一个更名
 cp [目录名] [目录+名称]

 7. #### 目录处理命令 mv

 剪切文件并改名
  mv [目录名] [目标目录]

 8. #### 目录处理命令 rm

 /bin/rm
  执行权限 所有用户 
  语法 rm [-rf] [文件或目录]

 	  -r 删除目录 -f 强制执行
 强制删除不会提示是否删除

 9. #### 文件处理命令 touch

 创建一个空文件
  touch [文件名] [文件名]...
  使用空格命名 touch "program files" 但是不推荐这样命名

 10. #### 文件处理命令 cat

      查看文件内容 cat [目录文件名]
      cat -n /linux/note  (-n 分行显示)

 11. #### 文件处理命令 tac

       倒着显示文件内容

 12. #### 文件处理命令 more

       分页显示文件内容 人性化 good
       空格/F翻页 回车一行 Q退出 B回翻

 13. #### 文件处理命令 less

       page up/page down 翻页 
       /[关键词]：可搜索关键词   next翻页找其他匹配行

 14. #### 文件处理命令 head

       head -n [行号] [目录文件名]
       只显示文件前几行

 15. #### 文件处理命令 tail

       tail [-nf] [行号] [目录文件名]
       显示倒数几行
       -f 动态显示,终端进入等待模式

 16. #### 文件处理命令 ln

       link 即为链接
       ln -s [原文件] [目标文件]
        参数 -s 为 创建软链接
        不带参数 则创建硬链接
       功能：生成链接文件

 硬链接 -rw-r--r-- 类型不同,与cp -p功能类似,但可以与原文件同步更新	
 软链接 lrwxrwxrwx 权限不同,有箭头指向 功能类似WINDOWS的快捷方式

 删除原文件后，软链接失效(找不到原文件),硬链接依然可以访问
 		
 观察INODE：原文件与硬链接INODE相同，软链接则不同
 一个文件只有一个INODE，但一个INODE可以对应多个文件(硬链接)

 硬链接不能跨分区 不能针对目录使用
 软链接则可以



###  linux常用命令 权限管理命令

  ugo rwx

 1. #### **chmod** 修改文件权限
    

  只有root和所有者才能修改文件权限
    chmod [{ugoa}{+-=}{rwx}] [目录文件名] 

  	[mode=421] [目录文件名]
  	-R 递归修改(连同子目录一起被更改)
  chmod g+w,o-r [文件目录名]

  linux一般使用数字表示权限 r为4 w为2 x为1
  rwx对应7 rw-对应6 r--对应4
  再如 532 r-x-wx-w-

  **对权限rwx的理解(重要)**
  对文件：r 可以查看文件内容 w 可以修改文件内容 x 可以执行文件
  对目录：r 可以列出目录内容 w 可以在目录创建和删除文件 x 可以进入目录

 2. #### **chown** 修改文件所有者
    

  只有root权限可以更改
    chown [用户] [文件或目录]
    使用前注意用户必须存在

 3. #### **chgrp** 修改文件所属组
    

  chgrp [用户组] [文件或目录]

 4. #### **umask** 查看缺省权限 
    

  umask [-S] 显示和设置文件的缺省权限
    文件缺省权限中不会有x
    umask 022 设置缺省权限，依靠异或运算得到缺省权限
    缺省权限为 rwx r-x r-x 目录

  	     rw- r-- r-- 二进制文件权限

 


###  linux常用命令 文件搜索命令

1. #### **find** 文件搜索

   **find [搜索范围] [匹配条件]**

   匹配条件默认为精确搜索

   模糊搜索需要使用通配符 

   **\*** : 匹配任意一个字符

   **?**：匹配单个字符

   1. **-name**  根据文件名查找

      find /etc -name init

   2. **-iname** 不区分大小写

      find /etc -iname init

   3. **-size** 根据文件大小查找

      find / -size +204800

      +n 大于 (**单位为数据块** 即为512B,linux存储文件的最小单位)

      -n 小于

   4. **-user** 根据所有者查找

   5. **-group** 根据所属组查找

   6. 三个时间查找

      find /etc -amin -5 

      单位时间为分钟,意味几分钟内/外

      1. **-amin** 访问时间 access
      2. **-cmin** 文件属性(读写权限、所有者、所属组等等)
      3. **-mmin** 文件内容(如文本内容)

   7. 连接符

      **-a** 两个条件同时满足	**-o** 满足其中一个

      **-exec** 搜索后执行命令  **-ok** 搜索后执行命令并确认(如find后rm)

      find /etc -name init -exec ls -ld {} \; ({} \为固定格式)

   8. **-type** 根据文件类型查找

      -type f d l (二进制 目录 软链接)

   9. **-inum** 根据iNode查找

2. #### locate  文件快速检索

   建立一个文件资料库,查找速度很快

   在文件资料库中查找文件(如同索引)

   locate 不能找到/tmp/下的文件(临时文件)

   **updatedb** 更新文件资料库,需要定期维护文件资料库

   **数据库保存路径**：/var/lib/mlocate/mlocate.db

3. #### which 查找命令目录

   which rm

   可以显示命令绝对路径 和 别名(输入命令时首先使用别名) 如：rm -> rm -i

4. #### whereis 

   whereis ls

   搜索命令所在目录及帮助文档

5. #### grep

   特殊查找命令：在文件内容中查找某一关键词所在的一行

   grep [-iv] [关键词] [文件路径]

   选项 -i 不区分大小写	-v 排除制定字符串

   例如去除注释行 grep -v # /etc/inittab
   
   

### linux常用命令 帮助命令

1. #### man

   意为manual 手册 ，位于/usr/bin/man

   man ls 查询命令帮助文档

   man services 查询配置文件帮助文档

   使用方法参考more 和 less

   两种帮助文档类型：man1/passwd.1.gz(命令)	man5/passwd.5.gz(配置文件)

   优先查看命令 查看配置文件: man 5 passwd

2. #### whatis

   whatis [命令名称]

   直接读取命令的简介
   $ what is ls

3. #### apropos 

   apropos [配置文件名]

   直接获取配置文件的简介

4. #### --help

   列出命令的选项

   rm --help

5. #### help

   Shell即为命令解释器

   查看Shell内置命令的帮助文档

   help umask

   help pwd等等

6. #### info

   功能同man
   
   

### linux常用命令 用户管理命令

1. #### useradd 添加一个用户

   执行权限 root

   useradd van

2. #### passwd 更改用户密码

   本用户 passwd

   root权限 passwd [用户名]

3. #### who 查看登录用户信息

   获取简单登录用户信息

   用户 终端 时间 IP

   root pts/0 2021-11-30 ...(192.168.1.1)

   pts 远程终端

   tty 本地终端

4. **w 详细登录用户信息**

   IDLE 空闲时间 LOGIN@ 用户登录时长 JCPU 所有进程使用时间

    PCPU 当前进程使用时间 WHAT 用户当前进程

   

### linux常用命令 解压缩命令

1. #### .gz 格式

   1. **gzip** [文件名] 	压缩.gz文件

   2. **gunzip** [.gz文件名]     解压.gz文件 

   3. 注意：gz只能压缩文件,不能压缩目录

      ​		  gz压缩后不保留原文件,注意cp备份

2. #### tar 压缩目录

   压缩后就为最常见的压缩格式 : .tar.gz

   **tar [-cvfz] [压缩后文件名] [目录]**

   选项 -c  打包 -v  显示详细信息 -f  指定文件名 -z 打包同时压缩为.gz

3. #### tar 解压目录

   **tar [-zxvf] [已压缩文件名] [解压后目录名]**

   其他选项不变 

   -x 解压 
   
   -C [文件名] 解压到
   
4. #### zip 压缩

   **zip [-r] [压缩文件名] [文件或目录]**

   选项 -r 压缩目录

   与gz的不同：可以压缩目录,保留原文件

5. #### unzip 解压

   **unzip [压缩文件]**

6. #### bzip2 升级版解压缩

   压缩比更高 , 也可以保留原文件 , 压缩后文件 .bz2

   **bzip2 [-k] [文件]**

   选项 -k 是否保留原文件

   如何配合tar使用(将z替换为j)：

   **tar -cjf [文件名]** -> 打包并压缩为.tar.bz2

   **tar -xjf [文件名]** -> 解压tar.bz2文件



### linux常用命令 网络命令

1. #### write 给登录用户发送信息

   write [用户]

   ctrl+back 退格 

   ctrl+D 结束并发送

2. #### wall 给所有用户发送信息

   wall [message]

   广播信息

3. #### ping 测试流通性

   ping [选项] [IP]

   ping -c 4 192.168.1.10 (ping4次)

4. #### ifconfig 网络配置

   ifconfig 显示配置信息

   ifconfig [网卡名] [IP] 设置网卡信息

5. #### ipaddr 同ifconfig

6. #### mail 

   mail [用户名] 发送邮件

   mail 邮件列表

7. #### last

   较常用

   查看过去登录用户的详细信息

   **lastlog** 检查用户的登录情况 

   **lastlog -u [uid]** 检查特定用户的登录情况

8. #### traceroute

   显示数据包到目标主机的路径

   同Window traceroute

9. #### netstat 查询网络状态

   三个选项

   -tlun 查看计算机已开启的端口(启动了哪些服务)

   -an 查看所有网络连接

   -rn 查看本机路由

10. #### setup (RedHat linux专有)

11. #### mount 挂载设备

    挂载光盘、U盘等等

    图形界面可以自动挂载，但命令行需要手动

    **mount [设备名] [挂载位置]**

    **mount /dev/sr0 /media/yuange**

12. #### umount 卸载设备

    umount [设备名]

    退出挂载点后,卸载设备



### linux常用命令 关机重启命令

1. #### shutdown 关机命令

   shutdown [选项] 时间

   shutdown -h 关机

   shutdown -r 重启

   shutdown -c 取消前一个关机命令

   shutdown -r now 现在重启

   shutdown -r 

2. #### 其他重启命令

   reboot 

   init 6

3. #### init命令

   7个运行级别：

   0~6 关机、单用户、无NFS多用户、完全多用户、未分配、图形界面、重启

   ​	halt	single user mode	multiuser	unused 	X11	reboot

   它的配置文件 /etc/inittab

4. #### runlevel命令

   查看当前运行级别

   **runlevel**

   N 5 (之前一个级别，现在的级别)

5. #### logout命令

   注销当前登录用户

   ubantu使用：exit
   
   

### 常用命令补充：

1. **管道符 |** 

   使用管道操作符 | 可以把一个命令的标准输出传送到另一个命令的标准输入中

   连续的 | 意味着第一个命令的输出为第二个命令的输入，第二个命令的输入为第一个命令的输出，依次类推。

   常见用法

   ls /home/note.md | less

   ps -ef | grep pycharm

2. **命令的别名**

   **alias** 查询所有别名

   优先级高于系统命令

   **定义别名**：

   **alias ser='service network restart'**

   **保存别名的隐藏文件** 

   /root/.bashrc

   使用vi打开即可添加别名

3. **快捷键**

   **Tab**	命令或文件补全，按两下出提示

   **ctrl+A** 光标移动到命令行开头

   **ctrl+E** 光标移动到命令行结尾

   **ctrl+U** 剪切光标前的命令行

   **ctrl+Y** 粘贴ctrl+U剪切的内容

   

### 	文本编辑器 Vim

​	 建立、编辑、显示文本文件

1. #### 常用操作

   1. 三个工作模式

      命令模式

      ​	**vi [filename]** 进入文件或创建新文件	

      ​	**:wq**(编辑模式)退出

      ​	所有键入都会被当作命令执行

      编辑模式

      ​	命令模式下输入 **:** 进入

      ​	按esc退出编辑模式,进入命令模式

      插入模式

      ​	命令模式下，输入**i/a/o** 进入

      ​	键入ESC退出

      ​	即编辑文本功能

   2. 插入命令

      **a** 光标字符后插入 

      **A** 光标行尾插入

      **i** 光标字符前插入

      **I** 光标行首插入

      **o** 光标下插入新行

      **O** 光标上插入新行

   3. 行定位命令(经常使用)

      **: set nu** 	  设置行号

      **: set nonu**   取消行号

      **: n** 到第n行

      （以上为编辑模式命令）

      **gg** 到第一行

      **G** 到最后一行

      **nG** 到第n行

      **$** 移至行尾

      **0** 移至行首

   4. 字符定位命令(经常使用)

      **w** 移动到下一个单词头部  **3w**移动到下3个单词头部

      **b** 移动到前一个单词头部   **2b**移动到前两个单词头部

      **e** 移动到下个单词尾部

      **ge** 移动到前一个单词尾部

      字符串移动

      W	B	E	gE

      W/w	E/e 下一个头/尾

      B/b	gE/ge	前一个头/尾

   5. 删除命令

      **x** 删除光标字符

      **nx** 删除光标后n个字符

      **dd** 删除光标行

      **dG** 删除光标所在行到**文件内容最后**

      **D** 删除光标所在到**行尾** 

      **:[n1],[n2]d**

   6. 复制和剪切命令

      **yy** 复制当前行

      **nyy** 复制当前行以下n行

      **dd** 剪切当前行

      **ndd** 剪切当前行以下n行

      **p**	粘贴在当前光标所在行下

      **P**    粘贴在当前光标所在行上

   7. 替换和取消命令

      **r** 	取代光标所在处字符

      **R**	进入替换模式

      **u**	取消上一步操作 (ctrl + z)

   8. 搜索和搜索替换命令

      **/[string]** 进入搜索编辑模式,搜索指定字符串

      ​			 **:set ic** 搜索忽略大小写

      **n** 搜索指定字符串下一个出现位置

      **:%s/[old]/[new]/g**	全文替换指定字符串

      **:n1,n2s/[old]/[new]/g** 一定范围(行数)内替换指定字符串

   9. 保存退出命令

      **:w** 保存

      **:w [file_name]** 另存为指定文件

      **:wq** 保存修改并退出

      **ZZ** 快捷键,保存修改并退出

      **:q!** 不保存修改退出

      **:wq!** (文件所有者和root)无视权限,强行保存

2. #### 使用技巧

   /home/{username}/.vimrc

   /root/.vimrc

   ...

3. #### 在Eclipse使用Vi/Vim

4. #### 在IDEA使用Vi/Vim



### Linux软件包管理

1. 软件包简介

   源码包	（C语言）：

   编译安装，可以直接删除目录；安装较慢

   二进制包	：

   （经过编译）RPM包(RedHat package manager)	系统默认包

   CentOS使用RPM包	Ubuntu使用DEB包(apt)

   通过命令安装，安装速度快

   软件包之间有依赖性

2. #### RPM包 rpm命令管理

   1. 命名原则

      httpd-2.2.15-15.el6.centos.l.i686.rpm

      包名-版本-发布次数-linux版本-硬件版本-rpm标识

   2. 依赖性(最大的问题)

      树形依赖 a->b->c

      环形依赖 a->b->a

      模块依赖 查询www.rpmfind.net

   3. 包全名和包名

      操作的包未安装，使用包全名

      操作已经安装的软件包时，使用包名

      （/var/lib/rpm下保存有数据）

   4. 安装

      **rpm -ivh** 包全名

      -i 				安装

      -v				显示详细信息

      -h				显示进度

      --nodeps	不检测依赖性

   5. 升级和卸载

      rpm -e 包名 (rpm -e httpd)

      -e（erase) 卸载

      rpm -Uvh 包全名

      -U (upgrade) 升级
      
   6. 查询安装

      rpm -q 包名	   查询该包是否安装

      rpm -qa 	    	列出所有安装包

      rpm -qi 包名	  列出该包的信息

      

   7. 

3. #### RPM包 yum在线管理

   yum管理的同样是RPM包

   1. IP地址配置和网络yum源

      1. **虚拟机Hyper-V中**

         https://blog.csdn.net/chenjh213/article/details/88941756

      2. set up 命令

         网络配置

         选择网卡，配置IP信息

      3. 修改配置文件

         **vi /etc/sysconfig/network-scripts/ifcfg-eth0**

         修改ONBOOT 

         DNS文件配置

         **vi /etc/resolv.conf**

         重启网路服务

         **service network restart**

      4. yum源

         **cd etc/yum.repos.d/**

         联网yum源

         CentOS-Base.repo

         光盘yum源

         CentOS-Media.repo

         网络yum源解析 - 

         **vi /etc/yum.repos.d/CentOS-Base.repo**

         1. [base] 容器名称
         2. name 容器说明
         3. mirrorlist 镜像站点
         4. baseurl  yum源服务器地址
         5. enabled 默认1，容器是否生效
         6. gpgcheck 数字证书验证确认
         7. gpgkey 数字证书公钥

   2. yum命令

      1. 查询

         **yum list**

         查询所有可用软件信息

         **yum search** 包名

         查询相关包信息

         yum search httpd

      2. 安装

         **yum -y install 包名**

         -y : 自动安装

         yum -y install gcc

         安装C语言编译器,源码安装包必须品

      3. 升级

         \# **yum -y update 包名**

         yum -y update

         未加包名，甚至会更新内核

         远程更新时小心

         -y 自动更新

      4. 卸载

         \# **yum -y remove 包名**

         -y 自动删除

         会可能造成其他依赖该包的软件失效

      5. linux服务器安装软件建议

         最小安装，尽量不要卸载

         yum更新和卸载命令谨慎使用

      6. yum软件组管理命令

         \# yum grouplist

         \# yum groupinstall 软件组名

         \# yum groupremove 软件组名

   3. 光盘yum源

      1. 挂载光盘

      **mount /dev/sr0 /mnt/cdrom/**

      2. 让CentOS-Media.repo生效，让其他repo失效

      3. 更改其他三个repo后缀名

         mv CentOS-Base.repo CentOS-Base.repo.bak

      4. 修改CentOS-Media.repo

         **vi /etc/yum.repos.d/CentOS-Base.repo**

         **baseurl=file:///mnt/cdrom**

         修改地址为光盘挂载点

         注释下方默认地址

         \# **file:///...**

         注意：linux配置文件格式要求严格，不要出现缩进和额外注释

4. #### 源码包

   1. 安装后位置的区别

      **RPM包安装默认位置**

      /etc/		配置文件

      /usr/bin	可执行命令

      /usr/lib	  函数库

      /usr/share/doc	软件使用手册

      /usr/share/man/	帮助文件

      **源码包安装位置**

      /usr/local/软件名/

   2. 安装位置不同的影响

      **RPM服务启动**

      /etc/rc.d/init.d/httpd start

      service httpd start

      systemctl httpd start

      **如何打开Apache测试页？**

      iptables -F 关闭防火墙

      service httpd start

      192.168.137.200 成功！

      关闭服务

      service httpd stop

      **在Apache上写上HTML**

      **/var/www/html/**

      **vi home.html**

      \n

      **源码包不能被服务管理命令管理**

      只能访问绝对路径 启动

   3. 安装源码包

      1. 解压缩源码包

         tar -zxvf ...

      2. 进入解压目录

      3. 查看README，常有安装说明

      4. ./configure 软件配置和检查

         ./configure --prefix=/usr/local/apache2

         定义安装目录

         写入MakeFile

      5. make 编译

      6. make install 编译安装

      7. 启动看看

         PREFIX/bin/apachectl start

   4. 卸载源码包

      rm -rf /usr/local/apache2/

      简简单单

   5. 如何选择源码包和RPM包

      对外提供服务的

      如Apache，外部访问量大，源码包性能高

      不对外服务

      如jdk，使用yum即可

   6. 实战 安装mysql

      1. 安装编译包

         **yum -y  install  gcc  gcc-c++  perl ncurses-devel**

      2. 

      

5. **脚本安装包**

   不是独立软件包类型，常见安装的是源码包

   安装过程写成了自动安装的脚本

   使源码包安装更方便

   类似windows软件安装

   1. 解压缩

   2. 执行setup.sh脚本

      ./setup.sh

   3. WEBMIN 基于网页的图形界面linux管理
   
      

### 用户和用户组管理

1. #### 用户配置文件 - 用户信息文件

   linux中通过用户配置文件来查看和修改用户信息

   1. **/etc/passwd**	基本配置文件

      每一行代码一个用户

      用:分割每一个字段

      - 各字段详解：

      **用户:密码标志:UID:GID(初始组):用户说明:家目录:登录之后的Shell**

      - 密码标志 x 说明用户有密码

      - UID含义 0 超级用户

      ​			1-499 系统用户(伪用户)

      ​			500-65535 普通用户 (CentOS7从7开始)

      ​		若把用户UID改为0,操作系统就将其作为root对待

      ​		系统用户不能删除,专门用来启动linux系统服务,不能登录

      - 用户初始组 

        初始组 每个用户必须且只能有一个,可以更改

        附加组 每个用户可以加入多个其他用户组

      - 家目录

        普通用户 /home/用户名/

        root用户 /root/

      - Shell linux的命令解释器

        系统用户		 root用户		普通用户

        /sbin/nologin	/sbin/halt	/bin/bash

        若想禁用普通用户,可以更改

        /bin/bash -> /sbin/nologin

   2. **/etc/shadow 影子文件**

      保存密码密文 SHA512加密

      只有root权限可以读

      - 字段序列

        **用户名:加密密码:密码最后一次修改时间:两次密码间隔:密码有效期:密码修改到期前警告天数:密码失效宽限时间:账号失效时间:保留**

      - 加密密码

        若为 !! 或 * 用户没有密码,代表不能登录

        同样可以用于禁用用户

      - 密码修改/间隔/有效时间

        单位为时间戳 1970年1月1日为起始

        1天为+1；

      - 密码到期警告天数

        密码有效期到期前会提示用户

      - 密码失效宽限时间

        0 或 空 代表密码过期立即失效

        -1 代表永远宽限

      - 账号失效时间

        默认为空

        同样用时间戳表示,时间一到,账号禁用

   3. **/etc/group**

      组信息文件

      - 字段说明

        **组名:组密码位:GID:组中其他用户**

      **/etc/gshadow** 组密码文件

   4. **/etc/skel** 

      用户模板目录,保存一些隐藏文件

      自动生成用户家目录 /home/用户名时，拷贝隐藏文件到家目录

      管理员可以编辑一些文件交给新用户

   5. **/var/spool/mail/user3**

      用户邮箱信息

   6. 创建用户后，用户信息会写入到六个文件

      /etc/passwd

      /etc/shadow

      /etc/group

      /etc/gshadow

      /var/spool/mail/[用户名]

      /home/[用户名]

      手动删除对应文件内容后,即删除了用户

2. #### 用户管理命令

   1. 添加用户
   
      **useradd [用户名]**
   
      **passwd [用户名]**
   
      /etc/shadow若无密码(!!)，则无法登录
   
      除了用于启动服务的伪用户
   
      常用选项
   
      useradd -g t1 p1	初始组（不建议使用）
   
      useradd -G t2 p2	附加组 
   
      ********配置文件：/etc/default/useradd********
   
      INACTIVE=-1 (密码过期宽限天数)
   
      EXPIRE=(密码失效时间)
   
      **配置文件：/etc/login.deft**
   
      shadow文件内其他默认配置
   
   2. 更改密码
   
      **passwd [用户名]** root权限
   
      **passwd** 普通用户更改自己的密码
   
      root权限锁定用户
   
      **passwd -l [用户名]** 
   
      实际上在shadow字段加!!
   
      **passwd -u [用户名]**
   
      解锁
   
      **--sdin** 通过管道符输出数据作为用户密码，不需要交互
   
      用于编写程序，批量添加用户
   
      **echo "123456" | passwd --sdin [用户名]**
   
      --sdin缺陷：history 查看输入过的命令
   
      批量添加用户后,修改shadow的修改时间字段 -> 0，让用户在登录后就必须修改密码
   
      **chage -d 0 [用户名]**
   
      命令修改
   
   3. 用户信息修改
   
      **usermod [选项] 用户名**
   
      usermod -G 组名 [用户名] 
   
      **usermod -G user1 user2 user3**
   
      (将用户user2 user3 附加到user1组 即user1的初始组)
   
      用户加入附加组
   
      usermod -l 更改用户名
   
   4. 删除用户
   
      **userdel p1**
   
      **rm -rf /home/p1**
   
      连同家目录一起扬了
   
      **userdel -r p1**
   
      推荐备份家目录 再一起删除
   
   5. 切换用户
   
      **su [用户名]**
   
3. #### 组管理命令

   添加用户组：**groupadd**

   删除用户组：**groupdel**

   用户添加/删除进组 **gpasswd** 只针对附加组

   1. 用户进组

      **gpasswd -a [用户名] [组名]**

   2. 用户退组

      **gpasswd -d [用户名] [组名]**

   3. 删除用户组

      groupdel 不能删除主组

      useradd -g t1 t5

      组不作为主组时，就可以删

   4. 有效组

      用户创建文件时,文件所有者即为用户，而文件所属组为**该用户的主组**

      **即用户默认有效组为主组**

      **newgrp 组名**

      改变用户的有效组

   5. 权限练习并总结

      作业系统目录 简单权限管理

      1. 分身份

         老师 所有者 tea

         学生 所属组 stu

         **groupadd stu** 

      2. 创建文件并分权限

         **mkdir work_dir**

         修改所有者

         **chown  tea work_dir**

         老师和学生对目录可以

         列出目录内容 r

         新建和修改内部文件 w

         可以进入目录 x

         **chmod 770 work_dir**

      3. 修改文件所属组

         **chgrp work_dir stu**

      4. 将学生编入所属组

         **gpasswd -a stu1 stu2 stu3 work_dir**

      5. **问题**

         学生同样可以**删除**内部文件

         这在后续需要新的权限管理方式解决
         
         

### 权限管理

1. #### ACL权限

   原有的基础权限管理功能不足

   每个文件**只能有一个所属组**,当身份安排情况复杂时，不能处理权限管理问题

   实际问题：老师、旁听生、选课学生在作业系统的权限如何安排

   **ACL**：解决用户对文件身份不足的问题

   解决方案类似Windows的文件权限管理

   旁听生不进所属组，直接作为个体分配对于作业系统的权限

   1. ACL基本命令

      查看文件ACL权限

      **getfacl 文件名**

      为用户/组设置ACL权限

      **setfacl -m u:用户名:权限 文件名** 

      **setfacl -m g:组名:权限 文件名**

      设置后 ls -l 目录权限可能为

      **drwxr-x---+**	后接+说明使用了ACL

   2. 选项明细

      **setfacl -m u:用户名:权限 -R 目录名**

      -R选项说明，对目录内所有文件都生效(递归生效)

      注意：-R只对当前目录生效，不更新后继目录

      若要更新，设置默认

      **setfacl -m d:u:用户名:权限 -R 目录**

      过度使用ACL会导致：权限溢出

   3. 删除ACL权限

      删除用户和用户组的ACL权限

      **setfacl -x u:用户名 文件名**

      **setfacl -x g:用户名 文件名**

      删除文件所有ACL权限

      **setfacl -b 文件名**

2. **sudo权限**

   **给普通用户赋予部分管理员权限**

   /sbin	/usr/sbin/

   在此目录下命令只有超级用户能使用

   注意：赋予的命令越详细(包含选项和参数),该用户的执行权限越小

   1. **visudo**

      (之后按vi命令操作文本)

      **root ALL=(ALL)	ALL**

      用户名 被管理主机地址=(可使用身份)

      授权命令(绝对路径)

      **%group ALL=(ALL)	ALL**

      %组名 被管理主机地址=(可使用身份)

      授权命令(绝对路径)

      \n

      (ALL)可以省略，代表root

   2. **sudo -l**

      获取该用户被赋予的命令 

      如：(root) /sbin/shutdown

      ​		身份  命令

   3. 用户调用

      sudo 被赋予命令

      (sudo 代表root身份)

      **sudo /sbin/shutdown -r now**

   4. 利用sudo为普通用户赋予管理Apache权限

      **user1 ALL=/etc/rc.d/init.d/httpd configtest**

      **user1 ALL=/etc/rc.d/init.d/httpd reload**

      允许某一用户使用服务器管理脚本

      **user1 ALL=/usr/bin/vim /etc/httpd/conf/httpd.conf**

      允许某一用户修改服务器配置文件

   5. sudo使用注意

      细化到选项和参数

      sudo可以分配所有命令，如vim

   6. 授权其他用户也可以添加其他用户

      **user1 ALL=/usr/sbin/useradd**

      允许用户添加新用户

      **user1 ALL=/usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd "", !/usr/bin/passwd root**

      允许用户修改其他用户密码,除了用户自己和root(使用正则和!,逗号分隔多个命令-)

      执行

      sudo /usr/sbin/usradd user2

      sudo /usr/bin/passwd user2

3. #### 文件特殊权限

   SetUID	SetGID	Sticky BIT

   问题：umask 0022

   第一个0就与文件特殊权限相关

   **抛出问题**：为何/etc/shadow的权限为000（只有root可以操作）

   但是用户使用passwd却可以更改自己的密码？

   **解答**：ll /usr/bin/passwd

   ​		  权限为-rwsr-xr-x

   所有者权限出现s，即使用了SetUID

   若组权限出现s，即使用了SetGID

   若其他人出现s，即使用了Sticky BIT

   1. **SetUID**

      chmod u+s [文件名]

      权限-rwSr--r--

      为大写说明文件不能执行

      小写的说明s=S+x

      应该这样设置

      **chmod 4755 [文件名]**

      4代表SetUID,2代表SetGID,1代表SBIT

      **前置条件**：

      1. 只有可执行二进制文件可以设置
      2. 命令执行者必须能执行这个文件(x权限)

      **生成效果**：

      1. 使命令执行者在**命令执行过程**身份转变为**所有者**

         (这就解释了普通用户输入passwd命令可以对shadow文件操作)

      2. 只在程序执行过程中生效

      **使用SetUID的危险**：

      ​	**chmod 4755 /usr/bin/vim**

      ​	任何用户在执行命令后，身份都会变成root，意味着可以篡改所有配置文件 

   2. **SetGID**

      **特点**：SGID既可以对文件使用，也可以对目录使用

      **针对文件**：与SUID相似

      执行程序时，组身份升级为**程序文件所属组**

      **典型例子：locate**命令

      ll /usr/bin/locate

      **-rwx--s--x**	所属组权限有s

      ll /var/lib/mlocate/mlocate.db

      -rw-r-----	所属组权限有r

      故命令执行者在locate执行时对数据库有读权限

      **手动赋予**：同样不推荐,有安全风险

      chmod g+s [文件名]

      chmod 2755 [文件名]

   3. **Sticky BIT**

      也称黏着位

      目前仅针对目录有效

      当用户对该目录有**7权限**可以使用

      使用后：即使用户对目录有w权限

      **也不能删除其他用户创建的文件**

      (对root用户不能起作用)

      **只能删除自己建立的文件**

      **使用**：

      **chmod 1770 [目录名]**

      **chmod o+t [目录名]**

   4. 找出具有特殊文件权限的文件

      find -perm 4755

      find -perm 2755

      find -perm -4000

      find -perm -2000

      -perm : 根据权限筛选

4. **chattr权限**

   没有安全风险，且能限制root(相当于防范误删机制)

   1. 使用

      +:增加权限

      -:删除权限

      **i**：**对文件**，不允许删除、更名、添加数据、修改数据；

        **对目录**，只允许目录下修改文件数据，不允许在目录删除和建立文件

      **a**：**对文件**，只能增加数据

      ​	(不能用vi 只能用echo "" >> [文件名])

      ​	**对目录**，不允许删除文件

      **chattr +i [文件名]**

      **chattr +a [文件名]**

   2. 查看文件chattr权限

      **lsattr [文件名]** 查看文件

      **lsattr -d [文件名]** 查看目录	

      

   











