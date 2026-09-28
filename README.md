# 部署数据库mysql

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

####修改mysql的配置文件



















