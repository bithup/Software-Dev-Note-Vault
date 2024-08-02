

# JDK安装

## 一、压缩包安装

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

   