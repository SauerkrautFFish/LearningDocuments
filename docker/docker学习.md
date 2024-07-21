https://www.bilibili.com/video/BV1Zn4y1X7AZ?p=8&vd_source=da303f8b38422434eae039096186e9ad





why docker

快速构建、分享、运行应用



加速我们的应用打包（如需要mysql，redis等，不需要去服务器手动安装，直接把环境打包），快速帮我们发布到docker hub，然后通过一行命令就能启动。



docker命令

docker pull redis 拉取镜像

docker run redis 跑redis容器（跑镜像启动起来的应用，容器之间互相隔离，本地没有则会去hub拉取）

docker build xxx 构建镜像

docker push xxx 上传镜像



docker存储

docker rm $(docker ps -aq)  // 删除所有容器



docker run时可以目录挂载：-v /app/nghtml:/usr/share/nginx/html，格式为主机的目录:镜像的目录，这样主机里修改和镜像里修改，对方都能看得到，但是一开始镜像里/usr/share/nginx/html的内容就是/app/nghtml里的内容，本地的如果没有/app/nghtml目录会被自动创建



卷映射：-v ngconf:/etc/nginx，一开始ngconf的内容时镜像里/etc/nginx的内容，本地的ngconf如果没有可以自动被创建，和目录挂载的区别是本地路径不以 / 开头，ngconf会被统一创建在/var/lib/docker/volumes/<volume-name>里，所以重启也和目录挂载一样不会丢失，但好像每个版本不太一样？可以通过docker inspect看看详情，上面我记得有路径



docker volume ls，列出所有卷



docker网络

在docker启动两个服务，然后服务1要访问服务2，是可以的，因为它们被设置成了同一个网络docker0，使用容器ip+容器端口即可访问，通过docker inspect [container]可以看到网络情况



docker network create mynet // 创建自定义网络，因为容器如果重启ip会变，最好的方式是通过域名访问，但是默认网络docker0不支持域名访问，所以需要创建个自定义网络

docker network ls

docker run -d -p 88:80 --name app1 --network mynet nginx

docker run -d -p 89:80 --name app2 --network mynet nginx

在app2里可以通过curl https://app1:80 就能访问app1



docker run如果要设置环境变量，可以使用-e参数，但是得镜像支持这些环境变量的设置，感觉是镜像本身有些配置，然后你可以约定大于配置。



docker compose

compose是组成的意思

docker compose是把你想要启动的所有容器和配置写进compose.yaml里，批量管理，比如说启动、关闭，命令默认找compose.yaml文件，如果需要指定，使用-f参数。

docker compose up -d，up就是上线，-d就是后台方式

docker compose down，即下线

docker compose stop x1 x3 // 停止yaml定义的某些应用

docker compose start x1 x2 x3 // x1,x2,x3是yaml定义的3个应用，up是第一次启动用，后面停止后，想再次启动可以用start

docker compose scale x2=3 // 扩容，使得x2的实例变成3份



compose.yaml主要的顶级元素：

name：名字

services：服务

networks：网络

volumes：卷

configs：配置

secrets：密钥

例如要创建个博客以及数据库的镜像：

![image-20240707134307012](F:\markdowm图片保存\image-20240707134307012.png)



![image-20240707134334934](F:\markdowm图片保存\image-20240707134334934.png)







dockerfile

用来制作镜像

dockerfile里需要包含基础环境，软件，启动引导命令。

https://docs.docker.com/reference/dockerfile

from 指定镜像基础环境

run 运行自定义命令

cmd 容器启动命令或参数

label 自定义标签

expose 指定暴露端口

env 环境变量

add 添加文件到镜像

copy 复制文件到镜像

entrypoint 容器固定启动命令

volume 数据卷

user 指定用户和用户组

workdir 指定默认工作目录

arg 指定构建参数



写好dockerfile后，使用该命令构建镜像：docker build -f Dockerfile -t myjavaapp:v1.0 .



docker分层机制类似于git管理，共同的部分不会占用多一倍空间，比如说下载了nginx镜像后，基于该镜像修改了点东西，然后打成新镜像，那么没被修改的部分是公用的。



## 实验

启动一个nginx，并将它首页改成自己页面，发布出去，让所有人都能使用该镜像



步骤

下载镜像->启动容器->修改页面->保存镜像->分享社区



详细

1.下载镜像

检索：docker search nginx

下载：docker pull nginx

查看本地镜像列表：docker images

删除：docker rmi 【repository:tag】，例如docker rmi nginx:latest，或者docker rmi 【image id】





2.启动容器

运行：docker run（通过docker run --help可以看支持参数，-d后台启动，-d是deamon的意思；--name可以给容器取名字；-p 外部端口:内部端口，开放服务给外部，外部端口不可以重复，但是内部端口可以，因为容器之间是隔离的）

查看：docker ps

停止：docker stop

启动：docker start

重启：docker restart

状态：docker stats

日志：docker logs

进入：docker exec

删除：docker rm（-f参数可以强制删除容器，否则需要先stop，再删除，rm删除容器，rmi删除镜像）



3.修改页面

docker -it [容器] /bin/bash

cd /usr/share/nginx/html

echo "<h1>hello world</h1>" > index.html



4.保存镜像

提交：docker commit [container] [repository:tag]（建议--help可以看参数，把容器打包成新镜像，-m可以写提交信息），例如：

docker -m "update index" mynginx myginx:v1.0

保存：docker save [image]，例如：

docker save -o mynginx.tar myginx:v1.0，就能把myginx镜像打成tar包

加载：docker load，例如：docker load -i mynginx.tar，从tar包加载变成镜像



5.分享社区

登录：docker login

命名：docker tag source_image:tag target_image（push之前需要先改tag，因为要求镜像是[username]/[iamge:tag]，以区分不同人的镜像），例如：

docker tag mynginx:v1.0 jackjhhuang/mynginx:v1.0，（这里最好再改一个docker tag mynginx:v1.0 jackjhhuang/mynginx:latest，然后上传，因为这样不用加版本号，别人也能pull）



推送：docker push [image:tag]