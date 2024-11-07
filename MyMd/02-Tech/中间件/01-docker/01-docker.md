## 1 程序性材料

### 0-安装docker

#### 方式-1

```xml
卸载
sudo yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine

指定从哪安装
sudo yum install -y yum-utils

sudo yum-config-manager \
--add-repo \
https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io

sudo systemctl start docker
sudo systemctl enable docker  自动启动

docker -v

sudo docker images


配置阿里云镜像加速
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://sajti7bb.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 方式-2

```xml
1--使用安装脚本下载Docke
wget -qO- https://get.docker.com/ | sh
```

安装中途断了网



强制删除镜像

```xml
#查看镜像
docker images 
#查看容器
docker ps 
容器属于镜像


使用-f选项强制删除，即docker rmi -f image-id.
使用镜像的仓库路径来删除，即docker rmi repository:tag.

重命名容器

docker rename oldname newname
```

### 1.1 官网

```
https://hub.docker.com/
https://docs.docker.com/
```

### 1.2 发布项目

#### 发布命令

```
docker build -t auth .
docker run -d --restart=always -p:10001:10001 auth

docker build -t gateway .
docker run -d --restart=always -p:19999:19999 gateway

```

#### 查看项目运行日志

```
docker logs -f -t --tail 100 noncar
sudo docker logs -f -t generator
sudo docker logs -f -t --tail 行数 容器名
```



#### 配置文件

```
FROM openjdk:8

COPY *.jar /app.jar
CMD ["--server.port=8080"]
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app.jar"]
```

### 1.3 安装容器

#### 01-MySQL

```
1-- 添加镜像
sudo docker pull mysql:5.7

2-- 运行
sudo docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/var/mysql \
-e MYSQL_ROOT_PASSWORD=ywy123abr \
-d mysql:5.7

3-- 运行
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=ywy123abr -d mysql:5.7

4-- 启动容器
sudo docker start mysql
sudo docker restart mysql

5-- 问题
run 和 start 的区别

6-- 进入容器终端
sudo docker exec -it mysql:5.7 /bin/bash
mysql -uroot -p

```

#### 02-Redis

```
sudo docker pull redis:7.0.12

1--redis文件配置目录
mkdir -p /mydata/redis/conf
2--持久化文件存放目录
mkdir -p /mydata/redis/data

3--启动方式--1
docker run --restart=always -p 6379:6379 -i -t --name redis -d redis:7.0.12  --requirepass ywy123abr --appendonly yes
--解释  --requirepass root 使得所有的连接都需要加上密码

3--启动方式--2 建议用这个 没有问题
docker run -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /mydata/redis/data:/data \
-d --name redis \
-p 6379:6379 \
--restart=always \
-t \
-i \
redis:7.0.12  redis-server  --appendonly yes --requirepass ywy123abr
--问题 
没有映射到端口号
/mydata/redis/redis.conf 这个是错误的，没有用 -v 命令映射到linux文件夹



4--端口号映射又出问题了
-- 加上版本号，或者说是具体的容器名称
-- 加上 -d 后台运行
sudo docker run -p 6379:6379 -i -t -d redis:7.0.12

```

修改配置文件

```
#下载修改配置文件  好像可以直接通过命令修改 不需要配置文件
bind 127.0.0.1 #注释掉这部分，使redis可以外部访问
daemonize no #用守护线程的方式启动
requirepass 你的密码#给redis设置密码
appendonly yes #redis持久化　　默认是no
tcp-keepalive 300 #防止出现远程主机强迫关闭了一个现有的连接的错误 默认是300

```

```
https://www.cnblogs.com/zjw-blog/p/17804379.html
```

#### 03-Sentinel

```
docker pull alibaba/sentinel-dashboard

docker pull bladex/sentinel-dashboard

docker images bladex/sentinel-dashboard

docker run --name sentinel -d -p 8858:8858 bladex/sentinel-dashboard


登陆密码都是 sentinel

http://139.224.248.106:8858
```

[教程1](https://blog.51cto.com/u_16175465/7337108)

[教程2](https://www.mainboot.com/article/index/id/45a3073.html)

#### 04-RabbitMQ

```
进入网站
http://139.224.248.106:15672/
```

安装

```
docker pull docker.io/rabbitmq:3.8-management

