#!/bin/bash

GROUP_NAME=mysql  
USER_NAME=mysql
ERROR_EXIT=65

read -n1 -p "are you sure setup[y/n]?" answer  
 case $answer in   
 Y | y)  
       echo   
       echo "start setup....";;  
 N | n)  
       echo   
       echo "Cancel setup...."  
       exit 10 ;;  
   *)  
       echo   
       echo "error input parameter....."   
       exit 11 ;;  
 esac  
 
#check if user is root    

if [ $(id -u) != "0" ];then  
   echo "Error: You must be root to run this script!"  
   

   exit 1  
fi  

##安装编译工具及库文件
echo"开始安装编译工具及库文件"
for i in gcc gcc-c++ make autoconf libtool-ltdl-devel gd-devel freetype-devel libxml2-devel \
                   libjpeg-devel libpng-devel openssl-devel curl-devel bison patch unzip libmcrypt-devel \
                   libmhash-devel ncurses-devel sudo bzip2 flex libaio-devel wget
do    
         yum -y install $i   
done 

##安装cmake编译器
rm -rf /tmp/cmake-3.1.1.tar.gz
echo"开始下载cmake-3.1.1.tar.gz"

wget http://www.cmake.org/files/v3.1/cmake-3.1.1.tar.gz -P /tmp  
     if(( $? == 0 ))  
       then  
        echo "cmake DownLoad sucessfully!"   
       else  
        echo "cmake DownLoad failed!"  
        exit $ERROR_EXIT  
     fi  
cd /tmp  
tar xzvf cmake-3.1.1.tar.gz   
cd cmake-3.1.1  
./bootstrap  
make && make install



#downMySQL  
 rm -rf /tmp/mysql-5.6.15.tar.gz
 echo"开始下载mysql"
 wget http://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.15.tar.gz -P /tmp   
     if(( $? == 0 ))  
       then   
        echo "MySQL DownLoad sucessfully!"   
       else   
        echo "MySQL DownLoad failed!"  
        exit $ERROR_EXIT  
     fi  
  
  
cd /tmp  
tar xzvf mysql-5.6.15.tar.gz 
cd mysql-5.6.15

cmake \
   -DCMAKE_INSTALL_PREFIX=/usr/local/webserver/mysql/ \
   -DMYSQL_UNIX_ADDR=/tmp/mysql.sock \
   -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci \
   -DWITH_EXTRA_CHARSETS=all -DWITH_MYISAM_STORAGE_ENGINE=1 \
   -DWITH_INNOBASE_STORAGE_ENGINE=1 \
   -DWITH_MEMORY_STORAGE_ENGINE=1 \
   -DWITH_READLINE=1 \
   -DWITH_INNODB_MEMCACHED=1 \
   -DWITH_DEBUG=OFF -DWITH_ZLIB=bundled -DENABLED_LOCAL_INFILE=1 \
   -DENABLED_PROFILING=ON \
   -DMYSQL_MAINTAINER_MODE=OFF \
   -DMYSQL_DATADIR=/usr/local/webserver/mysql/data \
   -DMYSQL_TCP_PORT=3306
make && make install

#addGroup  
  
if [ -z $(cat /etc/group|awk -F: '{print $1}'| grep -w "$GROUP_NAME") ]  
then  
  groupadd  $GROUP_NAME  
    if(( $? == 0 ))  
      then  
         echo "group $GROUP_NAME add sucessfully!"  
    fi     
else  
  echo "$GROUP_NAME is exsits"  
fi   
  
  
#addUser  
  
if [ -z $(cat /etc/passwd|awk -F: '{print $1}'| grep -w "$USE_NAME") ]  
then  
  adduser -g $GROUP_NAME $USER_NAME  
     if (( $? == 0 ))  
       then  
       echo "user $USER_NAME add sucessfully!"  
     fi  
else  
  echo "$USER_NAME is exsits"  
fi   

#创建binlog和库的存储路径并赋予mysql用户权限

mkdir -p /usr/local/webserver/mysql/binlog /www/data_mysql
chown mysql.mysql /usr/local/webserver/mysql/binlog/ /www/data_mysql/

