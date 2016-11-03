# docker与docker-compose使用简介

docker是一个开源的应用容器引擎，docker-compose是一个部署多个容器的简单但是非常必要的工具，利用它们可以轻松、高效解决环境部署的难题

## 基本使用方式

### CentOS安装docker
升级yum
>$ sudo yum update

配置源

>$ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'

>[dockerrepo]

>name=Docker Repository

>baseurl=https://yum.dockerproject.org/repo/main/centos/7/

>enabled=1

>gpgcheck=1

>gpgkey=https://yum.dockerproject.org/gpg

>EOF

安装docker
>$ sudo yum install docker-engine

启用服务
>$ sudo systemctl enable docker.service

启动docker守护进程
>$ sudo systemctl start docker

设置开机自动启动docker
>$ sudo systemctl enable docker

### CentOS安装docker-compose
文件获取
>curl -L "https://github.com/docker/compose/releases/download/1.8.1/docker-compose-$(uname -s)-$(uname -m)" > /usr/local/bin/docker-compose

为二进制可执行文件添加权限
>$ chmod +x /usr/local/bin/docker-compose

如果安装出现问题，可以使用下列方法
>$ pip install docker-compose

安装完后检查版本
>$ docker-compose --version

## 配置文件

配置文件名必须为docker-compose.yml或者docker-compose.yaml，跑项目用一般配置四个容器，分别是nginx、php-fpm、db、redis

文件参考结构如下

	version: '2'
	services:
	  nginx:
	    container_name: nginx2			//容器名，在compose里通过容器名访问容器
	    image: nginx					//镜像名，compose会根据此项执行docker pull <imgae> 来创建相关镜像
	    restart: always					//容器因故障停止后，自动restart
	    ports:							//端口映射，前者为宿主机的端口，后者为容器的端口，配置此项后，我们可以通过访问系统的IP来访问到容器
	       - "80:80"    					//有时候端口被占用导致无法启动容器，我们只需要换一个端口比如8080即可
	      - "443:443"
	    volumes:						//路径、文件映射。配置将容器里的文件挂在到宿主机里来运行容器。需要注意的是，如果是文件映射，需要先创建一个空白的同名文件，否则初始化时会自动创建为目录
	      - ../www:/data/www				//nginx容器我们一般将项目目录和配置文件挂载到宿主机上，这样便于我们开发和修改
	      - ../nginx/nginx.conf:/etc/nginx/nginx.conf
	      - ../nginx/conf.d:/etc/nginx/conf.d
	    depends_on:						//依赖容器，nginx的容器需要php环境的支持
	      - php-fpm
	  php-fpm:
	    build:
	      context: .
	      dockerfile: Dockerfile-phpfpm    //在配置文件下额外有个dockerfile文件，用于配置php环境
	    container_name: php2
	    image: hw/phpfpm
	    restart: always
	    ports:
	      - "9000:9000"
	      - "12307:12307"
	      - "9527:9527"
	    volumes:
	      - ../www:/data/www
	    depends_on:
	      - db
	      - redis
	  db:
	    container_name: mysql2
	    image: mysql:5.7.12
	    restart: always
	    user: root
	    environment:
	      MYSQL_ROOT_PASSWORD: root
	    restart: always
	    ports:
	      - "3306:3306"
	    volumes:
	      - ../mysql:/var/lib/mysql
	  redis:
	    container_name: redis2
	    image: redis:3.0.7-alpine
	    restart: always

##项目目录结构

我们在宿主机创建一个workspace的目录

在workspace目录里创建一个docker目录，并依此还有docker-compose.yml里面配置到的需要挂到宿主机的目录、文件

目录参考




配置好文件和项目目录后，在docker-compspe.yml的目录下进行初始化
>docker-compose up -d

此时docker-compose会自动检查各个容器是否存在、是否最新，进行pull或者recreate，然后自动启动各个容器

##常用命令

为了我们更好的使用各个容器，先罗列一些常用命令如下：

启动/重启容器
>$ docker start/restart [container_name]

停止容器
>$ docker stop [container_name]

进入容器命令行
>$ docker exec -ti [container_name] bash

在容器内执行命令
>$ docker exec -ti [container_name] [command] [options]

跟踪调取容器日志
>$ docker logs -ft [container_name]

查看容器详细信息
>$docker inspect [container_name]

列出正在运行的容器
>$docker ps

列出所有容器
>docker ps -a

删除容器
>$docker rm [container_name]

删除镜像
>$docker rmi [image_name]

查看更多命令请执行
>$docker --help

##dphp命令
为了让大家更方便的使用php容器的命令来开发，故提供一种可以更便捷的使用docker exec -ti php的命令———— dphp

###配置方法

在Linux系统里创建一个shell脚本，dphp.sh，我们把它放在/opt/目录下

脚本内容如下（可自己根据实际需求定制修改）：
	
	#!/bin/sh
	VIF=$(pwd|grep "www" | wc -l)
	VAR=$(pwd)
	if test $VIF = 0; then
	echo 'only use dphp in project folder'
	else
	echo ${pwd}
	docker exec -ti php2 "/data/www/""${VAR##*/}/""$@"	//请把此处的php2替换成你实际的php-fpm容器名，使用docker ps查看
	fi

配置环境变量
> vi /etc/bashrc

在环境变量的最后一行加上 
>alias dphp='/opt/dphp.sh'

重新登录终端，dphp即生效

###使用要点

- 在项目目录下比如/www/project1执行dphp yii，即相当于docker exec -ti php /data/www/project1/yii
- 可以在后面加参数 比如 dphp yii migrate/create customer_status
- 在项目目录下使用才有效，此处限制了项目目录的上一层文件夹名为www，可自行修改shell脚本定制