docker images
818bf18535d7

docker run --name rabbitmq -d -p 15672:15672 -p 5672:5672 818bf18535d7
```



.新添加一个账户

默认的`guest` 账户有访问限制，默认只能通过本地网络(如 localhost) 访问，远程网络访问受限，所以在使用时我们一般另外添加用户，例如我们添加一个root用户：

①执行`docker exec -i -t 3ae bin/bash`进入到rabbitMq容器内部

```
[root@localhost docker]# 
docker exec -it rabbitmq bin/bash
root@3ae75edc48e2:/# 
```

②执行`rabbitmqctl add_user root 123456` 添加用户，用户名为root,密码为123456

```
root@3ae75edc48e2:/# 
rabbitmqctl add_user root root 
Adding user "root" ...
```

③执行`rabbitmqctl set_permissions -p / root ".*" ".*" ".*"` 赋予root用户所有权限

```
root@3ae75edc48e2:/# rabbitmqctl set_permissions -p / root ".*" ".*" ".*"
Setting permissions for user "root" in vhost "/" ...
```

④执行`rabbitmqctl set_user_tags root administrator`赋予root用户administrator角色

```
root@3ae75edc48e2:/# rabbitmqctl set_user_tags root administrator
Setting tags for user "root" to [adminstrator] ...
```

⑤执行`rabbitmqctl list_users`查看所有用户即可看到root用户已经添加成功

```
root@3ae75edc48e2:/# rabbitmqctl list_users
Listing users ...
user	tags
guest	[administrator]
root	[administrator]
```

执行`exit`命令，从容器内部退出即可。这时我们使用root账户登录web界面也是可以的。到此，rabbitMq的安装就结束了，接下里就实际代码开发。



```
docker exec -it rabbitmq bin/bash

rabbitmqctl add_user root root

rabbitmqctl set_permissions -p / root ".*" ".*" ".*"

rabbitmqctl set_user_tags root administrator

rabbitmqctl list_users
```

```
docker update --restart=always 容器id 或 容器名称

docker update --restart=no 容器id 或 容器名称

docker update --restart=always $(docker ps -aq)
```

#### 05-ElasticSearch

```sql
docker pull elasticsearch:7.6.2
docker pull kibana:7.6.2

http://139.224.248.106:9200/
```



dokcer中安装elastic search

（1）下载ealastic search和kibana

```plain
docker pull elasticsearch:7.6.2
docker pull kibana:7.6.2
```

（2）配置

```plain
mkdir -p /mydata/elasticsearch/config  创建目录
mkdir -p /mydata/elasticsearch/data
echo "http.host: 0.0.0.0" >/mydata/elasticsearch/config/elasticsearch.yml

//将mydata/elasticsearch/文件夹中文件都可读可写
chmod -R 777 /mydata/elasticsearch/
```

（3）启动Elastic search

```plain
docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e  "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx512m" \
-v /mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /mydata/elasticsearch/data:/usr/share/elasticsearch/data \
-v  /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.6.2
```

设置开机启动elasticsearch

docker update elasticsearch --restart=always

（4）启动kibana：

docker run --name kibana -e ELASTICSEARCH_HOSTS=http://139.224.248.106:9200 -p 5601:5601 -d kibana:7.6.2

设置开机启动kibana

docker update kibana  --restart=always

（5）测试

查看elasticsearch版本信息： http://139.224.248.106:9200/

```plain
{
    "name": "0adeb7852e00",
    "cluster_name": "elasticsearch",
    "cluster_uuid": "9gglpP0HTfyOTRAaSe2rIg",
    "version": {
        "number": "7.6.2",
        "build_flavor": "default",
        "build_type": "docker",
        "build_hash": "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
        "build_date": "2020-03-26T06:34:37.794943Z",
        "build_snapshot": false,
        "lucene_version": "8.4.0",
        "minimum_wire_compatibility_version": "6.8.0",
        "minimum_index_compatibility_version": "6.0.0-beta1"
    },
    "tagline": "You Know, for Search"
}
```

显示elasticsearch 节点信息http://139.224.248.106:9200/_cat/nodes ，

127.0.0.1 76 95 1 0.26 1.40 1.22 dilm * 0adeb7852e00

访问Kibana： http://139.224.248.106:5601/app/kibana

####   06-ZipKin

```xml
docker pull openzipkin/zipkin
docker run --name zipkin -d -p 9411:9411 openzipkin/zipkin
```

#### 07-Nacos

教程

```xml
https://blog.51cto.com/u_14736355/5853015

