---
title: docker基础(-)
date: 2017-03-31 00:39:00
tags:
---

当一个项目组件以及功能越来越多，开发人员越来越多的时候，很难保证大家都熟悉某一种语言或者框架。传统的项目模块间互相依赖，代码量大，项目臃肿。现在软件开发更提倡微服务，小型的模块架构，模块之间减少互相依赖而只提供对外的API，docker非常适合微服务架构。

docker原意是码头工人，container原意集装箱,集装箱把货物之间分隔，提高轮船运输率，是运输业革命性的创新。docker把每个组件之间之间分隔。不同于传统的虚拟机，docker模拟的是一个个容器，每个容器都建立在images基础上，docker容器实际上是给镜像上添加一个读写层。 


docker文档里面包含很多工具，网上许多教程都是基于这些工具而写的，初学者如果一开始没有接触过，很容易混淆，并且不知道哪些是自己需要用的：

- Dockerfile
- docker-compose
- docker-machine
- docker swarm
- docker hub
- docker toolbox

我们先不管上面的概念，以先从最基础的指令开始了解，再一步步介绍：

    docker run ubuntu（ubuntu默认bash就是 /bin/bash，所以后面不加此指令也可以）

这条指令的意思就是利用docker运行一个基于ubuntu镜像的容器:
    
    Unable to find image 'ubuntu:latest' locally
    latest: Pulling from library/ubuntu
    6bbedd9b76a4: Downloading [======>      ] 5.997 MB/49.87 MB
    fc19d60a83f1: Download complete 
    de413bb911fd: Download complete 
    2879a7ad3144: Download complete 
    668604fde02e: Download complete 

