# Redis

# 概述

Remote dictionary server, 开源的基于内存的数据存储系统, 用于数据库缓存和消息队列等各种场景, NoSQL之一, 数据以键值对形式存储

MySQL基于硬盘IO, 速度慢, 不适应发展

## 三种使用方式

- CLI: 命令行界面

  - 安装: `brew install redis`​
  - 启动服务器: `redis-server`​, 关闭: ctrl+c
  - 启动客户端: `redis-cli`​

    - 支持中文: `redis-cli --raw`​, 以原始形式显示内容
    - 指定端口: `redis-cli -p 6380`​
- API: 应用程序接口
- GUI: 图形用户界面, 如Redis Insight

## 特点

- 性能极高
- 数据类型丰富, 单键值对最大支持512M大小
- 简单易用, 支持所有主流编程语言
- 支持数据持久化、主从赋值、哨兵模式等高可用特性
- 命令不区分大小写, 键名区分大小写

‍

# 数据结构

## 基本类型

- 字符串String: Redis默认使用二进制字符串存储数据

  - ​`set key value`​: 设置键值对
  - ​`get key`​: 查看键对应的值, 显示nil表示无, 空
  - ​`del key`​: 删除键对应的键值对
  - ​`exists key`​: 判断一个键是否存在, 结果为1则存在
  - 支持模式匹配: `*`​为所有, `*me`​为所有以me结尾的键
  - ​`keys *`​: 查找所有键, *为模式匹配
  - ​`clear`​: 清屏
  - ​`flushall`​: 删除所有键
  - ​`ttl key`​: time to live, 查看键的生存时间, 单位秒, -1表未设置, -2表已过期
  - ​`expire key seconds`​: 设置键的生存时间, 单位秒
  - ​`setex key seconds value`​: 设置带生存时间的键值对
  - ​`setnx key value`​: 当键不存在时在设置键值对, 存在则不做动作
- 列表List: key不存在时自动创建, 元素可重复

  - ​`lpush key element [element ...]`​: 从列表头部添加数据, 可一次添加多个内容, 空格隔开, 按从左到右顺序依次加入, 而不是作为整体添加到头部, 最后添加的元素出现在列表最前
  - ​`rpush key element [element ...]`​: 从列表尾部添加数据
  - ​`lpop key [count]`​: 从头部添加元素, 返回被删元素, 可一次删除多个, 参数count表要删的元素个数
  - ​`rpop key [count]`​: 从尾部删除元素
  - ​`lrange key start stop`​: 获取列表内容, 以0开始, 结尾可用-1表示
  - ​`llen key`​: 查看列表长度
  - ​`lrem key count value`​: 删除匹配值

    - count>0时, 从头向尾删除最多count个匹配的元素
    - count<0时, 从尾向头删除最多-count个匹配的元素
    - count=0时, 删除所有匹配元素
  - ​`ltrim key start stop`​: 删除列表中指定范围以外的元素, trim修剪
- 集合Set: 无序集合, 不可重复, 命令均以s开头

  - ​`sadd key member [member ...]`​: 向集合添加元素, 可添加多个, 返回1表示添加成功
  - ​`smembers key`​: 查看集合内容
  - ​`sismember key member`​: 判断元素是否在集合中, 返回1表在集合中
  - ​`srem key member [member ...]`​: 删除元素, 返回1表删除成功
  - ​`sinter key [key ...]`​: 交集
  - ​`sunion key [key ...]`​: 并集
  - ​`sdiff key [key ...]`​: 差集
- 有序集合SortedSet: 每个元素关联一个浮点类型的分数用于<u>升序</u>排序, 元素不可重复, 分数可重复, 分数重复则按字典序排, 命令均以z开头

  - ​`zadd key source member [source member ...]`​: 添加元素, 分数在前, 元素在后
  - ​`zrange key start stop [withscores]`​: 查看元素内容, 默认不显示分数, 加withscores显示分数
  - ​`zscore key member`​: 显示元素对应的分数
  - ​`zrank key member`​: 显示元素排名, 默认升序
  - ​`zrevrank key member`​: 降序显示元素排名
  - ​`zrem key member`​: 删除元素, 返回1表删除成功
