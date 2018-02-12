# redis 使用
## 1、下载redis
进入[redis官网](https://redis.io/download)下载对应版本的redis
```
$ wget http://download.redis.io/releases/redis-4.0.8.tar.gz
$ tar xzf redis-4.0.8.tar.gz
$ cd redis-4.0.8
$ make
```
make完后 redis-2.8.17目录下会出现编译后的redis服务程序redis-server,还有用于测试的客户端程序redis-cli,两个程序位于安装目录 src 目录下：
运行redis服务
```
cd src
./redis-server
```
## 2、下载phpredis驱动
### 2.1 [下载地址](https://github.com/phpredis/phpredis/releases) 找到需要的phpredis版本下载
```
$ wget https://github.com/phpredis/phpredis/archive/4.0.0RC1.zip
$ unzip 4.0.0RC1.zip
$ cd phpredis                            # 进入 phpredis 目录
$ /usr/local/php/bin/phpize              # php安装后的路径
$ ./configure --with-php-config=/usr/local/php/bin/php-config
$ make && make install
```
### 2.2 配置
```
vi /usr/local/php/etc/php.ini
extension=redis.so
```
重启``php-fpm``
```
php -m //查看redis是否已经安装
```

## 连接到redis服务
```
<?php
    //连接本地的 Redis 服务
   $redis = new Redis();
   $redis->connect('127.0.0.1', 6379);
   echo "Connection to server sucessfully";
         //查看服务是否运行
   echo "Server is running: " . $redis->ping();
?>
```
执行脚本会输出：
```
Connection to server sucessfully
Server is running: PONG
```

## php-redis 命令
### new Redis()
    初始化redis
### $redis->connect('127.0.0.1', 6379, 1); 
    短链接，本地host，端口为6379，超过1秒放弃链接
### $redis->open('127.0.0.1', 6379, 1);  
    短链接(同上)
### $redis->pconnect('127.0.0.1', 6379, 1); 
    长链接，本地host，端口为6379，超过1秒放弃链接
### $redis->popen('127.0.0.1', 6379, 1);  
    长链接(同上)
### $redis->auth('password');  
    登录验证密码，返回【true | false】
### $redis->select(0);  
    选择redis库, 0~15 共16个库
### $redis->ping()
    连接成功返回PONG ,否则返回连接失败异常
### $redis->close(); 
     释放资源
### $redis->ttl('key');  
    查看失效时间[-1 | timestamps]
### $redis->persist('key');  
    移除失效时间[ 1 | 0]
### $redis->sort('key', [$array]);  
    返回或保存给定列表、集合、有序集合key中经过排序的元素，$array为参数limit等！【配合$array很强大】 [array|false]


### $redis->expire('key', 10);  
    设置失效时间[true | false]
###$redis->move('key', 15);  
    把当前库中的key移动到15库中[0|1]

### $redis->strlen('key');  
    获取当前key的长度
### $redis->append('key', 'string');  
    把string追加到key现有的value中[追加后的个数]
### $redis->incr('key');  
    自增1，如不存在key, 赋值为1(只对整数有效, 存储以10进制64位，redis中为str)[new_num | false]

### $redis->incrby('key', $num);  
    自增$num, 不存在为赋值, 值需为整数[new_num | false]
### $redis->decr('key');  
    自减1，[new_num | false]
### $redis->decrby('key', $num);  
    自减$num，[ new_num | false]
### $redis->setex('key', 10, 'value');  
    key=value，有效期为10秒[true]

### $redis->llen('key');  
    返回列表key的长度, 不存在key返回0， [ len | 0]

### $redis->scard('key');  
    返回集合key的基数(集合中元素的数量)。[num | 0]
### $redis->sMove('key1',  'key2',  'member');  
    移动，将member元素从key1集合移动到key2集合。[1 | 0]

### $redis->zcard('key');  
    返回集合key的基数(集合中元素的数量)。[num | 0]

### $redis->zcount('key', 0, -1);  
    返回有序集key中，score值在min和max之间(默认包括score值等于min或max)的成员。[num | 0]

### $redis->hexists('key', 'field');  
    查看hash中是否存在field, [1 | 0]
### $redis->hincrby('key', 'field', $int_num);  
    为哈希表key中的域field的值加上量(+|-)num, [new_num | false]
### $redis->hlen('key');  
    返回哈希表key中域的数量。[ num | 0]

### $redis->dbSize();  
    返回当前库中的key的个数
### $redis->flushAll();  
    清空整个redis[总true]
### $redis->flushDB();  
    清空当前redis库[总true]
### $redis->save();  
    同步??把数据存储到磁盘-dump.rdb[true]
### $redis->bgsave();  
    异步？？把数据存储到磁盘-dump.rdb[true]
### $redis->info();  
    查询当前redis的状态 [verson:2.4.5....]
### $redis->lastSave();  
    上次存储时间key的时间[timestamp]

### $redis->watch('key', 'keyn');  
    监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断 [true]
### $redis->unwatch('key', 'keyn');  
    取消监视一个(或多个) key [true]
### $redis->multi(Redis::MULTI);  
    开启事务，事务块内的多条命令会按照先后顺序被放进一个队列当中，最后由 EXEC 命令在一个原子时间内执行。
### $redis->multi(Redis::PIPELINE);  
    开启管道，事务块内的多条命令会按照先后顺序被放进一个队列当中，最后由 EXEC 命令在一个原子时间内执行。
### $redis->exec();  
    执行所有事务块内的命令，；【事务块内所有命令的返回值，按命令执行的先后顺序排列，当操作被打断时，返回空值 false】



/**
 * String
 */
### $redis->setOption(Redis::OPT_PREFIX, 'hf_');  
    设置表前缀为hf_
### $redis->set('key', 1);  
    设置key=aa value=1 [true]
### $redis->mset($arr);  
    设置一个或多个键值[true]
### $redis->setnx('key', 'value');  
    key=value, key存在返回false[|true]
### $redis->get('key');  
    获取key [value]
### $redis->mget($arr);  
    (string|arr), 返回所查询键的值
### $redis->del($key_arr);  
    (string|arr)删除key，支持数组批量删除【返回删除个数】
### $redis->delete($key_str, $key2, $key3);  
    删除keys, [del_num]
### $redis->getset('old_key', 'new_value');  
    先获得key的值，然后重新赋值, [old_value | false]



/**
 * Hash
 */
### $redis->hset('key', 'field', 'value');  
    增，改，将哈希表key中的域field的值设为value, 不存在创建, 存在就覆盖【1 | 0】
### $redis->hget('key', 'field');  
    查，取值【value|false】
### $redis->hmset('key', $arr);  
    $arr = array('one'=>1, 2, 3);        
    增，改，设置多值$arr为(索引|关联)数组, $arr[key]=field,  [ true ]
### $redis->hmget('key', $arr2);  
    $arr2 = array('one', 0, 1);
    查，获取指定下标的field，[$arr | false]
### $redis->hgetall('key');  
    查，返回哈希表key中的所有域和值。[当key不存在时，返回一个空表]
### $redis->hkeys('key');  
    查，返回哈希表key中的所有域。[当key不存在时，返回一个空表]
### $redis->hvals('key');  
    查，返回哈希表key中的所有值。[当key不存在时，返回一个空表]
### $redis->hdel('key', $arr2);  
    删，删除指定下标的field, 不存在的域将被忽略, [num | false]



/**
 * List
 */
### $redis->lpush('key', 'value');  
    增，只能将一个值value插入到列表key的表头，不存在就创建 [列表的长度 |false]
### $redis->rpush('key', 'value');  
    增，只能将一个值value插入到列表key的表尾 [列表的长度 |false]
### $redis->lInsert('key',  Redis::AFTER,  'value',  'new_value');  
    增，将值value插入到列表key当中，位于值value之前或之后。[new_len | false]
### $redis->lpushx('key', 'value');  
    增，只能将一个值value插入到列表key的表头，不存在不创建 [列表的长度 |false]
### $redis->rpushx('key', 'value');  
    增，只能将一个值value插入到列表key的表尾，不存在不创建 [列表的长度 |false]
### $redis->lpop('key');  
    删，移除并返回列表key的头元素, [被删元素 | false]
### $redis->rpop('key');  
    删，移除并返回列表key的尾元素, [被删元素 | false]
### $redis->lrem('key', 'value', 0);  
    删，根据参数count的值，移除列表中与参数value相等的元素count=(0|-n表头向尾|+n表尾向头移除n个value)  [被移除的数量 | 0]
### $redis->ltrim('key', start, end);  
    删，列表修剪，保留(start, end)之间的值 [true|false]
### $redis->lset('key', index, 'new_v');  
    改，从表头数，将列表key下标为第index的元素的值为new_v,  [true | false]
### $redis->lindex('key', index);  
    查，返回列表key中，下标为index的元素[value|false]
### $redis->lrange('key', 0, -1);  
    查，(start, stop|0, -1)返回列表key中指定区间内的元素，区间以偏移量start和stop指定。[array|false]


/**
 * Set
 */
### $redis->sadd('key', 'value1', 'value2', 'valuen');  
    增，改，将一个或多个member元素加入到集合key当中，已经存在于集合的member元素将被忽略。[insert_num]
### $redis->srem('key', 'value1', 'value2', 'valuen');  
    删，移除集合key中的一个或多个member元素，不存在的member元素会被忽略 [del_num | false]
### $redis->smembers('key');  
    查，返回集合key中的所有成员 [array | '']
### $redis->sismember('key', 'member');  
    判断member元素是否是集合key的成员 [1 | 0]
### $redis->spop('key');  
    删，移除并返回集合中的一个随机元素 [member | false]
### $redis->srandmember('key');  
    查，返回集合中的一个随机元素 [member | false]
### $redis->sinter('key1', 'key2', 'keyn');     
    查，返回所有给定集合的交集 [array | false]
### $redis->sunion('key1', 'key2', 'keyn');  
    查，返回所有给定集合的并集 [array | false]
### $redis->sdiff('key1', 'key2', 'keyn');  
    查，返回所有给定集合的差集 [array | false]



/**
 * Zset
 */
### $redis->zAdd('key', $score1, $member1, $scoreN, $memberN);  
    增，改，将一个或多个member元素及其score值加入到有序集key当中。[num | 0]
### $redis->zrem('key', 'member1', 'membern');  
    删，移除有序集key中的一个或多个成员，不存在的成员将被忽略。[del_num | 0]
### $redis->zscore('key', 'member');  
    查, 通过值反拿权 [num | null]
### $redis->zrange('key', $start, $stop);  
    查，通过(score从小到大)【排序名次范围】拿member值，返回有序集key中，【指定区间内】的成员 [array | null]
### $redis->zrevrange('key', $start, $stop);  
    查，通过(score从大到小)【排序名次范围】拿member值，返回有序集key中，【指定区间内】的成员 [array | null]
### $redis->zrangebyscore('key', $min, $max[, $config]);  
    查，通过scroe权范围拿member值，返回有序集key中，指定区间内的(从小到大排)成员[array | null]
### $redis->zrevrangebyscore('key', $max, $min[, $config]);  
    查，通过scroe权范围拿member值，返回有序集key中，指定区间内的(从大到小排)成员[array | null]
### $redis->zrank('key', 'member');  
    查，通过member值查(score从小到大)排名结果中的【member排序名次】[order | null]
### $redis->zrevrank('key', 'member');  
    查，通过member值查(score从大到小)排名结果中的【member排序名次】[order | null]
### $redis->ZINTERSTORE();  
    交集
### $redis->ZUNIONSTORE();  
    差集













