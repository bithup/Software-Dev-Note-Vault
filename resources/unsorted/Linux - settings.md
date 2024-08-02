

# Linux软件安装与系统配置



## 网络配置

### CentOS不显示ipv4地址

```shell
vi /etc/sysconfig/network-scripts/ifcfg-ens33
// 修改ONBOOT=no为ONBOOT=yes
// 重启网络服务
systemctl restart network
```



## 防火墙配置

### firewall-cmd

```shell
# 查看端口是否开放
firewall-cmd --zone=public --query-port=80/tcp
# 永久添加开放端口
firewall-cmd --zone=public --add-port=80/tcp --permanent 
# 重新载入
firewall-cmd --reload
# 永久删除开放端口
firewall-cmd --zone=public --remove-port=80/tcp --permanent
```

### iptable



## 开机启动项配置

```shell
# 查看开机启动项的状态，enabled是开机启动，disabled是开机不启动
systemctl list-unit-files
systemctl list-unit-files | grep enabled
systemctl list-unit-files | grep mysql
# 设置开机启动项
systemctl enable mysqld
systemctl disable mysqld
```

## CentOS配置 yum仓库

### 配置阿里云yum源

可以不用单独配置阿里云yum源，yum命令会自动识别最快的源

```shell
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo.bak
wget -O CentOs-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
#yum源更新命令
yum clean all
yum makecache
yum update
```

## CentOS7部署Nginx

### yum安装

### 启动：

```shell
systemctl start nginx
# 查看版本
nginx -v
# 访问测试
curl -i localhost
```

### 配置

- 配置文件路径：/etc/nginx/nginx.conf 和 /etc/nginx/conf.d/default.conf
- 默认root目录 ：/usr/share/nginx/html



## CentOS7部署MySQL80

### 安装yum源

```shell
# 访问https://dev.mysql.com/downloads/repo/yum/，下载对应版本的yum源RPM包链接
wget https://dev.mysql.com/get/mysql80-community-release-el7-4.noarch.rpm
# 安装yun源
yum -y install mysql80-community-release-el7-4.noarch.rpm
```

### 安装MySQL

```shell
# 安装MySQL
yum -y install mysql-community-server
# 启动
systemctl start mysqld
# 查看状态
systemctl status mysqld
# 查看初始密码
grep "password" /var/log/mysqld.log
# 登录MySQL并修改密码
mysql> alter user 'root'@'localhost' identified by 'Mysql-80@vm';
```

### 权限配置

```shell
# 使用mysql库
mysql> use mysql;
# 创建用户，默认允许远程访问
mysql> create user 'admin' identified by 'Mysql-80@vm';
# 查询用户
mysql> select user, host, authentication_string from user where user='admin';
# 修改密码
mysql> update user set authentication_string='123456' where user='admin';
# 删除用户
mysql> drop user 'admin';
# 查询用户权限
mysql> show grants for 'admin';
# 授予权限
mysql> grant all privileges on databasename.tablename to 'user'@'host' identified by 'password'；
# 授予admin用户全局级全部权限：
mysql> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' IDENTIFIED BY 'Mysql-80@vm' WITH GRANT OPTION;
# 授予admin用户针对testdb数据库全部权限：
mysql> GRANT ALL PRIVILEGES ON testdb.* TO 'admin'
# 生效(刷新权限)
mysql> FLUSH PRIVILEGES;
```

### mysql8配置文件



## CentOS7部署PostgreSQL

## CentOS7部署Redis

## CentOS7部署MongoDB

## CentOS7部署RabbitMQ

## CentOS7部署ElasticSearch

## JDK安装配置

### 一、压缩包安装

1. 查看系统是否有Java环境 

   ```shell
   java -version
   echo $JAVA_HOME
   ```

   

2. 下载压缩包jdk1.8.0_65.tar.gz

3. 解压、重命名、移动目录

   ```shell
   tar -zxvf jdk1.8.0_65.tar.gz
   mv jdk1.8.0_65/ jdk/
   mv jdk/ /usr/local/
   ```

4. 配置环境变量

   ```shell
   #centos修改/etc/profile , ubuntu修改~/.bashrc , 在最后添加如下内容
   
   JAVA_HOME=/usr/local/jdk
   PATH=$JAVA_HOME/bin:$PATH
   CLASSPATH=.:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar
   export PATH JAVA_HOME CLASSPATH
   ```

5. 使配置生效

   ```shell
   source /etc/profile
   ```

## CentOS7离线安装MySQL57

### RPM包安装

1. 卸载CentOS自带mariadb

   ```shell
   rpm -qa | grep mariadb           # 查看
   rpm -qa | grep mysql
   rpm -e --nodeps mariadb-libs-... # 卸载
   ```

   

2. 下载并解压rpm-bundle.tar
   mysql-el7-5.7.29: *https://dev.mysql.com/downloads/file/?id=491956*

3. 按顺序安装RPM包

   ```shell
   rpm -ivh mysql-community-common-5.7.*.rpm
   rpm -ivh mysql-community-libs-5.7.*.rpm
   rpm -ivh mysql-community-client-5.7.*.rpm
   rpm -ivh mysql-community-server-5.7.*.rpm
   ```

4. 查询、启动mysql服务

   ```shell
   service mysqld status     # 查看
   service mysqld start      # 启动
   service mysqld restart    # 重启
   ```

5. 查看临时密码、登录并修改密码

   ```shell
   grep password /var/log/mysqld.log
   mysql -u root -p
   ```

   ```mysql
   # 设置复杂密码
   mysql> set password = password("Abc@1234#"); 
   # 或者修改密码策略，设置简单密码
   mysql> set global validate_password_policy=0;
   mysql> set global validate_password_length=4;
   mysql> set password = password("root");
   mysql> flush privileges;
   mysql> exit # 退出重新登录
   
   ```

6. 允许远程连接数据库

   ```mysql
   mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@ '%' IDENTIFIED BY '登陆密码' WITH GRANT OPTION;
   ```

7. 配置文件 /etc/my.cnf

   ```txt
   [mysqld]
   lower_case_table_names=1   #1:不区分大小写，0:区分
   character_set_server=utf8  #编码格式
   max_connections=1024       #最大连接数
   ```

### 压缩包安装

1. 下载解压

   ```shell
   tar -zxvf mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz 
   ```

2. 重命名、移动

   ```shell
   mv mysql-5.7.27-linux-glibc2.12-x86_64/ mysql/
   mv mysql/ /usr/local/
   ```

3. 卸载CentOS自带mariadb或mysql

   ```
   rpm -qa | grep mysql
   ```

4. 安装

   ```shell
   cd /usr/local/mysql/bin
   mysqld --initialize --user=root --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql
   ```

5. 配置my.cnf

   ```shell
   vi /etc/my.cnf
   # [mysqld]
   # default-character-set = utf8
   # character-set-server = utf8
   # port=3306
   # datadir=/usr/local/mysql/data
   # socket=/var/lib/mysql/mysql.sock
   # lower_case_table_names=1
   ```
