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

5. 启动服务
```
./restart.sh db_proxy_server
./restart.sh login_server
./restart.sh route_server
./restart.sh msg_server
./restart.sh websocker_server
```

6. 配置nginx
```
user www www;
worker_processes auto;

error_log  /home/wwwlogs/nginx_error.log  crit;
pid        /usr/local/nginx/logs/nginx.pid;

worker_rlimit_nofile 51200;
events
    {
        use epoll;
        worker_connections 51200;
        multi_accept on;
    }
http
    {
        include       mime.types;
        default_type  application/octet-stream;
        server_names_hash_bucket_size 128;
        client_header_buffer_size 32k;
        large_client_header_buffers 4 32k;
        client_max_body_size 50m;
        sendfile on;
        tcp_nopush     on;
        keepalive_timeout 60;
        tcp_nodelay on;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
        fastcgi_buffer_size 64k;
        fastcgi_buffers 4 64k;
        fastcgi_busy_buffers_size 128k;
        fastcgi_temp_file_write_size 256k;
        gzip on;
        gzip_min_length  1k;
        gzip_buffers     4 16k;
        gzip_http_version 1.0;
        gzip_comp_level 2;
        gzip_types       text/plain application/x-javascript text/css application/xml;
        gzip_vary on;
        gzip_proxied        expired no-cache no-store private auth;
        gzip_disable        "MSIE [1-6]\.";
        server_tokens off;
        log_format  access  '$remote_addr - $remote_user [$time_local] "$request" '
        '$status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" $http_x_forwarded_for';

	server
    	{
            listen       80;
            server_name _;
            index index.html index.htm index.php default.html default.htm default.php;
            root        /www/wwwroot/default;

            location ~ \.php($|/) {
                fastcgi_pass   unix:/tmp/php-cgi.sock;
                fastcgi_index  index.php;
                fastcgi_split_path_info ^(.+\.php)(.*)$;
                fastcgi_param   PATH_INFO $fastcgi_path_info;
                fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
                include        fastcgi_params;
            }

            location ~ /im {
                if (!-e $request_filename){
                 rewrite ^(.*)$ /$1.html last;
                 break;
                }
            }
            location  / {
                if (!-e $request_filename) {
                 rewrite ^/(.*)$ /index.php/$1 last;
                 break;
                }
            }
      }
}
```
7. 静态文件放到对应的目录, 访问 127.0.0.1