echo "[client]
port = 3306
socket = /tmp/mysql.sock
[mysqld]
replicate-ignore-db = mysql
replicate-ignore-db = test
replicate-ignore-db = information_schema
user = mysql
port = 3306
socket = /tmp/mysql.sock
basedir = /usr/local/webserver/mysql
datadir = /www/data_mysql
log-error = /usr/local/webserver/mysql/mysql_error.log
pid-file = /usr/local/webserver/mysql/mysql.pid
open_files_limit = 65535
back_log = 600
max_connections = 5000
max_connect_errors = 1000
table_open_cache = 1024
external-locking = FALSE
max_allowed_packet = 32M
sort_buffer_size = 1M
join_buffer_size = 1M
thread_cache_size = 600
#thread_concurrency = 8
query_cache_size = 128M
query_cache_limit = 2M
query_cache_min_res_unit = 2k
default-storage-engine = MyISAM
default-tmp-storage-engine=MYISAM
thread_stack = 192K
transaction_isolation = READ-COMMITTED
tmp_table_size = 128M
max_heap_table_size = 128M
log-slave-updates
log-bin = /usr/local/webserver/mysql/binlog/binlog
binlog-do-db=oa_fb
binlog-ignore-db=mysql
binlog_cache_size = 4M
binlog_format = MIXED
max_binlog_cache_size = 8M
max_binlog_size = 1G
relay-log-index = /usr/local/webserver/mysql/relaylog/relaylog
relay-log-info-file = /usr/local/webserver/mysql/relaylog/relaylog
relay-log = /usr/local/webserver/mysql/relaylog/relaylog
expire_logs_days = 10
key_buffer_size = 256M
read_buffer_size = 1M
read_rnd_buffer_size = 16M
bulk_insert_buffer_size = 64M
myisam_sort_buffer_size = 128M
myisam_max_sort_file_size = 10G
myisam_repair_threads = 1
myisam_recover
interactive_timeout = 120
wait_timeout = 120
skip-name-resolve
#master-connect-retry = 10
slave-skip-errors = 1032,1062,126,1114,1146,1048,1396
#master-host = 192.168.1.2
#master-user = username
#master-password = password
#master-port = 3306
server-id = 1
loose-innodb-trx=0 
loose-innodb-locks=0 
loose-innodb-lock-waits=0 
loose-innodb-cmp=0 
loose-innodb-cmp-per-index=0
loose-innodb-cmp-per-index-reset=0
loose-innodb-cmp-reset=0 
loose-innodb-cmpmem=0 
loose-innodb-cmpmem-reset=0 
loose-innodb-buffer-page=0 
loose-innodb-buffer-page-lru=0 
loose-innodb-buffer-pool-stats=0 
loose-innodb-metrics=0 
loose-innodb-ft-default-stopword=0 
loose-innodb-ft-inserted=0 
loose-innodb-ft-deleted=0 
loose-innodb-ft-being-deleted=0 
loose-innodb-ft-config=0 
loose-innodb-ft-index-cache=0 
loose-innodb-ft-index-table=0 
loose-innodb-sys-tables=0 
loose-innodb-sys-tablestats=0 
loose-innodb-sys-indexes=0 
loose-innodb-sys-columns=0 
loose-innodb-sys-fields=0 
loose-innodb-sys-foreign=0 
loose-innodb-sys-foreign-cols=0

slow_query_log_file=/usr/local/webserver/mysql/mysql_slow.log
long_query_time = 1
[mysqldump]
quick
max_allowed_packet = 32M" >/etc/my.cnf

#初始化数据库
/usr/local/webserver/mysql/scripts/mysql_install_db --defaults-file=/etc/my.cnf  --user=mysql --basedir=/usr/local/webserver/mysql --datadir=/www/data_mysql &

#创建开机启动脚本
cd /usr/local/webserver/mysql/
chown -R mysql:mysql ./
cp support-files/mysql.server /etc/rc.d/init.d/mysqld 
chkconfig --add mysqld 
chkconfig --level 35 mysqld on

#启动mysql服务器
service mysqld start


ln -s /usr/local/webserver/mysql/bin/* /usr/bin


#新建myapp数据库

  
mysql <<EOF 2> /dev/null  
create database myapp;  
EOF
  
[ $? -eq 0 ] && echo "The database Created myapp" || echo "The database myapp is already exist"


echo "set up successfully!enjoy it...."

exit 0
