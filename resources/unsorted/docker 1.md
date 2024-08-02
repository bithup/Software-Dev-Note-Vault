# Docker笔记

## 一、安装启动

### 在线安装

```shell
yum install docker
```

### 离线安装

```shell
#下载所需rpm包
rpm -ivh *.rpm --nodeps --force
```

### 启动

```shell
systemctl start docker
#一些常用docker命令
docker version
docker info
docker pull centos:latest
#查看镜像
docker images [-a]
#删除镜像
docker rmi [镜像名]
#查看、启动、停止、进入、删除容器
docker ps [-a]
docker start|stop|restart|attach|rm [容器名]



```

## 二、拉取镜像、构建镜像

拉取基础镜像

添加项目文件到基础镜像

## 三、启动和关闭镜像容器

## 四、容器数据持久化

## 五、如何处理日志文件等不需要持久花的文件，端口映射，如何一次启动多个容器

## 六、离线环境转移docker镜像

## 七、mysql容器

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



八、redis容器

九、nginx容器

十、rabbitMQ容器