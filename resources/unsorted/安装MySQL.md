# MySQL安装

## 一、CentO S 7.x 安装MySQL v5.7.x

### >>> RPM包安装

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

### >>> 解压包安装

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

   

   

## 二、MySQL配置与管理

