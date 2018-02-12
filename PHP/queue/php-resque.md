# php-resque    

-  Job  任务 ： 一个Job就是一个需要在后台完成的任务，比如本文举例的发送邮件，就可以抽象为一个Job。在Resque中一个Job就是一个Class。    
-  Queue  队列 ： 也就是上文的消息队列，在Resque中，队列则是由Redis实现的。Resque还提供了一个简单的队列管理器，可以实现将Job插入/取出队列等功能。   
-  Worker  执行者 ： 负责从队列中取出Job并执行，可以以守护进程的方式运行在后台。

那么基于这个划分，一个后台任务在Resque下的基本流程是这样的：

1、将一个后台任务编写为一个独立的Class，这个Class就是一个Job。  
2、在需要使用后台程序的地方，系统将Job Class的名称以及所需参数放入队列。   
3、以命令行方式开启一个Worker，并通过参数指定Worker所需要处理的队列。   
4、Worker作为守护进程运行，并且定时检查队列。  
5、当队列中有Job时，Worker取出Job并运行，即实例化Job Class并执行Class中的方法。   

## 安装
### 1、安装redis
```
apt-get install redis-server
```
### 2、安装Composer
```
apt-get install curl
cd /usr/local/bin
curl -s http://getcomposer.org/installer | php
chmod a+x composer.phar
alias composer='/usr/local/bin/composer.phar'
```
### 3、使用Composer安装php-resque
假设web目录在/opt/htdocs
```
apt-get install git git-core
cd /opt/htdocs
git clone git://github.com/chrisboulton/php-resque.git
cd php-resque
composer install
```

## php-resque的使用
### 编写一个worker
```
class PHP_Job
{
    public function perform()
    {
        sleep(120);
        fwrite(STDOUT, 'Hello!');
    }
}
```
这个Job就是在120秒后向STDOUT输出字符Hello!

在Resque的设计中，一个Job必须存在一个perform方法，Worker则会自动运行这个方法。

### 将Job插入队列
php-resque也给出了最简单的插入队列实现 demo/queue.php：

```
if(empty($argv[1])) {
    die('Specify the name of a job to add. e.g, php queue.php PHP_Job');
}

require __DIR__ . '/init.php';
date_default_timezone_set('GMT');
Resque::setBackend('127.0.0.1:6379');

$args = array(
    'time' => time(),
    'array' => array(
        'test' => 'test',
    ),
);

$jobId = Resque::enqueue('default', $argv[1], $args, true);
echo "Queued job ".$jobId."\n\n";
```

在这个例子中，queue.php需要以cli方式运行，将cli接收到的第一个参数作为Job名称，插入名为'default'的队列，同时向屏幕输出刚才插入队列的Job Id。在终端输入：
```
php demo/queue.php PHP_Job
```

结果可以看到屏幕上输出：
```
Queued job b1f01038e5e833d24b46271a0e31f6d6
```
即Job已经添加成功。注意这里的Job名称与我们编写的Job Class名称保持一致：PHP_Job

## 查看Job运行情况
php-resque同样提供了查看Job运行状态的例子，直接运行：
```
php demo/check_status.php b1f01038e5e833d24b46271a0e31f6d6
```
可以看到输出为：
```
Tracking status of b1f01038e5e833d24b46271a0e31f6d6. Press [break] to stop. 
Status of b1f01038e5e833d24b46271a0e31f6d6 is: 1
```
我们刚才创建的Job状态为1。在Resque中，一个Job有以下4种状态：

* Resque_Job_Status::STATUS_WAITING = 1; (等待)
* Resque_Job_Status::STATUS_RUNNING = 2; (正在执行)
* Resque_Job_Status::STATUS_FAILED = 3; (失败)
* Resque_Job_Status::STATUS_COMPLETE = 4; (结束)
因为没有Worker运行，所以刚才创建的Job还是等待状态。

## 运行Worker
这次我们直接编写demo/resque.php：
```
<?php
date_default_timezone_set('GMT');
require 'job.php';
require '../bin/resque';
```

可以看到一个Worker至少需要两部分：

1、可以直接包含Job类文件，也可以使用php的自动加载机制，指定好Job Class所在路径并能实现自动加载
2、包含Resque的默认Worker： bin/resque
在终端中运行：
```
QUEUE=default php demo/resque.php
```
前面的QUEUE部分是设置环境变量，我们指定当前的Worker只负责处理default队列。也可以使用
```
QUEUE=* php demo/resque.php
```

来处理所有队列。

运行后输出为
```
    #!/usr/bin/env php
    *** Starting worker
```
用ps指令检查一下：
```
ps aux | grep resque
```
可以看到有一个php的守护进程已经在运行了
```
1000      4607  0.0  0.1  74816 11612 pts/3    S+   14:52   0:00 php demo/resque.php
```
再使用之前的检查Job指令
```
php demo/check_status.php b1f01038e5e833d24b46271a0e31f6d6
```
2分钟后可以看到
```
Status of b1f01038e5e833d24b46271a0e31f6d6 is: 4
```
任务已经运行完毕，同时屏幕上应该可以看到输出的``Hello!``

至此我们已经成功的完成了一个最简单的Resque实例的全部演示，更复杂的情况以及遗留的问题会在下一次的日志中说明。







