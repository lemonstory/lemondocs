#Problem & Fixed
##1. 系统磁盘空间剩余不足
查看磁盘情况：<code>df -h</code>, <code>du --max-depth=1 -h</code>

分析出由于 **/alidata/log/php/php-fpm.log** 文件占用过多空间，检查无用后<code>rm -f</code>删除

问题来了，df检查并没有释放空间，原因是因为php-fpm.log文件正在被使用，所以不能释放，需要重启php-fpm: 

    1. ps -ef | grep php-fpm
    2. kill -USR2 PID

##2. 安装php5.6
wget http://cn2.php.net/get/php-5.6.29.tar.gz/from/this/mirror
tar zxvf mirror
cd php-5.6.29/
./configure  --prefix=/alidata/server/php5.6 --with-config-file-path=/alidata/server/php5.6/etc --with-mysql=mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --enable-fpm --enable-fastcgi --enable-static --enable-inline-optimization --enable-sockets --enable-wddx --enable-zip --enable-calendar --enable-bcmath --enable-soap --with-zlib --with-iconv --with-gd --with-xmlrpc --enable-mbstring --without-sqlite --with-curl --enable-ftp --with-mcrypt --with-freetype-dir=/usr/local/freetype.2.1.10 --with-jpeg-dir=/usr/local/jpeg.6 --with-png-dir=/usr/local/libpng.1.2.50 --disable-ipv6 --disable-debug --with-openssl --disable-maintainer-zts --disable-safe-mode --disable-fileinfo
vim Makefile #编辑
make
make install
cp php.ini-production /alidata/server/php5.6/etc/php.ini

wget https://github.com/phpredis/phpredis/archive/2.2.8.tar.gz
tar zxvf 2.2.8.tar.gz 
cd phpredis-2.2.8/
/alidata/server/php5.6/bin/phpize 
./configure --with-php-config=/alidata/server/php5.6/bin/php-config 
make
make install


wget http://pecl.php.net/get/yac-0.9.2.tgz
tar zxvf yac-0.9.2.tgz 
cd yac-0.9.2
ls
/alidata/server/php5.6/bin/phpize 
./configure --with-php-config=/alidata/server/php5.6/bin/php-config 
make
make install

cd /alidata/server/php5.6/
cd bin/
./pecl install memcached

修改php.ini

## DEBUG
利用Facebook的php优化工具xhprof，对代码进行优化，只需要在接口URL后面加上参数debug=true就可以参看接口运行情况

## 接口优化
| 接口说明   | API  | 原来的时间    | 现在的时间    | 是否优化  | 是否上线 |
| :--------:   | :-----:  | :----:  | :-----:  | :----:  | :----:  |
| 首页|	/default/v2.6.4/index.php|	177ms|	15ms|	是|	是
| 专辑详情页 |	/album/v2.6/info.php	|130ms	|56ms	|是	|是|
| 标签专辑列表 |	/tag/gettagalbumlist.php	|20ms	|20ms	|无	|-|
| 全部分类 |	/default/v2.6/category.php	|13ms	|6ms	|是	|NO|
| 最新上架 |	default/v2.6/online_list.php	|31ms	|6ms	|是	|NO|
| 我的故事 |					
| 学霸排行榜 |	/userinfo/ranklistenuserlist.php	|26ms	|26ms	|无	|-|