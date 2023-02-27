# Docker

1. for beginner：https://docker-curriculum.com/

2. 一些简单要点：

   1. 方便deploy：一个镜像，将需要的东西打包在一起，直接拉起来运行。比虚拟机更轻量。

   2. 安装好docker服务端，启动docker daemon程序后，就可以使用docker cli 命令来拉镜像、启容器了。

   3. 核心几个概念：image、container、docker daemon、docker client、 docker hub

   4. 默认跑起来的容器，端口是不expose出来的。需要使用 -P 来暴露端口到容器外部

   5. Image有 base image 和 child image之分。 base image 一般是os系统如 ubuntu，centos等，child image是在 base image之上再添加功能。

   6. 官方的image一般一个词，如python、ubuntu、nginx； 个人上传的镜像一般是 user/image-name 格式

   7. 构建一个image需要一个Dockerfile，Dockerfile描述构建和激动镜像的步骤（一些类似gnu的命令）

   8. 多个容器之间通信（如app容器访问es容器），可以建立一个网络，然后在两个容器启动的时候，指定使用该网络。例子

      ```bash
      #!/bin/bash
      
      # build the flask container
      docker build -t yourusername/foodtrucks-web .
      
      # create the network
      docker network create foodtrucks-net
      
      # start the ES container, --net指定使用哪个网络
      docker run -d --name es --net foodtrucks-net -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.3.2
      
      # start the flask app container 使用 --net 指定使用哪个网络
      docker run -d --net foodtrucks-net -p 5000:5000 --name foodtrucks-web yourusername/foodtrucks-web
      ```

   9. 多容器编排一般使用成熟的工具，如docker-compose， 或者k8s。