#docker 安装一个lnmp环境
## 准备工作
首先安装centos
```
docker pull centos
sudo docker run -ti --name first centos bash   //进入容器centos中，并运行bash命令
```

>    命令解释：  
>Docker run 是从一个镜像运行一个容器的指令。    
>-ti 参数的含义是：terminal interactive，这个参数可以让我们进入容器的交互式终端。   
>--name 指定容器的名字，后面的 first 就是我们给这个容器起的名字。    
>centos 是致命从哪个镜像运行容器，centos是仓库名，16.04是标签。 
>bash 指明我们使用 bash 终端。

因为安装的centos镜像是一个全新的，简洁版的环境，所以如果要安装其他软件，需要先安装``wget``
```
yum install wget
```

## 开始安装lnmp
进入[lnmp官网](https://lnmp.org/install.html) ,按照教程进行安装
```
wget -c http://soft.vpser.net/lnmp/lnmp1.4.tar.gz && tar zxf lnmp1.4.tar.gz && cd lnmp1.4 && ./install.sh lnmp
```

安装结果：   
![安装成功](https://lnmp.org/images/1.4/lnmp1.4-install-success.png)    

- LNMP添加、删除虚拟主机及伪静态使用教程   [参考教程](https://lnmp.org/faq/lnmp-vhost-add-howto.html)
