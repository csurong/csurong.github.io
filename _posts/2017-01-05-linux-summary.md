---
layout: post
title:  "Linux 常用命令总结"
date: 2017-01-05 08:18:57 +0800
categories: Linux
tags:  linux
author: csurong
---

* content
{:toc}

网上找了不少 Linux 方面的资料，令我想不通的是，这么简单的东西，为什么可以写的这么复杂。。我本地装了一个 Ubuntu 虚拟机，云上装了一个 CentOS，用着用着就熟练了




## Linux 的命令格式

`[root@localhost~]#`
+ root: 当前登录用户
+ localhost: 主机名
+ ~ : 当前所在目录
+ # : 超级用户的提示符
+ $ : 普通用户的提示符

+ / 开头的是绝对路径
+ . 是当前目录,统计目录可以省略 ./
+ .. 当前目录的父目录
+ cd 更改当前目录到家目录
+ cd - 更改当前目录到先前的目录
+ 每个用户都有一个家目录,默认在 /home/用户名
+ root 用户的家目录是 /root

## 文件命令

### 常规

+ 命令格式 [选项] [参数]
+ tap 自动补全命令
+ 打印当前工作目录: pwd
+ 查看文件类型 file
+ 查询目录中内容 ls [选项] [文件或目录]
	+ ls -l 显示所有文件
	+ ls -lh 人性化显示所有文件
	+ ls -a 显示所有文件 以.开头的都是隐藏文件
+ cd (change direcotry):
	+ - 更改工作目录到先前的工作目录
	+ . 当前目录,同级目录可以省略 ./
	+ .. 当前目录的父目录
	+ ../.. 父目录的父目录
	+ / 开头为绝对路径, . 开头为相对路径

+ cp (copy):
	+ cp a.txt b.txt
	+ 复制 a.txt 并把新文件命名为 b.txt
	+ 复制目录要加上 -r 参数 cp -r a b

+ mkdir (创建一个目录)
	+ -p 可以一次性创建多层目录
	+ mkdir -p a/b/c

+ rmdir (remove empty directories)
	删除一个空目录

+ rm
	+ 这个命令直接删除东西,很危险,一般不要用
	+ 删除文件或者目录
	+ -f 强制删除
	+ -r 用来删除目录

+ mv (用来移动文件\文件夹 或者改名)
	+ mv a.txt b.txt 此为改名
	+ mv b.txt ../
	+ mv b.txt ../gua.txt
	+ 可以用 mv xx /tmp 的方式来将文件放入临时文件夹(/tmp 是操作系统提供的临时文件夹,重启会删除里面的所有文件,可以替代 rm 删除命令)
+ cat 显示文件内容
+ tac 反过来显示文件内容
+ nl 显示内容并附带行号
+ more(分屏分批看文件内容)
+ less(比 more 好用,可以前后退看文件)
+ head,tail (显示文件的前,后10行)
+ head 和 tail 有一个 -n 参数 head-n 20 a.txt 显示20行
+ touch a.txt 如果文件存在就更新修改时间,不存在就创建文件

### 文件解压缩

+ `tar -cf name.tar name` 压缩
+ `tar -tf name.tar` 显示压缩的文件 `tar -tvf name.tar` 显示详细信息（都是只显示，不解压。v表示显示详细信息）verbose(详细)
+ `tar -xf name.tar` 解压（抽取）

+ `tar -czvf name.tar.gz name` (加了一个 z，以gz格式压缩)
+ `tar -tzvf name.tar.gz` (加了一个 z，以gz格式查看)
+ `tar -xzvf name.tar.gz` (加了一个 z，以gz格式抽取)

+ `tar -cvf|tvf|xvf	tar -czvf|tzvf|xzvf`         记住这一行就行了
+ compression(create)|list|extract(提取)

### 文件编辑（vim）

+ touch 可以新建文件，vim 也可以直接新建文件
+ 打开后，写入内容，按 i 键（insert）
+ 退出 按 esc ：wq 保存退出
+ 移到第一行 gg；移到最后一行 G
+ 删除一整行 dd；恢复 u。前提是从 insert 模式中切换出来
+ 复制一整行 yy；粘贴 p
+ 文件的权限 r-w-x 4-2-1 读-写-可执行

## 权限操作

+ sudo 用管理员账户执行程序(安装程序或修改一些系统配置都需要管理员权限)
+ su (switch user) 切换用户 su root
+ ll 全部显示

## 信息查找

+ file 显示文件类型
+ uname 显示操作系统名字或者其它信息 -r(内核) -a(全部)
+ whoami 查看当前身份
+ find . -name "\*.py" 查找所有以 .py 结尾的文件

## 符号

+ ~ 家目录快捷方式
+ \> 覆盖式重定向
+ \>> 追加重定向

## 网络

+ ifconfig(查看 ip 一般使用这个)
+ ip addr（也可查看 ip）

## SSH

+ ssh，安全外壳协议，建立在应用层，专为远程登录会话和其它网络服务提供安全性的协议
+ 以下内容在 CentOs 中操作，Ubuntu 中相关文件位置自行 Google

### 服务器安装 SSH 服务

+ 安装 `yum install openssh-server`
+ 启动 `service sshd start`
+ 设置开机运行 `chkconfig sshd on`
+ 服务器版本的操作系统一般都已经装好。桌面版的可能没有

### 客户端安装 SSH 工具

+ windows下很多工具支持，Xshell，Putty，secureCRT
+ Linux平台安装 `yum install openssh-clients`
+ 客户端连接SSH服务 `ssh root@公网ip` 实际与新建会话是一样的+ 
+ exit 回到本地

### SSH config（配置）

