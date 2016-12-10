#Problem & Fixed
##1. 系统磁盘空间剩余不足
查看磁盘情况：<code>df -h</code>, <code>du --max-depth=1 -h</code>

分析出由于 **/alidata/log/php/php-fpm.log** 文件占用过多空间，检查无用后<code>rm -f</code>删除

问题来了，df检查并没有释放空间，原因是因为php-fpm.log文件正在被使用，所以不能释放，需要重启php-fpm: 

    1. ps -ef | grep php-fpm
    2. kill -USR2 PID

