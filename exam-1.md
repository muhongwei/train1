#Linux/centos和ubuntu安装网络配置和ssh服务开启
Centos7安装，网络配置和ssh服务开启：
Centos安装
新建虚拟机

选择自定义类型配置：

导入安装程序映像文件路径，选择稍后安装操作系统：

确定安装操作系统类型和版本

之后一直选择默认选项，完成安装。

在虚拟机设备设置中将映像文件导入：

开启虚拟机

选择install centos7:

选择英语：

进行软件选择项设置，选择第一个Minimal install，不需要图像界面：
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/1.png)

选择磁盘分区自动分区：

设置root password为root

等待安装完成，点击右下角reboot重启：

Centos7网络配置和ssh服务开启：
登陆之后通过命令行 cd /etc/sysconfig/network-scripts/
vi ifcfg-ens33对配置文件进行设置
将ONBOOT=no 改为yes
BOOTPROTO=static
IPADDR=192.168.0.111
GATEWAY=192.168.0.1
DNS1=180.76.76.76

![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/2.png)

Esc :wq退出。
使用命令行service network restart进行网络重启


Centos7 ssh服务配置：
执行vi /etc/ssh/sshd_config

将port22 
  ListenAddress 
  permitRootlogin yes 
  passwordAuthentication yes前面#去掉，退出编辑
使用命令行service sshd start对sshd服务进行开启
检查sshd是否开启ps –e | grep sshd返回sshd表示开启：


打开xshell:输入虚拟机id: 192.168.0.111

输入用户名;root

输入密码：root

连接成功.

ubuntu安装，网络配置和ssh服务开启：
Ubuntu安装
选择自定义类型配置：

导入安装程序映像文件路径，选择稍后安装操作系统：

确定安装操作系统类型和版本：

之后一直选择默认选项，完成安装。
在虚拟机设备设置中将映像文件导入：

开启虚拟机
语言设置选择英文：

先择第一项安装：install ubuntu servier:
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/3.png)

Installation process语言设置选择英文：

设置地址选其他

选asia 

选hongkong
 
之后一直默认进行安装。
设置用户名mhw

确认使用weak password

之后选择默认选择：进行安装

硬盘分区选择第二个选项：guided-use entire and set up lvm

![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/4.png)

选择默认继续安装，不设置其他

默认默认，一直enter安装完成。进入如下ubuntu命令行界面输入用户名密码登录。



ubuntu配置网络和ssh服务：

输入命令sudo vi /etc/network/interfaces
输入：修改如下
iface ens33 inet static
address 192.168.0.112
netmask 255.255.0.0
gateway 192.168.0.1
Ctrl+x保存退出
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/5.png)

 
设置域名服务器
输入命令sudo vi /etc/resolvconf/resolv.conf.d/base
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/6.png)


开启ssh服务：
输入"sudo apt-get update"

输入"sudo apt-get install openssh-server"-->回车-->输入"y"-->回车-->安装完成。

输入"sudo ps -e |grep ssh"-->回车-->有sshd,说明ssh服务已经启动，如果没有启动，输入"sudo service ssh start"-->回车-->ssh服务就会启动


打开xshell：
输入网址192.168.0.112

输入用户名：mhw

输入密码：201314

连接成功