https://www.cnblogs.com/chenxingyang/p/15780213.html
https://gitee.com/mirrors/Nacos/blob/2.0.3/distribution/conf/nacos-mysql.sql
```



```xml
docker pull nacos/nacos-server

mkdir -p /home/nacos/logs/
mkdir -p /home/nacos/init.d/

vim /home/nacos/init.d/custom.properties 

docker inspect mysql | grep IPAddress
```



配置文件

```xml
#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://124.223.38.120:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=true&serverTimezone=Asia/Shanghai
db.user.0=nacos
db.password.0=nacos
```



```xml
# 建库
create database nacos;
use nacos;

-- 执行或者导入 nacos-db.sql，不知道为什么这个文件里的注释还写了 【数据库全名 = nacos_config】
-- 可以直接复制粘贴到 navicat 或 sqlyog 上执行，200多行不是很长

# 创建 nacos 单独使用的一个用户，也可以直接把 root 用户给他
create user 'nacos'@'%' IDENTIFIED BY 'nacos';
-- 库nacos的所有表的执行存储过程、CRUD权限
grant execute, insert, select, update on nacos.* to 'nacos'@'%';
-- 刷新权限
FLUSH PRIVILEGES;


-- 查看权限
show grants for nacos;
```



```xml
docker run \
    --name nacos \
    -d \
    -p 8848:8848 \
    -p 9848:9848 \
    -p 9849:9849 \
    --restart=always \
    -e JVM_XMS=256m \
    -e JVM_XMX=256m \
    -e MODE=standalone \
    -v /home/docker/nacos/logs:/home/nacos/logs \
    -v /home/docker/nacos/init.d/custom.properties:/home/nacos/init.d/custom.properties \
    nacos/nacos-server:latest


124.223.38.120:8848
http://124.223.38.120:8848/nacos


139.224.248.106:8848
http://139.224.248.106:8848/nacos
```



http://139.224.248.106:8848/nacos



配置文件

放在 /home/nacos/init.d/custom.properties 

```xml
#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://124.223.38.120:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=true&serverTimezone=Asia/Shanghai
db.user.0=nacos
db.password.0=nacos
#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://139.224.248.106:3306/nacos?useSSL=false&characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=true&serverTimezone=Asia/Shanghai
db.user.0=nacos
db.password.0=nacos



#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://139.224.248.106:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=true&serverTimezone=Asia/Shanghai
db.user.0=nacos
db.password.0=nacos
```





问题：启动后连接不了

```xml
会不会是端口号被占用了
和fast这个服务的端口号有冲突 虽然这个服务没有明显看到用8848 用的是8080，但是刻印nacos也需要这个端口，
```

#### 08-Nginx

139.224.248.106:80

[安装命令](https://zhuanlan.zhihu.com/p/453181968)

```xml
docker pull nginx

docker run --name nginx -p 80:80 -d nginx


# 创建挂载目录
mkdir -p /home/nginx/conf
mkdir -p /home/nginx/log
mkdir -p /home/nginx/html

2、拷贝nginx容器对应的文件默认配置
docker cp nginx:/etc/nginx/nginx.conf /opt/docker/nginx/conf/nginx.conf
docker cp nginx:/etc/nginx/conf.d /opt/docker/nginx/conf.d
docker cp nginx:/usr/share/nginx/html /opt/docker/nginx
3、停止并删除nginx容器
docker stop nginx 

docker rm nginx
重新启动nginx镜像重新新容器

少了一个 \ 
sudo docker run -p 9002:80 -i -t nginx

docker run \
-p 9002:80 \
--name nginx \
--net host \
-v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/conf/conf.d:/etc/nginx/conf.d \
-v /home/nginx/log:/var/log/nginx \
-v /home/nginx/html:/usr/share/nginx/html \
-d nginx:latest

