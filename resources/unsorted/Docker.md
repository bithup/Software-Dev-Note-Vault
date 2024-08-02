# Docker

## 一、安装配置

### 安装

```shell
# 在线安装
yum install docker
# 离线安装，下载所需rpm包
rpm -ivh *.rpm --nodeps --force
# 启动、重启、停止、查看状态
systemctl start|restart|stop|status docker
```

## docker命令

### 查看docker相关信息

```shell
docker info
# 查看docker版本
docker version
```

### 镜像相关

```shell
# 拉去远程镜像
docker pull centos:latest
# 加载tar文件到本地镜像库
docker load -i [文件路径]
# 导出镜像到tar文件
docker save -o [目标文件路径] [镜像名:版本]
# 查看本地镜像
docker images -a
#删除镜像
docker rmi [镜像名]
```

### 容器相关

#### 创建并运行容器

```shell
docker run [容器运行参数] [镜像名] [容器内运行的命令和参数]
```

容器运行参数：

- -d														 后台运行
- -it 														以交互模式运行容器，为容器重新分配一个伪输入终端
- -p [宿主机端口|容器端口]  		     端口映射，可以传多个-p参数
- --name=[容器名]
- --privileged=[true|false]

#### 管理容器

```shell
# 查看容器
docker ps -a
# 启动、停止、重启、进入、删除容器
docker start|stop|restart|attach|rm [容器名|容器ID]
# 进入容器内，进入容器交互界面
docker exec -it [容器名] /bin/bash
# 推出容器
exit
```

### 容器文件管理

```shell
# 拷贝容器内文件到容器外，冒号表示容器内路径
docker cp mysql:/etc/my.cnf /etc/my.cnf
# 拷贝容器外文件到容器内
docker cp /etc/my.cnf mysql:/etc/my.cnf
```

![](img/docker基本命令.png)

## 二、构建镜像

拉取基础镜像

添加项目文件到基础镜像

### docker file

## MySQL容器

```shell
docker pull mysql
# 启动
docker run 
	--name mysqldock 
	-e MYSQL_ROOT_PASSWORD=root 
	-e MYSQL_DATABASE=localtrptestcopy 
	-d 
	-p 3306:3306 
	 mysql
# 进入容器
docker exec -it mysqldock bash
# 退出容器
exit
# 保存容器，生成新镜像，mysql57是要保存的容器名，mysql是新镜像的repository
docker commit -m "localtrptestcopy" -a "jettopro" mysql57 mysql
```

[Docker 从入门到精通（建议收藏的教程） (qq.com)](https://mp.weixin.qq.com/s/FK2vi1JOdtdMlcJPNXr08w)