+ 以下讲的都是linux客户端的config），客户端的config才能对自己生效
+ config 方便管理员批量管理多个 ssh
+ config 存放在 ~/.ssh/config(.ssh目录下没有的话就`touch config`新建一个)
+ SSH config 语法关键字：
	+ Host 别名
	+ HostName 主机名
	+ Port 端口（ssh服务的默认端口为22）
	+ User 用户名
	+ IdentityFile 密钥文件的路径

	+ 配置了config之后，连接服务器直接 `ssh 别名`
```
host "tencent"
    HostName xxx.xx.x.xx
    User root
    Port 22

```

### SSH安全免密码登录：ssh  key

+ 普通登录：`ssh username@xxx.xx.x.xx`
+ ssh key 使用非对称加密方式生成公钥和私钥
+ 私钥存放在本地 `~/.ssh` 目录
+ 公钥可以对外公开，放在服务器的 `~/.ssh/authorized_keys` 
+ Linux 平台生成 ssh key:
	+ 客户端cd ~/.ssh/
	+ 客户端使用 ssh-keygen -t rsa 命令亦可 ssh-keygen -t dsa
	+ 服务端在 authorized_keys 文件中写入客户端生成的公钥
	+ 客户端将密钥加载到ssh服务中：`ssh-add ~/.ssh/私钥文件`
+ windows 平台生成 ssh key:
	+ 直接在 xshell 工具栏中生成
	+ 在 ~/.ssh 目录下建立 authorized_keys 文件，编辑放入公钥
+ 如果是新用户，则还要修改一下权限
```
cd /home/username/
chmod 700 .ssh
cd /home/username/.ssh
chmod 600 authorized_keys
```

### SSH安全端口

+ 避免服务器的远程连接端口被别人知道
+ 改变 SSH 服务端口：修改 `/etc/ssh/sshd_config`(里面也可以添加连接的端口，这是对服务器端的操作)

## 软硬件的安装、查看

### 软件操作（CentOs）

+ 软件包管理器： yum
+ 安装软件： `yum intall xxx`
+ 卸载软件： `yum remove xxx`
+ 搜索软件： `yum serach xxx`
+ 清理缓存： `yum clean packages`
+ 列出已安装： `yum list`
+ 软件包信息： `yum info xxx`

### 软件操作（Ubuntu）

+ `sudo apt-get install name` `sudo apt install`
+ 备份Ubuntu默认源地址 `sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup`
+ 更新源服务器列表，即 `/etc/apt/sources.list`
+ `sudo apt-get update` sudo apt install 
+ `sudo apt-get remove name` 删除包
+ `sudo apt-cache search package` 搜索软件包
+ `sudo apt-cache show package`  获取包的相关信息，如说明、大小、版本等

### 硬件操作

+ 内存： `free -m`
+ 硬盘：`df -h`
+ 负载： `w/top`
+ cpu： `cat /proc/cpuinfo`

## 其它

### kill

+ `sudo netstat -tnlp` 查看所有进程端口占用情况
+ `sudo kill xxpidxx` 杀死进程

### 防火墙

+ `yum install firewalld`				安装
+ `service firewalld start`			启动
+ `service firewalld status`			检查状态
+ `service firewalld stop/disable` 	关闭/禁用防火墙

+ 可能已默认安装，用 `yum list |grep firewall` 检查

+ `firewall-cmd --version` 	查看版本
+ `firewall-cmd --help`		查看帮助文档
+ `firewall-cmd --state`		查看运行状态

> 在防火墙有区域和端口的区分

+ `firewall-cmd --get-zones` 查看区域
+ `firewall-cmd --get-default-zone` 默认区域
+ `firewall-cmd --list-all-zone` 列出每一个区域的配置信息
+ `firewall-cmd --query-service=ssh` 查询服务
+ `firewall-cmd --remove-serivce=ssh` 删除服务
+ `firewall-cmd --add-service=ssh` 添加服务
+ `firewall-cmd --list-services` 列出所有服务
+ `firewall-cmd --query-port=22/tcp` 查询端口是否开启
+ `firewall-cmd --add-port=22/tcp` 开启端口
+ `firewall-cmd --remove-port=22/tcp` 关闭端口
+ `firewall-cmd --list-ports` 查看所有打开的端口

+ 端口和服务的概念
+ 删除服务后开启端口依然可以（已实测）
+ 安装了服务都会开启默认端口，不用特意操作。测试时关闭防火墙

### 提权 、上传、下载

+ 提权：sudo
+ root 下通过 visudo 命令将用户加入到文件中
```
## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL
%rong ALL=(ALL)       ALL
```
+ 一般不用 root 进行操作。都是将用户加入文件，用用户操作，特殊命令用 sudo 提权
+ 文件下载
	+ wget `wget http://www.baidu.com`
	+ curl `curl -o filename http://www.baidu.com` 将下载下来的文件命名为 filename
+ 文件上传
	+ scp上传文件格式为 scp 文件名 用户@公网地址：路径 例：`scp a.txt rong@192.168.x.xxx:/tmp/` (scp是 secure copy的缩写, scp是linux系统下基于ssh登陆进行安全的远程文件拷贝命令)
	+ 下载 `scp rong@192.168.x.xxx:/tmp/a.txt ./` 格式为：scp 用户名@公网地址：服务器上文件地址 文件要保存的本机地址
	> 以上是 linux 下的操作，windows 下见下
	+ 服务器上安装软件 yum install lrzsz(在linux里可代替ftp上传和下载)
	+ 服务器端 `rz` 上传命令(服务器端 receive)
	+ 服务器端 `sz filename` 从服务器端下载文件（服务器端 send）
	+ ZMODEM 协议