- 哈希Hash: 字符类型的字段和值的映射表, 即键值对的集合, 适合存储对象, 命令均以h开头

  - ​`hset key field value [field value ...]`​: 添加键值对
  - ​`hget key field`​: 查看键对应的值
  - ​`hgetall key`​: 查看所有键值对
  - ​`hdel key field`​: 删除某键值对, 返回1则删除成功
  - ​`hexists key field`​: 查看是否存在键值对, 返回1则存在
  - ​`hkeys key`​: 查看所有键
  - ​`hlen key`​: 查看长度

## 高级数据类型

- 消息队列Stream

  - 发布订阅消息: 消息无法持久化, 无法记录历史消息

    - ​`subscribe name`​: 订阅频道, 会持续收到该频道发布的消息
    - ​`publish name content`​: 发布content
  - Stream解决发布订阅的局限, 命令均以x开头

    - ​`xadd key id field value [field value ...]`​: 返回消息ID,

      - ​`*`​表示自动生成一个消息ID, 保证递增
      - 不使用*时注意id格式: n-n格式, 第一个整数n表一个时间戳, 中间一个短横线, 后一个整数n表示一个序列号, 需保证id递增
    - ​`xlen key`​: 查看消息数量
    - ​`xrange key start end [count]`​: 显示Stream所有消息, 可用`xrange key - +`​表示所有消息
    - ​`xdel key id [id ...]`​: 删除指定id的消息
    - ​`xtrim key maxlen 0`​: 表示删除所有消息
    - ​`xread count n block m key i`​: 从key中一次读取n条消息, 若无消息, 则阻塞m毫秒

      - i:表示从第下标i的消息开始读取, 0为从头读取
      - 若i大于消息队列中最大id, 则阻塞m毫秒
      - i为$表示获取从现在开始以后的最新消息
    - ​`xgroup create key group_name id`​: 创建名为group_name的消费者组
    - ​`xinfo groups key`​: 查看key的消费者组相关信息
    - ​`xgroup createconsumer key group_name consumer_name`​: 给group_name创建一个消费者consumer_name
    - ​`xreadgroup group group_name consumer_name count n block m streams key >`​: 从名为key的Stream中读取n条消息, 无消息则阻塞m毫秒, >表示从这个消息中读取最新的消息
- 地理空间Geospatial: 存储地理位置信息的数据结构, 可计算操作, 命令均以geo开头

  - ​`geoadd key 经度longitude 纬度latitue member`​: 添加位置信息
  - ​`geopos key member [member ...]`​: 获取某位置的经纬度
  - ​`geodist key member1 member2 [M|KM|FT|MI]`​: 计算两个member的距离, 默认单位米, KM为改变单位为千米
  - ​`geosearch key frommember member byradius n KM`​: 返回距离指定member小于n千米的member, 也可以

    - fromlonlat给定经纬度
    - bybox: 矩形范围
  - ​`georadius`​ / `georadiusbymember`​: 功能同上
- HyperLogLog: 用于基数统计的算法, 牺牲一定精度, 占用内存小, 适合对精度要求不高且数据量大的统计工作, 命令均以pf开头

  - ​`pfadd key [element [element ...]]`​: 添加元素
  - ​`pfcount key`​: 查看基数
  - ​`pfmerge destkey sourcekey [sourcekey ...]`​: 合并sourcekey到destkey中
- 位图Bitmap: 字符串类型的扩展, 可用String类型来模拟Bit数组, 命令均以bit开头, 值只有0和1, 下标即偏移量

  - ​`setbit key offset value`​: 设置某个位的值
  - ​`getbit key offset`​: 获取某位的值
  - 用String来模拟: `set key "\xF0"`​: 直接设置了16位, 不用一个位一个位地设置, 注意0为高位
  - ​`bitcount key`​: 统计1的数量
  - ​`bitpos key bit [start [end [BYTE|BIT]]]`​: 查看第一个0或1出现的位置, 可指定范围
- 位域Bitfield: 可将多个小整数存储到一个较大的位图中, 更高效地使用内存

  - ​`bitfield key set u8 #0 1`​: 设置key, u为无符号整数, 8位, 第0位设置为1
  - ​`bitfield key get u8 #0`​: 获取key的第0位
  - ​`bitfield key incrby u32 #1 100`​: 第1位增加100

