
# Linux/centos和ubuntu安装网络配置和ssh服务开启


## Centos7安装，网络配置和ssh服务开启


### Centos安装


* 新建虚拟机
* 选择自定义类型配置
* 导入安装程序映像文件路径，选择稍后安装操作系统
* 确定安装操作系统类型和版本
* 之后一直选择默认选项，完成安装
* 在虚拟机设备设置中将映像文件导入
* 开启虚拟机
* 选择install centos7:
* 选择英语：
* 进行软件选择项设置，选择第一个Minimal install，不需要图像界面<br>
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/1.png)
* 选择磁盘分区自动分区：
* 设置root password为root
* 等待安装完成，点击右下角reboot重启：
### 配置网络基础
#### 桥接与NAT的区别：
* bridged networking（桥接模式）<br>
在这种模式下，VMWare虚拟出来的操作系统就像是局域网中的一台独立的主机，它可以访问网内任何一台机器。在桥接模式下，你需要手工为虚拟系统配置IP地址、子网掩码，而且还要和宿主机器处于同一网段，这样虚拟系统才能和宿主机器进行通信。同时，配置好网关和DNS的地址后，以实现通过局域网的网关或路由器访问互联网。此时ip要与主机在同一个段，netmask和gateway与主机相同。
* network address translation(NAT模式) <br>
使用NAT模式，就是让虚拟系统借助NAT(网络地址转换)功能，通过宿主机器所在的网络来访问公网。也就是说，使用NAT模式可以实现在虚拟系统里访问互联网。NAT模式下的虚拟系统的TCP/IP配置信息是由VMnet8(NAT)虚拟网络的DHCP服务器提供的，无法进行手工修改，因此虚拟系统也就无法和本局域网中的其他真实主机进行通讯。采用NAT模式最大的优势是虚拟系统接入互联网非常简单，只需要宿主机器能访问互联网，你不需要配置IP地址，子网掩码，网关，但是DNS地址还是要根据实际情况填的。添加DNS地址除了在网卡属性中填写，还可以在虚拟机中的“虚拟网络编辑器”中的NAT选项卡中点击“编辑”按钮中来添加。此时动态生成的ip与主机vmnet8 ip在同一个段。

### Centos7静态网络配置
* 登陆之后通过命令行 cd /etc/sysconfig/network-scripts/
* vi ifcfg-ens33对配置文件进行设置
* 将ONBOOT=no 改为yes
* BOOTPROTO=static
* IPADDR=192.168.0.111
* GATEWAY=192.168.0.1
* DNS1=180.76.76.76<br>
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/2.png)
* Esc :wq退出。
* 使用命令行service network restart进行网络重启
* 检查配置是否成功
```
[root@localhost ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:0c:29:73:f8:60 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.111/16 brd 192.168.255.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::7cf6:8894:e1c:7c34/64 scope link
       valid_lft forever preferred_lft forever
[root@localhost ~]# ping www.baidu.com
PING www.a.shifen.com (180.97.33.107) 56(84) bytes of data.
64 bytes from 180.97.33.107 (180.97.33.107): icmp_seq=1 ttl=55 time=29.9 ms
64 bytes from 180.97.33.107 (180.97.33.107): icmp_seq=2 ttl=55 time=29.8 ms
64 bytes from 180.97.33.107 (180.97.33.107): icmp_seq=3 ttl=55 time=28.5 ms
64 bytes from 180.97.33.107 (180.97.33.107): icmp_seq=4 ttl=55 time=28.6 ms
64 bytes from 180.97.33.107 (180.97.33.107): icmp_seq=5 ttl=55 time=28.1 ms
64 bytes from 180.97.33.107 (180.97.33.107): icmp_seq=6 ttl=55 time=28.8 ms
^C
--- www.a.shifen.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5010ms
rtt min/avg/max/mdev = 28.139/28.996/29.991/0.693 ms

```



### Centos7 ssh服务配置

* 执行vi /etc/ssh/sshd_config
* 将port22 
*   ListenAddress 
*  permitRootlogin yes   
*   passwordAuthentication yes前面#去掉，退出编辑  
* 使用命令行service sshd start对sshd服务进行开启
* 检查sshd是否开启ps –e | grep sshd返回sshd表示开启
* 打开xshell:输入虚拟机id: 192.168.0.111
* 输入用户名;root
* 输入密码：root
* 连接成功.
## ubuntu安装，网络配置和ssh服务开启
### Ubuntu安装
* 选择自定义类型配置：
* 导入安装程序映像文件路径，选择稍后安装操作系统：
* 确定安装操作系统类型和版本：
* 之后一直选择默认选项，完成安装。
* 在虚拟机设备设置中将映像文件导入：
* 开启虚拟机
* 语言设置选择英文
* 先择第一项安装：install ubuntu servier<br>
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/3.png)
* Installation process语言设置选择英文：
* 设置地址选其他
* 选asia 
* 选hongkong 
* 之后一直默认进行安装。
* 设置用户名mhw
* 确认使用weak password
* 之后选择默认选择：进行安装
* 硬盘分区选择第二个选项：guided-use entire and set up lvm<br>
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/4.png)
* 选择默认继续安装，不设置其他
* 默认默认，一直enter安装完成。进入如下ubuntu命令行界面输入用户名密码登录。

### ubuntu配置网络
* 输入命令sudo vi /etc/network/interfaces
* 输入：修改如下
* iface ens33 inet static
* address 192.168.0.112
* netmask 255.255.0.0
* gateway 192.168.0.1
* Ctrl+x保存退出<br>
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/5.png)

* 设置域名服务器
* 输入命令sudo vi /etc/resolvconf/resolv.conf.d/base<br>
![Image text]( https://raw.githubusercontent.com/muhongwei/train1/master/imgfloder/6.png)

### ubuntu开启ssh服务：
```
mhw@ubuntu:~$ sudo apt-get update
[sudo] password for mhw:
Hit:1 http://hk.archive.ubuntu.com/ubuntu xenial InRelease
Hit:2 http://hk.archive.ubuntu.com/ubuntu xenial-updates InRelease
Hit:3 http://hk.archive.ubuntu.com/ubuntu xenial-backports InRelease
Hit:4 http://security.ubuntu.com/ubuntu xenial-security InRelease
Reading package lists... Done
mhw@ubuntu:~$ sudo apt-get install openssh-server
Reading package lists... Done
Building dependency tree
Reading state information... Done
openssh-server is already the newest version (1:7.2p2-4ubuntu2.2).
0 upgraded, 0 newly installed, 0 to remove and 88 not upgraded.
mhw@ubuntu:~$ sudo ps -e | grep ssh
  1140 ?        00:00:00 sshd
```
* 打开xshell：
* 输入网址192.168.0.112
* 输入用户名：mhw
* 输入密码：201314
* 连接成功


