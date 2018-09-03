# TeamTalk
	TeamTalk is a solution for enterprise IM
	
forked from xiaominfc/TeamTalk

### 使用centos7部署的完整流程:

1. 安装所需依赖: lnmp, redis
```
sudo yum -y remove httpd* php* mysql-server mysql mysql-libs php-mysql
sudo yum -y --skip-broken install wget vim git texinfo patch make cmake gcc gcc-c++ gcc-g77 flex bison file libtool libtool-libs autoconf kernel-devel libjpeg libjpeg-devel libpng libpng-devel libpng10 libpng10-devel gd gd-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glib2 glib2-devel bzip2 bzip2-devel libevent libevent-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel vim-minimal nano fonts-chinese gettext gettext-devel ncurses-devel gmp-devel pspell-devel unzip libcap diffutils
wget http://soft.vpser.net/lnmp/lnmp1.5.tar.gz -cO lnmp1.5.tar.gz && tar zxf lnmp1.5.tar.gz && cd lnmp1.5 && sudo ./install.sh lnmp
cd /etc/yum.repos.d/ && wget http://mirrors.aliyun.com/repo/epel-6.repo && sudo yum install -y redis && sudo service redis start
```
2. 获取项目
```
git clone https://github.com/Liuthx/TeamTalk.git
```
3. 编译
```
cd TeamTalk/server/src
./make_hiredis.sh
./make_log4cxx.sh
./make_protobuf.sh
./build.sh version test
```
4. 处理数据库
```
TeamTalk/auto_setup/mariadb/conf
mysql_secure_installation
mysql -uroot -p
source ttopen.sql
quit
```


