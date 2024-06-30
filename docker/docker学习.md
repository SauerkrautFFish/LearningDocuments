https://www.bilibili.com/video/BV1Zn4y1X7AZ?p=8&vd_source=da303f8b38422434eae039096186e9ad





why docker

快速构建、分享、运行应用



加速我们的应用打包（如需要mysql，redis等，不需要去服务器手动安装，直接把环境打包），快速帮我们发布到docker hub，然后通过一行命令就能启动。



docker命令

docker pull redis 拉取镜像

docker run redis 跑redis容器（跑镜像启动起来的应用，容器之间互相隔离）

docker build xxx 构建镜像

docker push xxx 上传镜像



docker存储



docker网络



docker compose 



dockerfile





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

运行：docker run

查看：docker ps

停止：docker stop

启动：docker start

重启：docker restart

状态：docker stats

日志：docker logs

进入：docker exec

删除：docker rm