# Docker 常用命令
## docker search 镜像名
    学会使用命令行的工具来检索名字叫做tutorial的镜像。
![查询结果](https://github.com/it037218/Document/blob/master/Images/002.jpeg?raw=true)
```
docker search php
```

## docker pull 镜像名
    下载容器镜像
    根据上面search出来的结果进行下载。

## docker run 容器
    docker run命令有两个参数，一个是镜像名，一个是要在镜像中运行的命令。    

## docker commit 
    保存对容器的修改
    首先使用docker ps -l命令获得安装完ping命令之后容器的id。然后把这个镜像保存为learn/ping。
    执行完docker commit命令之后，会返回新版本镜像的id号。

## docker ps 
    检查运行中的镜像

## docker inspect 
    根据``docker ps``展示的结果中的ID来查看某一镜像的完整信息

## docker images
    docker images命令可以列出所有安装过的镜像。

## docker push
    docker push命令可以将某一个镜像发布到官方网站。