docker会先尝试从本地寻找ubuntu这个镜像，如果没有找到的话，会从docker hub这个公有仓库里面下载到本地。ubuntu镜像包含了什么呢，从[docker-ubuntu](https://hub.docker.com/_/ubuntu/)可以看到这个镜像是用官方的tar包构建。

>What's in this image?
>This image is built from official rootfs tarballs provided by Canonical (specifically, https://partner-images.canonical.com/core/).

下载完之后运行

    docker ps -a

    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
    7e74b2522c3a        ubuntu              "/bin/bash"         14 seconds ago      Exited (0) 12 seconds ago                       hopeful_pare
这句指令列出现在所有容器的状态与基本信息，我们可以看到

- CONTAINER ID 容器唯一ID为7e74b2522c3a（在你的电脑会有所不同），
- IMAGE 基于ubuntu官方镜像构建（ubuntu镜像也可以选择不同的版本以及不同个人或组织修改过的版本）
- COMMAND https://github.com/dockerfile/ubuntu/blob/master/Dockerfile
- CREATED 创建时间
- STATUS  状态, 状态分为下面几种，具体含义可以参考官方文档

    1. created 
    2. restarting
    3. running
    4. paused
    5. exited	
    6. dead

- PORTS 运行的端口

        docker run -it -p 6677:6677 ubuntu

这条指令告诉docker把宿主，也就是此物理机的6677端口，与容器的6677端口相绑定。除了端口可以相互绑定，文件系统，也就是docker里面的volume也可以用类似的方法绑定。

- NAMES 容器的名字
可以通过 -name 指定，没有指定的话docker会自己分配一个给容器

回到例子1，我们看到这个容器运行之后STATUS显示马上退出了，但是第二个例子进入了容器内的终端，（通过 -it）并且STATUS是UP...，这里我一开始也觉得很困惑，为什么刚启动马上就退出了。后来了解到根据docker容器设计的思想，容器的生命周期是短暂的。它基于镜像，运行完指定的指令之后就会退出。而常规的数据库服务与web服务是一直运行的，所以不会退出。当有时候你需要做一些一次性的工作，例如数据库表的建立，此处以Django为例，建立migration记录：

    docker run -d your_images python manage.py makemigrations

运行完之后这个container就EXITED了。也可以删除此container了。
关于docker run的具体资料可以参考[Docker run reference](https://docs.docker.com/engine/reference/run/)，

先介绍第一个工具。

- Dockerfile
默认的docker官方image都是以最少组件为目的，和虚拟机不同，拉一个ubuntu镜像下来很多基本命令都用不了，例如curl，因为官方image默认就没有安装。如果你需要安装curl，那你就可以基于官方镜像建立自己的镜像，这时候就可以使用[Dockerfile](https://docs.docker.com/engine/reference/builder/)，建立文件名为Dockerfile的文件，然后写入：

    FROM ubuntu
    RUN apt-get update
    RUN apt-get install -y curl

之后在该文件同级目录运行（注意最后的.）

    docker build -t my_ubuntu .
就建立了你自己的可以自带curl的镜像：
    
    docker run -it my_ubuntu 
    curl --help

Dockerfile里面有几个常用的命令：

- FROM ubuntu
从ubuntu镜像的基础上建立镜像
- RUN apt-get update
运行apt-get update命令（通常是安装包与组件）
- CMD echo "Hello world"
运行echo "Hello world" 命令（常规命令）
RUN与CMD的区别可以参考这里[Docker RUN vs CMD vs ENTRYPOINT](http://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/)
- EXPOSE 3306
基于此镜像建立的容器对外界暴露的端口

    >The EXPOSE instruction indicates the ports on which a container will listen for connections. Consequently, you should use the common, traditional port for your application. For example, an image containing the Apache web server would use EXPOSE 80, while an image containing MongoDB would use EXPOSE 27017 and so on.

- ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH
设置容器的环境变量

- ADD or COPY
    
        COPY requirements.txt /tmp/
        RUN pip install --requirement /tmp/requirements.txt
因为RUN里的命令是在镜像内部进行的，假如你需要读取宿主的文件来执行命令。如上面所示，先把当前目录下的requirements.txt拷贝到容器中的/tmp/文件夹里，然后运行RUN命令。之后基于此镜像运行容器的时候，只要你没有使用-v绑定宿主与容器的文件系统，/tmp/就都会有requirements.txt这个文件。

- VOLUME

        FROM ubuntu
        RUN mkdir /myvol
        RUN echo "hello world" > /myvol/greeting
        VOLUME /myvol
镜像中先创建了/myvol文件夹，运行echo "hello world"，之后创建了/myvol文件夹

        docker build -t volume-example .
        docker run -it -v /tmp/:/myvol volume-example
通过-v参数可以把宿主的/tmp文件夹与容器的/myvol文件夹绑定在一起，只要修改其中一方，另外一方也会被修改。

- docoker-compose
Dockerfile可以说帮我们简化了许多工作，docker-compose则进一步减少我们的工作量，我们看看docker-compose的例子：

        version: '2'
        services:
          mysqldb:
            restart: always
            image: mysql:latest
            ports:
              - "3306:3306"
            environment:
              MYSQL_ROOT_PASSWORD: example
              MYSQL_USER: www
              MYSQL_PASSWORD: www
              MYSQL_DATABASE: example_database
            volumes:
              - mysqldata:/var/lib/mysql
              - ./config/:/etc/mysql/conf.d
          web:
            restart: always
            build: ./example_code
            env_file: .env
            environment:
              DEBUG: 'true'
              PYTHONPATH: '/usr/src/app/app_platform'
            command: bash -c "python manage.py runserver"
            volumes:
              - ./example_code:/usr/src/app
              - /usr/src/app/static
            ports:
              - "8000"
            dns:
                - 114.114.114.114
                - 8.8.8.8
            links:
              - mysqldb
          nginx:
            restart: always
            build: ./nginx/
            ports:
              - "80:80"
            volumes:
              - ./example_code:/usr/src/app
              - ./nginx/sites-enabled/:/etc/nginx/sites-enabled
            links:
              - web:web
        volumes:
            mysqldata:

一开始看可能一头雾水，放松点，我们慢慢一步步分析这个文件，首先整个服务分为三大块，

- mysql数据库，web服务，nginx服务
- 先看代码

        version: '2'
        services:

因为docker-compose暂时有两个版本，新版本多了一些参数和选项，我们这里使用第二个版本，下面的代码都是compose中的组件

- mysql

        mysqldb:
            restart: always //1
            image: mysql:latest //2
            ports: //3
              - "3306:3306"
            environment: //4
              MYSQL_ROOT_PASSWORD: example
              MYSQL_USER: www
              MYSQL_PASSWORD: www
              MYSQL_DATABASE: example_database
            volumes: //5
              - mysqldata:/var/lib/mysql
              - ./config/:/etc/mysql/conf.d

1. 如果遇到错误自动重启（如果错误没有解决会不断重启）
2. 使用mysql官方镜像（也可以使用自己基于其修改的镜像，这里可以使用build，指定Dockerfile的位置）
3. 主机的3306端口与容器的3306端口相连接。
4. 设置mysql的环境变量，具体设置docker hub的官方镜像里一般都有，可以参考[docker-hub-mysql](https://hub.docker.com/_/mysql/)
5. mysqldata:/var/lib/mysql，在代码的最下面可以看到 

        volumes: 
            mysqldata:

    docker1.9之后专门为数据持久化而推出named volume，[Manage data in containers](https://docs.docker.com/engine/tutorials/dockervolumes/#/creating-and-mounting-a-data-volume-container)，通过此指令创建一个独立的volume，不会因为删除container，image而丢失，可以通过

        docker volume list

    查看现有宿主的volume，所以这里指把容器中/var/lib/mysql中所有数据都独立存储在mysqldata这个volume中，接下来

        ./config/:/etc/mysql/conf.d
    把当前目录中的config文件夹与容器中/etc/mysql/conf.d想关联，这里等于重写了mysql的默认配置。具体可以参考[MySQL Docker Containers: Understanding the basics](http://severalnines.com/blog/mysql-docker-containers-understanding-basics)，

- web
        
        web:
            restart: always
            build: ./example_code //1
            env_file: .env //2
            environment:
              DEBUG: 'true'
              PYTHONPATH: '/usr/src/app/app_platform'
            command: bash -c "python manage.py runserver" //3
            volumes:
              - ./example_code:/usr/src/app
              - /usr/src/app/static
            ports:
              - "8000"
            dns:
                - 114.114.114.114
                - 8.8.8.8
            links: //4
              - mysqldb

1. 从./example_code文件夹中的Dockerfile建立并使用该镜像
2. 可以把你需要的环境变量都写在一个文件里，然后通过env_file引用，注意

    >Environment variables specified in environment override these values.
   如果同时你也指定了environment变量，env_file中变量将被覆盖
3. 容器启动时运行的指令
4. 因为web服务需要连接mysql，原本代码可能是通过IP+端口的方式连接，使用links的意思是通过查找mysqldb就能直接连接数据库，相当于数据库别名，这里以django为例：

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'example',
                'USER': 'www',
                'PASSWORD': 'www',
                'HOST': 'mysqldb',
                'PORT': '3306',
            }
        }
留意HOST参数，我们直接使用了mysqldb取代了之前的IP
- nginx

        nginx:
            restart: always
            build: ./nginx/
            ports:
              - "80:80"
            volumes:
              - ./example_code:/usr/src/app
              - ./nginx/sites-enabled/:/etc/nginx/sites-enabled
            links:
              - web:web
这部分基本与web一样，接着运行

        docker-compose build
        docker-compose up -d

建立镜像，并且基于docker-compose中的设置建立容器。**容器会按照compose中的顺序启动，但后面的容器不会等前面的准备就绪才启动**，也就是说一开始docker启动mysql->web->nginx，不过因为mysql启动与建立需要时间比较久，所以当web与nginx都启动后mysql有可能还没有准备好，这时候查看log的话就会看到无法连接mysqldb的日志，不过只需要等待一会就好。文档有强调这点：

>You can control the order of service startup with the depends_on option. Compose always starts containers in dependency order, where dependencies are determined by depends_on, links, volumes_from and network_mode: "service:...".

>However, Compose will not wait until a container is “ready” (whatever that means for your particular application) - only until it’s running. There’s a good reason for this.

下一章我们会继续介绍接下来的几款工具。
