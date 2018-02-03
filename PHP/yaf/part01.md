# yaf 安装
这里主要介绍如何在mac下面通过brew安装yaf

## 搜索yaf相关的软件
```
brew search yaf
```
搜索结果如下
![brew search结果](https://github.com/it037218/Document/blob/master/Images/003.jpeg?raw=true)
这里可以根据自己的选择安装对应版本的yaf
```
brew install yaf
```

安装成功之后需要配置yaf
一般情况下在mac中brew安装的文件都位于``/usr/local/Cellar``
这里需要吧yaf.so配置到php.ini文件中去
找到yaf.so文件
``/usr/local/Cellar/php71-yaf/3.0.4/yaf.so``

找到php.ini文件
如果这里需要查找phpinfo相关信息，找到本地php所加载的php.ini的路径
```
php -i
Loaded Configuration File => /etc/php.ini
```
打开``/etc/php.ini``文件
添加
```
extension=/usr/local/Cellar/php71-yaf/3.0.4/yaf.so
```
重启php
```
ps aux | grep 'php-fpm'

kill pid （就杀了所有进程，其中一个就可以）

php-fpm

```

ok！yaf安装完成！！！
