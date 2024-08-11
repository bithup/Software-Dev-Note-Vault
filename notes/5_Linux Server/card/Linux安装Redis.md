## CentOS Stream在线安装Redis

dnf install redis

redis-server --version

systemctl start redis.service

systemctl enable redis

配置reids
/etc/redis/redis.conf