单行模式
docker run -p 80:80  -v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/conf/conf.d:/etc/nginx/conf.d -v /home/nginx/log:/var/log/nginx -v /home/nginx/html:/usr/share/nginx/html -d nginx:latest


curl 127.0.0.1:9002

curl 127.0.0.1:80  linux 本地访问这个

curl 124.223.38.120:9002

http://124.223.38.120/ windows直接访问这个
```

![img](https://cdn.nlark.com/yuque/0/2023/png/26349101/1701672898763-73af4960-a6e6-4edc-8edc-4aff170c32dc.png)

教程链接

```xml
https://zhuanlan.zhihu.com/p/453181968
https://juejin.cn/post/7176299143659257893
```



```xml
# 生成容器
docker run --name nginx -p 80:80 -d nginx
# 将容器nginx.conf文件复制到宿主机
docker cp nginx:/etc/nginx/nginx.conf /home/nginx/conf/nginx.conf
# 将容器conf.d文件夹下内容复制到宿主机
docker cp nginx:/etc/nginx/conf.d /home/nginx/conf/conf.d
# 将容器中的html文件夹复制到宿主机
docker cp nginx:/usr/share/nginx/html /home/nginx/


# 直接执行docker rm nginx或者以容器id方式关闭容器
# 找到nginx对应的容器id
docker ps -a
# 关闭该容器
docker stop nginx
# 删除该容器
docker rm nginx
 
# 删除正在运行的nginx容器
docker rm -f nginx


docker run \
-p 9002:80 \
--name nginx \
--net host \
-v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/conf/conf.d:/etc/nginx/conf.d \
-v /home/nginx/log:/var/log/nginx \
-v /home/nginx/html:/usr/share/nginx/html \
-d nginx:latest


139.224.248.106:9002
```



端口号映射出问题

```xml
单行的就没有问题
docker run -p 80:80  -v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/conf/conf.d:/etc/nginx/conf.d -v /home/nginx/log:/var/log/nginx -v /home/nginx/html:/usr/share/nginx/html -d nginx:latest

139.224.248.106:80

奇怪
```

#### 09-图形化界面

```
https://zhuanlan.zhihu.com/p/667603932

docker pull portainer/portainer

docker volume create portainer_db

单行 不用 / 换行符
docker run -d --name docker-web -p 9000:9000     -v /var/run/docker.sock:/var/run/docker.sock     -v portainer_db:/data     portainer/portainer


http://139.224.248.106:9000/
root: admin
pass: dockeradminpass
```

### 1.4-常用命令

```
1--docker设置开机自启
system enable docker

 2--查看镜像
docker images

3--查看所有容器
docker ps -a 

4--删除容器
docker rm containerID

5--删除镜像
docker rmi id

--删除可以直接使用前3位符号

6--查看日志
docker logs containerID

7--设置docker中MySQL自启
docker update mysql --restart=always  4ddabf72583f

8--停止一个容器
docker stop id

9--清理页面
clear
```



## 2-问题

### 01-启动闪退

网上的一些原因分析

1、启动命令不对

2、虚拟内存不够

查看日志: docker log containerID 或者 容器名称

```xml
[root@VM-16-4-centos ~]# docker logs 53f5850fb737
2024-01-25 03:10:06+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.44-1.el7 started.
2024-01-25 03:10:06+00:00 [ERROR] [Entrypoint]: mysqld failed while attempting to check config
	command was: mysqld --verbose --help --log-bin-index=/tmp/tmp.MPMo97yHQg
	mysqld: Can't read dir of '/etc/mysql/conf.d/' (Errcode: 2 - No such file or directory)
mysqld: [ERROR] Fatal error in defaults handling. Program aborted!
```

mysql：5.7版本的文件路径应该是: /var/mysql/conf.d/  我给的是/var/mysql/conf.d/

### 02-拉去Redis超时

```
1--描述：用脚本下载docker容器，拉取mysql容器没有报错，然redis的时候出问题
2--脚本
wget -qO- https://get.docker.com/ | sh
# 或
curl -fsSL https://get.docker.com/ | sh

3--问题
Error response from daemon: Get "https://registry-1.docker.io/v2/": net/http: TLS handshake timeout

4--解决
5--配置阿里云镜像加速
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://sajti7bb.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

```

