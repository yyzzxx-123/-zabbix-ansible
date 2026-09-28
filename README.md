#部署数据库mysql

#######检查#######

yum repolist #查看yum仓库，若yum仓库repolist:0 则需要更换yum仓库操作如下：

mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak   #备份旧源

vi /etc/yum.repos.d/CentOS-Base.repo    #新建阿里源文件

#输入以下内容

[base]
name=CentOS-$releasever - Base - mirrors.aliyun.com
baseurl=https://mirrors.aliyun.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

[updates]
name=CentOS-$releasever - Updates - mirrors.aliyun.com
baseurl=https://mirrors.aliyun.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-$releasever - Extras - mirrors.aliyun.com
baseurl=https://mirrors.aliyun.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7


############下载并安装mysql数据库#############

rpm -qa|grep -i mysql   #检查是否已经安装数据库 没有输出则没有安装

rpm -qa|grep -i glibc   #查看glibc版本

https://downloads.mysql.com/archives/community/   #官网安装数据库

#####通过final shell 上传压缩包可能会遇到上传失败的问题，这时候只需要进入ssh连接中的用户名改为root，并且退出当前用户，再次进入即可############

tar -xJvf mysql-8.4.9-linux-glibc2.17-x86_64.tar.xz    #解压压缩包

mv tar -xJvf mysql-8.4.9-linux-glibc2.17-x86_64 /usr/local/mysql8.4.9   #mysql一般安装在 /usr/local/这个位置，将其移到目标地址

#########以下创建mysql专用用户和用户组，把mysql开在一个新建的用户上，能够保证他对系统绝大部分的位置，是不可写的。使mysql即使被攻击，它也可以限制整个系统的影响！############

chown -R mysql /usr/local/mysql8.4.9   #把mysql8.4.9所有者改成mysql用户

chgrp -R mysql /usr/local/mysql8.4.9   #把组属性改成mysql

mkdir -p /data/mysql | chown -R mysql:mysql /data/mysql     #在根目录下创建mysql数据库存放数据的目录，并修改所有者和所属组为mysql

vi /etc/my.cnf                        #修改mysql的配置文件

#注释原来的配置，改成下方的配置

bind-address=0.0.0.0
port=3306          #端口
user=mysql         #系统用户
basedir=/usr/local/mysql8.4.9   #安装目录
datadir=/data/mysql             #数据目录（数据库、表、数据文件等存放目录）
socket=/tmp/mysql.sock          #本机连接mysql的时候优先走这个文件、不走网络端口、*本地连接报错，经常就是sock文件找不到
log-error=/data/mysql/mysql.err    #错误日志路径
pid-file=/data/mysql/mysql.pid     #进程id文件
#character config          
character_set_server=utf8mb4       #数据库默认字符集
symbolic-links=0                   #关闭符号链接（软链接）
explicit_defaults_for_timestamp=true    #针对timestamp字段的兼容开关

./bin/mysqld --defaults-file=/etc/my.cnf --basedir=/usr/local/mysql8.4.9 --datadir=/data/mysql --user=mysql --initialize   #初始化

root@localhost: Nez/aFt8eKBq     #在MySQL.err 初始化之后产生的一个日志， Nez/aFt8eKBq 是初始化密码

cp /usr/local/mysql8.4.9/support-files/mysql.server /etc/init.d/mysqld   #复制mysql.server到/etc/init.d这个文件下，并且重命名为mysqld，创建mysql服务，使其能够开机自启

chkconfig --add mysqld       #将mysqld加入到开机自启
chkconfig --list mysqld      #检查是否添加成功
service mysql start  #启动服务
service mysql status   #查看是否启动成功


vi /etc/profile  #配置mysql环境（不仅可以配置mysql环境，也可以配置其他环节，如java）

export PATH=$PATH:/usr/local/mysql8.4.9/bin:/usr/local/mysql8.4.9/lib    #最后一行添加   

source /etc/profile     #重新加载配置 
env   #查看变量

mysql -uroot -p        #登录mysql  提示输入密码，用初始化给的密码。

ALTER USER "root"@"localhost" IDENTIFIED BY "yzx123456";  #第一次登录需要修改密码

SHCW DATABASES;    #授权远程登陆

USE mysql;        #先切换mysql的库

SHOW TABLES;      #授权远程用户连接

update user set host="%" where user = "root";    #允许所有的ip访问

flush privileges;      #刷新权限

#########连接创建好的数据库时，需要注意主机能否ping通虚拟机，是否已经关闭防火墙！###########

如果不想关闭防火墙的话，也可以让防火墙对目标端口开放！

例如：目标端口是3306

firewall-cmd --zone=public --add-port=3306/tcp --primanent    #对3306端口永久开放


#######查看防火墙状态##############

若遇到无法开启情况
先用：systemctl unmask firewalld service
然后：systemctl start firewalld service

#######查看对外开放端口状态##########


#######d对外开发端口########

查看想开的端口是否已开：firewalld-cmd --query-port=123/tcp   #123为任意端口号

添加指定需要开放的端口：firewalld-cmd --query-port=123/tcp --permanent




#######



