# 事务

在一次请求中执行多个命令

两个命令配合: 

- multi: 开启一个事务, 开启后命令提示符显示(TX)表示已进入事务模式, 所有命令被放入到一个缓存队列中, 不会立即执行, 直到exec
- exec: 执行所有命令, 若有失败, 其他命令<u>依然执行</u>

执行过程中, 其他客户端提交的命令请求不会插入到事务的执行命令序列中

‍

incr k: 将k自增1

‍

# 持久化

两种方式:

- RDB: Redis Database, 以指定时间间隔将数据快照写入磁盘, 是某一时间点数据的完整副本, 适合用来做备份

  - 可通过配置文件的save参数来配置自动保存快照: 

    ​`save <seconds> <changes> [<seconds> <changes> ...]`​如save 3600 1表示3600秒内有1次改动就保存快照, save 60 10000表示60秒内有10000次改动才保存快照
  - ​`save`​: 手动触发快照命令, 但是
  - bgsave: 后台保存快照, 创建子进程负责将数据写入硬盘, redis不会阻塞, 主进程可继续处理请求, 但有性能损耗, 不能做到秒级快照
- AOF: Append Only File, 追加文件, 执行写命令时不仅将命令写入内存, 还将命令写入到一个追加的文件中, 以日志形式记录每个写操作, redis重启时通过重新执行AOF文件中的命令在内存中重建整个数据库的内容

  - 开启方式: 在配置文件中将参数`appendonly`​的值改为`yes`​

‍

# 主从复制

## 概念

将一个Redis服务器(主节点)的数据复制到其他Redis服务器(从节点): 主从一对多关系, 复制为单向操作, 由主到从, 一般主负责写操作, 从节点负责读操作

主节点数据变化自动同步到从节点上: 主节点将自己的数据变化通过异步的方式发送给从节点, 从节点收到后更新自己的数据

## 配置方式

主节点不需配置

- 命令行命令: 

  - ​`role`​: 查看当前节点角色
  - ​`replicaof host port`​或`slaveof host port`​(旧版): 指定主节点的ip和端口
- 通过配置文件修改: redis.conf, 一般在redis的安装目录下

  ```shell
  cd /opt/homebrew/etc
  cp redis.conf ~ 	# 复制到根目录一份
  cd 
  cp redis.conf redis-6380.conf # 再复制一份作为从节点的配置文件, 6380为从节点的端口号
  ```

  修改几个关键位置: 

  - port改为6380
  - pidfile加上`_6380`​: redis_6380.pid
  - dbfilename加上`-6380`​: dump-6380.rdb
  - replicaof <masterip> <masterport>, ip改为127.0.0.1, port改为6379

  启动从节点: 

  ```shell
  redis-server redis-6380.conf
  ```

  开启新客户端: 

  ```shell
  redis-cli -p 6380
  ```

  可用redis命令`info replication`​来查看详细信息

‍

# 哨兵模式

主从复制无法实现自动故障转移, 主节点宕机后仍需手动把从节点提升为主节点

哨兵以一个独立进程运行在Redis集群中, 监控各个节点是否正常运行

‍

功能:

- 监控: 通过不断发送命令, 检查各节点是否正常
- 通知: 某节点出问题后, 哨兵通过发布订阅模式通知其他节点
- 自动故障转移: 主节点故障时, 将一个从节点升级为新的主节点, 并将其他从节点指向新的主节点

‍

配置: 新建sentinel.conf文件, 添加如下配置: 

```shell
sentinel monitor master 127.0.0.1 6379 1
# master为要监控的主节点的名称, 可自定义
# 后加主节点的ip地址和端口号
# 1表示只需要一个哨兵节点同意就可故障转移
```

启动哨兵节点: `redis-sentinel sentinel.conf`​

‍

哨兵自己也是一个进程, 也有单节点故障问题, 实际生产环境一般使用三个哨兵节点保证高可用, 三个哨兵选举产生一个领导者, 由领导者监控其他节点, 领导者故障时, 其他哨兵重新选举出一个领导者
