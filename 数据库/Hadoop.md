# Hadoop

# 概述

开源的分布式计算框架, 整个<u>大数据生态系统</u>的基础, 包含多个核心组件, 提供分布式存储和分布式计算能力

## 核心组件

- HDFS: 存储层, Hadoop的分布式文件系统, 存储海量数据, 数据被分割成多个block, 存储在集群节点上, 提供副本保证可靠, 本身无法查询管理数据, 没有内置索引机制, 甚至不了解文件中的具体数据结构
- YARN: 计算资源管理层, Hadoop的资源管理和作业调度框架, 管理集群中的计算资源并调度计算任务, Spark作业或Hive查询通过YARN请求资源, 然后在集群中分布式运行
- MapReduce: Hadoop的早期计算引擎, 提供基于`Map`​和`Reduce`​两个步骤的编程模型, 处理大规模数据, 主要用于批处理任务, 但因性能局限性已被替代
- Common: Hadoop的通用库, 为HDFS和YARN提供基础支持, 例如文件系统操作、配置管理等

## 上层应用/高级工具

- Hive: Hadoop生态上的数据仓库工具, 依赖HDFS和YARN, 提供基于SQL的接口(HiveQL), 用于查询存储在HDFS上的数据, 最初基于MapReduce执行查询, 后支持Spark、Tez等更高效的执行引擎
- Spark: Hadoop生态上的计算引擎, 更高效的分布式计算引擎, 支持批处理、流处理、机器学习, 可独立运行直接读取HDFS数据, 利用YARN资源, 也可作为Hive的执行引擎, 替代MapReduce

## **类比**

将整个 Hadoop 生态系统类比为一个大型的工厂:

|**组件**|**角色**|**描述**|
| --| --------------------| ---------------------------------------------------------|
|**HDFS**|仓库|存储所有原材料(数据)|
|**YARN**|调度中心|分配生产资源(CPU、内存), 确保生产顺利进行|
|**Hive**|订单处理和查询工具|提供SQL接口, 将订单(查询请求)翻译为工厂的生产计划(任务)|
|**Spark**|高效的自动化生产线|执行复杂任务(计算、分析), 以更快速度处理数据|
|**MapReduce**|传统的生产线|处理任务效率较低, 已逐渐被 Spark 等更快的工具取代|

## 注意

- 频繁启动容易导致hadoop进入安全模式, 变成只读状态, 解决:

  ```python
  hdfs dfsadmin -safemode leave 
  hdfs dfsadmin -savemode get 		# 显示OFF即可
  ```
- 使用命令停止集群后再关机
- 可以通过快照回复环境

‍

# Hadoop操作

- 一般命令

  - 启动: `start-all.sh`​
  - 关闭: `stop-all.sh`​
- hdfs集群

  - 启动: `start-dfs.sh`​
  - 关闭: `stop-dfs.sh`​
- yarn集群

  - 启动: `start-yarn.sh`​
  - 关闭: `stop-yarn.sh`​
- 历史服务器(可不用)

  - 启动: `mr-jobhistory-daemon.sh start historyserver`​
  - 关闭: `mr-jobhistory-daemon.sh stop historyserver`​
- 打开web

  - hdfs: master: 9870

    - Hadoop3版本默认端口为8020(2版本为9000) 

      3版本web端口为9870(2版本web端口为50070)
  - yarn: master: 8088
  - 历史服务器: master: 19888

‍

---

‍

# HDFS操作

所有命令前缀为`hdfs dfs -`​或`hadoop -`​: 

- ​`hdfs dfs -put 本地路径 目标路径`​: 文件上传
- ​`hdfs dfs -text 文件`​: 将源文件输出为文本格式
- ​`hdfs dfs -ls`​
- ​`hdfs dfs -mv`​: 目标路径应使用绝对路径, 相对路径会有解析问题
- ​`hdfs dfs -chmod`​
- ​`hdfs dfs -rm`​

‍

---

‍

# Hive操作

## shell

- 开启: `hive`​, 单连接
- 远程连接, 启动hiveserver2: `hive2.sh`​, 可通过解开注释开启独立元数据: `vim $HIVE_HOME/conf/hive-site.xml`​
- 启动hive元数据和hiveserver2: `hive-all.sh start`​, 开启后hive对应的web端口为master: 10002
- 连接: 

  - 方式一: 客户端模式: 连接`hive`​, 退出`exit;`​
  - 方式二: 远程连接: 连接`beeline`​再输入`!connect jdbc:hive2://master:10000`​, 用户密码都是root, 退出: `!exit`​

## 步骤

- 将文件放入hdfs
- 用hive创建数据库
- 使用pycharm连接hive
- 创表
- 加载数据: `load data inpath 'hadoop的path' into table 表名;`​

## HQL

### 函数

- ​`count()`​: `count(*)`​包含null, `count(col)`​不含null
- ​`coalesce()`​: 返回第一个非空值
- ​`nvl()`​: 两参数时, 等价于ifnull, 1参为null则返回2参, 否则返回1参

  - 三参数时, 1参为null则返回3参, 否则返回2参
- ​`unix_timestamp()`​: 当前时间戳, 时间序列号
- ​`current_date()`​: 当前日期, yyyy-MM-dd
- ​`current_timestamp()`​: 当前日期时间, yyyy-MM-dd hh:mm:ss
- ​`from_unixtime(unix_timestamp())`​: 当前时间戳转为yyyy-MM-dd hh:mm:ss格式
- 字符串转换时间格式: 

  ```sql
  from_unixtime(unix_timestamp('2020/02/02 02:02:02', 'yyyy/MM/dd hh:mm:SS'))
  from_unixtime(unix_timestamp('20200202020202', 'yyyyMMddhhmmSS'))
  ```
- ​`date_format(current_timestamp(), 'yyyy/MM/dd')`​: 日期转换字符串
- ​`select concat_ws(',', collect_list(colname)) ...`​: 没有`group_concat()`​, 用`concat_ws()`​和`collect_list()`​结合替代, 可自定义分隔符

### DDL

创建db的路径为/user/hive/warehouse

#### 库操作

- 创: 

  ```sql
  create datebase if not exists name;

  -- 指定存储路径
  create database name location 'path';
  ```
- 查: 

  ```sql
  desc database [extended] name;
  -- 显示基本信息
  -- extended显示详细信息
  ```
- 改: 

  ```sql
  alter database name set dbproperties('createtime'='...');
  ```
- 删: 

  ```sql
  -- 库中无表可直接删除
  drop database name;

  -- 库中有表
  drop database name cascade;	
  ```

#### 表操作

- 创: 

  - 内部表: 直接创表默认为内部表

    ```sql
    create table name(
    	字段 类型			-- 注意字符串类型为string, 没有char/varchar, 创表的列数必须和数据对应
    )row format delimited fields terminated by ','  -- 规定分隔符
    tblproperties('skip.header.line.count'='1') 	-- 跳过字段名(第一行)
    stored as textfile 		-- 指定存储格式
    location 'path';		-- 指定存储路径

    -- 通过select语句建表
    create table name1 as select * from name2;

    -- 通过现有表结构建表, 只复制表结构, 无数据
    create table name1 like name2;
    ```
  - 外部表: 

    ```sql
    create external table if not exists 表名();
    ```
- 查: 

  ```sql
  -- 显示属性信息
  desc [formatted] name;
  ```
- 改: 

  ```sql
  -- 重命名
  alter table name rename to newname;

  -- 增字段
  alter table name add columns(colname coltype, colname2 coltype);

  -- 改属性
  alter table name change column colname newcolname newtype;
  ```
- 删: 

  ```sql
  drop table name;
  -- 删除内部表, 元数据和表数据(文件)都会删除
  -- 删除外部表, 只删除元数据, 保留表数据(文件)

  truncate table name;
  -- 清空数据, 删除数据文件, 保留表结构
  -- 无法恢复, 谨慎使用
  ```
- 分区表: 根据指定字段值分文件夹存储数据, 每个分区为一个文件夹

  - 配置项: 

    - 设置启用动态分区: `set hive.exec.dynamic.partition=True;`​, 插入数据时可根据数据值自动创建新的分区
    - 设置查询模式为非严格: `set hive.exec.dynamic.partition.mode=nonstric;`​

      - 默认严格模式, 有错误/不规范则抛异常并终止查询
      - 非严格可进行隐式转换、允许不存在的字段、未声明的别名、join使用不等值连接等
    - 设置最大分区总数: `set hive.exec.max.dynamic.partitions=n;`​
    - 设置单个MR Job允许创建分区的最大数量: `set hive.exec.max.dynamic.partitions.pernode=n;`​
    - 设置自动分配运算内存: `set hive.exec.mode.local.auto=true;`​
  - 创: 

    ```sql
    create table name(
    	...							-- 注意分区中定义的名称不能和表中的列名相同
    ) partitioned by (col1 string)  -- 此处的col1和col2从创表字段中抽离, 不重复
    row format...

    -- 指定分区加载数据
    load data inpath ... into table name partition (col1=value)
    ```
  - 查: `show partition name;`​, 查看分区
  - 改: `alter table name add partition(col=) [partition(col=)]`​, 添加分区, 可添加多个
  - 删: `alter table name drop partition(col=)`​, 删除分区
- 分桶表: 分文件存储, 字段<u>哈希值</u>相同的分到一个文件中

  - 配置项

    - 开启hive的桶表功能: `set hive.enforce.bucketing=true;`​
    - 设置reduce的个数: `set mapreduce.job.reduces=n;`​
  - 创: `create table name() clustered by (col) into n buckets;`​, 此处的col3、col4不用抽离, 可重复

    - 不能直接加载数据(不会分桶), 需要先创普通表接受数据, 用insert overwrite加载时指定分桶的列: 

      ```sql
      -- 分桶表name_buc, 普通表name:
      load data ... into table name; 
      insert overwrite table name_buc select * from name cluster by (col);
      ```

#### 视图

隐藏复杂操作过程, 简化查询, 不存储数据, 一种快捷方式

- 创建: `create view name as select ...`​
- 重新定义: `alter view name as  select ...`​
- 更改视图属性: `alter view name  set tblproperties('comment'='...');`​
- 删除视图(不会删除原表数据): `drop view name;`​

### DML

不支持update和delete

- 加载数据

  ```sql
  insert into/overwrite table name [partition(col=)] values()/select * from name2;
  -- insert overwrite覆写更新, insert into追加更新
  -- [partition]可指定分区
  -- select可通过select插入

  load data [local] inpath 'path' [overwrite] into table name;
  -- [local]从本地加载(文件复制到hdfs路径下), 无local为从hdfs加载(文件移动到hdfs路径下)
  -- overwrite覆写, 无则为追加
  ```

  - 创表时指定位置(外部表): `create external table name() localtion 'path'`​
  - 导入导出(内部表): 

    - 导出到hdfs路径: `export table name to 'path'`​
    - 从hdfs导入表数据(需先创好表): `import table name from 'path'`​

      - import导入的数据必须是export导出的数据, 目录下必须要有元数据(\_metadata)和数据文件(data)
- 表数据导出

  - insert导出

    - 到本地: 

      ```sql
      insert overwrite local directory 'path' 
      [row format delimited fields terminated by '\t']  	-- []为是否格式化 
      select * from name;
      ```
    - 到hdfs:

      ```sql
      insert overwrite directory 'path' 		-- 无local
      row format delimited fields terminated by '\t' 
      select * from name;
      ```

      ‍
  - Hadoop shell命令导出到本地: `hadoop fs -get hdfs_path local_path`​
  - Hive shell命令导出到本地: `hive -e'select * from name;' > local_path`​

### DQL

- 单表查询:

  ```sql
  select [all|distinct] col1, col2...  
    from name 
  [连接方式 join name on 连接条件] 
  [where 分组前筛选] 			-- where子句不能写聚合函数
  [group by 分组字段 [having 分组后筛选]] 
  [[cluster by 分区及局部排序字段] ｜ distribute by 分区字段 [sort by | order by]]  -- cluster和distribute二选一
  [limit 限制行数];									-- sort by分区排序和order by全部排序(耗时长)二选一
  ```

  - 若无分区/分桶则sort/order结果一致

  - distribute by + sort by, 以某字段分区并按某字段局部排序, 可为同一字段(等同于cluster by)
  - cluster by, 具有distribute by分区功能, 也具有sort by局部排序功能, 不能指定排序规则, 只能升序

  - null不能做比较:

    - ​`where age <> 18`​: 不含null
    - ​`where age <> 18 or age is null`​
- 多表查询: join连接

  - 设置关闭自动mapjoin(数据集很少时可能抛出异常): `set hive.auto.convert.join=false`​
  - ​`[inner] join on`​、`left join on`​、`right join on`​、`full join on`​、逗号连接
  - ​`left semi join on`​: 左半连接, 显示能匹配到右表的左表结果, 只显示左表的列
- 抽样

  - 随机抽样: 

    - 设置reduce个数: `set mapreduce.job.reduces=n;`​
    - 查询reduce个数: `set mapreduce.job.reduces;`​
    - 随机分区: `select * from 库.表 distribute by rand() sort by ... limit n;`​
  - 块抽样: 

    - ​`tablesample(n percent)`​: 按比例抽样

      - eg: select \* from name tablesample(10 percent)
    - ​`tablesample(nM)`​: 按大小抽样, 单位为字节, 中间没有空格
    - ​`tablesample(n rows)`​: 按行抽样
  - 分桶抽样: ​`tablesample(bucket x out of y [on colname])`​

    - x: 抽取第几组
    - y: 一个数据分成几组
    - eg: select \* from name tablesample(bucket 1 out of 10 on rand( ))

### DCL

- grant
- revoke

‍

# Spark操作

## shell

- 先启动hadoop集群
- 启动spark集群: 

  - 启动: `start-spark.sh`​
  - 关闭: `stop-spark.sh`​
- jupyter notebook:

  - 启动: 终端输入`jupyter notebook`​
  - 关闭: 终端连按两次`ctrl+c`​
- 运行:

  - 方式一: pyspark: `pyspark --master spark://master:7077`​, 不加参数就是运行在本地
  - 方式二: scala: `spark-shell --master spark://master:7077`​, 不加参数就是运行在本地
- web页面:

  - spark:master: 7070
  - jupyter notebook: master: 8888

## 基本语法

- 导包:

  ```python
  import pyspark
  # 或
  from pyspark.sql import SparkSession
  ```
- 配置项:

  ```python
  spark = SparkSession\
  		.builder\
  		.appName('demo')\
  		.master('local[*]')\
  		.enableHiveSupport()\
  		.getOrCreate()
  sc = spark.sparkContext
  ```
- 一般语句:

  ```python
  spark.sql('HQL语句').show() 		# 有输出的一般加.show(), 无输出则不加
  ```
- 转dataframe: `.toPandas()`​
- 读取数据: `spark.read.csv('path')`​

## RDD

可分布式、弹性、不可变数据集:

- 不可变: RDD的操作通过创建新RDD实现, 不能直接修改原数据
- 弹性: 有容错性, 可通过操作历史在故障时自动恢复数据
- 分布式: 可分布在集群的多个结点上进行并行处理

### 基本操作

- 导包: `from pyspark import SparkConf, SparkContext`​, 配置项和上下文管理器
- 配置: 

  ```python
  conf = SparkConf().setAppName('test').setMaster('local[*]')
  sc = SparkContext(conf=conf)  	# sc用完要关闭: sc.stop()
  ```
- 创: 

  ```python
  rdd1 = sc.textFile()
  # path = '/root/……/*.txt'或'hdfs://localhost:9000/warehouse/……/*.txt' 本地或hdfs系统
  # n: 最小分区数量

  rdd2 = sc.parallelize()
  # c: 数据集
  # numSlices: 分区数量

  rdd3 = df1.rdd
  # 由df转为rdd
  ```
- 持久化: `.persist()`​, 可指定持久性级别和存储级别

  ```python
  # 需导包
  from pyspark.storagelevel import StorageLevel
  # eg:
  r1.persist(StorageLevel.MEMORY_ONLY)
  ```

  - 三种存储级别:

    - MEMORY_ONLY
    - DISK_ONLY
    - MEMORY_AND_DISK: 可有后缀:

      - 先尝试缓存到内存中, 空间不足则溢出到磁盘
      - 无后缀则只有一个副本
      - MEMORY_AND_DISK_2: 表两个副本, 容错性好
      - MEMORY_AND_DISK_2_2: 两个副本, 且均以序列化形式存储
- ​`.cache()`​: 持久化的特例(默认级别), 指定缓存到内存
- ​`.unpersist()`​: 立即释放内存
- 共享变量: 参与运算时可有广播机制

  ```python
  b = sc.broadcast(value)

  rdd1.map(lambda x: b.value*x).collect()
  ```
- 保存: `.saveAsTextFile('path')`​

### 分区

- ​`.getNumPartitions()`​: 获取分区的数量
- ​`.glom().collect()`​: 查看数据分区情况
- ​`.coalesce()`​: 重新分区, num: 新分区数, shuffle: bool, 是否打乱
- ​`.repartition()`​: 重新分区, num: 新分区数
- ​`.partitionBy()`​: 把原数据分到n个区中, num: 分区数, partitionfunc: 分区函数
- ​`.mapPartitions()`​: 遍历聚合, 遍历的是分区, 不是元素, func: 函数
- ​`.mapPartitionsWithIndex()`​: 展示数据时增加一列索引列, 从0开始
- ​`.repartitionAndSortWithinPartitions()`​: 重新分区并排序, num: 分区数, partitionfunc: 可用lambda, ascending: bool, 是否升序
- ​`.foreachPartition()`​: 遍历的是分区, func: 函数
- ​`.aggregate()`​: 先分区内聚合, 再将分区聚合, zerovalue: 初始值, seq0p: 各分区并行进行操作, com0p: 对各分区的结果进行操作
- ​`.aggregateByKey()`​: 根据键分区聚合再聚合

### 算子

spark通用的操作概念, 代表一个操作函数

- 转换算子: 定义逻辑, '做什么'

  - 特点:

    - 惰性求值: 调用时, Spark不会立即执行, 而是记录操作逻辑, 构建一个DAG(有向无环图)
    - 每次调用都生成新的数据结构
    - 纯函数: 转换算子中使用的函数通常是纯函数, 没有副作用, 以确保并行计算的正确性
  - ​`.map()`​: 遍历映射转换, 可用lambda函数
  - ​`.flatmap()`​: 遍历映射降维, 类似map, 但返回值为一维
  - ​`.filter()`​: 过滤, 留下为true的值, 可用lambda函数, eg: `rdd1.filter(lambda x: x>5).collect()`​
  - ​​`.sample()`​: 抽样

    - ​`withReplacement`​: 是否有放回
    - ​`fraction`​: 抽样比例, 范围[0, 1]
    - ​`seed`​: 随机种子编号
  - ​`.distinct()`​: 去重
  - ​`.substract()`​: 差集, eg: `r1.substract(r2)`​
  - ​`.substractByKey()`​: 根据键求差集, 适用于二维及以上数据
  - ​`.union()`​: 简单合并, 不去重
  - ​`.intersection()`​: 交集
  - ​`.cartesian()`​: 笛卡尔积
  - ​​`.sortBy()`​: 根据键排序, 不能用于一维数据

    - keyfunc: 可用lambda函数
    - ascending\=: 是否升序
  - ​`.sortByKey()`​: 根据键排序

    - ascending\=True: 是否升序
    - num: 分区数量
    - keyfunc: 函数, 可用lambda
  - ​​`.reduceByKey()`​: 根据键聚合, 把键相同的值聚合

    - func: 函数, 可用lambda
    - num: 分区数
  - ​`.groupByKey()`​: 返回迭代器, 不能`.collect()`​, 需要用列表/字典生成式查看元素: `{t[0]: list(t[1]) for t in r}`​
  - ​`.zip()`​: 同python中zip()
  - ​`.zipWithIndex()`​: zip同时生成一列索引
  - ​`.join()`​: 相当于inner join, 键相同的匹配, 值聚合为元组
  - ​`.leftOuterJoin()`​: 左连接
  - ​`.rightOuterJoin()`​: 右连接
  - ​`.cogroup()`​: 全连接, 返回迭代器, 需要用生成式查看元素: `{i[0]: [list(j) for j in i[0]] for i in r}`​
  - ​`.foldByKey()`​: 根据键累加

    - zerovalue: 初始值
    - func
    - num: 分区数量
- 动作算子: 触发实际计算动作, 将RDD的计算逻辑应用到数据上, 返回结果到驱动程序, 或写入存储

  - 聚合: ​`.count()`​、`.max()`​、`.min()`​、`.stdev()`​、`.variance()`​、`.mean()`​、`.sampleVariance()`​、`.sampleStdev()`​
  - ​`.collect()`​: 查看所有元素
  - ​`.keys()`​: (用于二维及以上数据)键
  - ​`.values()`​: (用于二维及以上数据)值
  - ​`.take(n)`​: 指定显示前n行
  - ​`.takeSample()`​: 抽样

    - withReplacement\=True: 是否有放回
    - num\=: 样本容量
    - seed\=: 随机种子编号
  - ​​`.takeOrdered()`​: 抽样并排序, 先运行函数, 再排序, 再取值

    - num: 样本容量
    - key\=: 排序字段, 可用lambda
  - ​`.first()`​: 获取第一个元素
  - ​`.reduce()`​: 聚合遍历, 可用lambda
  - ​`.foreach()`​: 只有循环的功能, 不改变原数据, 不返回值, 参数为函数(可自定义), 常配合全局累加器, `accum = sc.accumulator(value=0)`​, value设置初始值
  - ​`.countByKey()`​: 根据键计数, 不能操作一维数据, 默认第一列为计数层
  - ​`.saveAsTextFile()`​: 'path'

## DF

- 导包: `from pyspark.sql import SparkSession`​
- 配置:

  ```python
  spark = SparkSession
  		.builder
  		.appName('test')
  		.master('local[*]')
  		.enabelHiveSupport()
  		.getOrCreate()

  sc = spark.sparkContext
  ```
- 创:

  - ​`rdd1.toDF()`​: 转换DF类型
  - ​`spark.createDataFrame()`​:

    - ​`data=[]`​: 数据
    - ​`schema=[]`​: 列名
  - 定义结构和数据类型:

    ```python
    from pyspark.sql.types import *
    data = []

    schema = StructType(fields=[   		# 可在定义时限定类型和空值允许
    	StructField('name', StringType(), nullable=False), 
    	StructField('name', StringType(), nullable=False), 
    	StructField('name', StringType(), nullable=False) 
    ])

    spark.createDataFrame(data, schema).show()
    ```
  - 从文件中读取: `spark.read.csv/text('path', header=, sep=',', encoding='')`​, 读取后默认为DF类型
  - 从hive获取数据: `spark.sql('select * from name').show()`​
  - 连接mysql:

    ```python
    url = 'jdbc:mysql://localhost:3306/demo'  

    df = spark.read.format('jdbc')\ 
    	.option('url', url)\ 
    	.option('dbtabel', 'userinfo')\ 
    	.option('user', 'root')\ 
    	.option('password', '123456')\  
    	.load()
    ```
- 查:

  - ​`.printSchema()`​: 查看结构信息
  - ​`.columns`​: 查看字段
  - ​`.dtypes`​: 查看类型
  - ​`.count()`​: 查看行数
  - ​`len(df.columns)`​: 查看列数
  - ​`.show()`​: 表方式预览
  - 行式预览: 

    - ​`.first()`​
    - ​`.head(n)`​
    - ​`.collect()`​
- 存: 

  - 保存为csv: 

    - ​`df.write.csv()`​: path、header=bool
    - ​`df.write.format('csv').option('header','True').save('path')`​
  - 保存为txt: `df.write.text('path')`​
  - 保存为json: `df.write.json('path')`​
  - 保存到Hive: `df.write.bucketBy('')`​, 需要建表语句: 

    ```python
    spark.sql('set hive.exec.dynamic.partition.mode = nonstrict') 
    spark.sql('set hive.exec.dynamic.partition = true') 
    df.write.format('Hive')\ 
    .mode('overwrite')\ 
    .option('header','True')\ 
    .option('delimiter',',')\
    .saveAsTable('test.userinfo')
    ```
  - 保存到mysql: 

    ```python
    url = 'jdbc:mysql://localhost:3306/demo' 
    df = df.write.format('jdbc')\ 
    	 .option('url',url)\ 
    	 .option('user','root')\ 
    	 .option('password','123456')\ 
    	 .saveAsTable('userinfo')

    from sqlalchemy import create_engine 
    engine = create_engine('mysql+pymysql://root:123456@localhost/demo?charset\=utf8')  	# utf8, 没有小横线
    df.toPandas().to_sql('tablename', engine, index=False)
    ```
- 类SQL操作: 

  - 投影

    - ​`df.select(col1, col2……).show(n)`​: 投影, 展示n行结果
    - ​`df.select(df[col1], df[col2], 'sex', 'age').show()`​: 这里的df[col1]可为计算列
    - ​`df.select('*').where/filter(df['col']=='……').limit(n).show()`​
    - ​`df.select(df.colRegex("'.*?_id'")).show()`​: 可用正则
    - ​`df[col1, col2,……].show()`​: 可为计算列
  - 新建列: `df.new = df.withColumn('tablename', expressions)`​, 没有广播机制
  - 删除列: `df.drop('tablename')`​
  - 重命名

    - 列: `df.withColumnRenamed('oldname', 'newname')`​
    - 表: `df.alias('newname')`​
  - 排序: `df.sort(df['col1'].desc(), df['col2'].asc()).show()`​
  - 替换: `df.replace(to_replace={'old':'new','…':'…'}, subset=['col']).show()`​
  - 删除空值: `df.dropna()`​
  - 填充空值: `df.fillna('填充值', subset=[ ])`​
  - 删除重复项: `df.dropDuplicates(subset=[ ]).show()`​
  - 转换字段类型: 

    ```python
    from pyspark.sql import types 				# IntegerType()、DateType()、StringType()
    from pyspark.sql import functions as f 		# f.substring('col', start_pos, num), 下标从1开始
    ```

    - 方法一: `df.select(f.col('colname').cast(types.IntergerType()))`​
    - 方法二: `df.select(df.colname.astype(types.IntegerType()))`​
  - 条件判断: 

    ```python
    df.select('col1','col2', f.when(exp, res).when(exp, res).otherwise(res).alias('newname')).show()
    ```
  - 转pandas的DataFrame: `df.toPandas()`​
  - 聚合计算: `df.agg({'age': 'max/mean/min……'})`​
  - 描述统计: `df.select('colname').describe().show()`​
  - 分位数: `df.stat.approxQuantile('col',[0, 0.25, 0.5, 0.75])`​
  - 协方差: `df.cov('col1', 'col2')`​
  - 相关性: `df.corr('col1', 'col2')`​
  - 集合运算

    - 差集: 

      ```python
      df1.exceptAll(df2).show() 	# 不去重
      df1.substract()				# 去重
      ```
    - 交集:

      ```python
      df1.intersectAll(df2).show() 	# 不去重
      df1.intersect(df2).show()		# 去重
      ```
    - 并集: 

      ```python
      df1.union(df2).show()		# 不去重
      df1.unionAll(df2).show()	# 不去重
      ```
  - 随机抽样: `df1.sample()`​

    - ​`withReplacement=False`​: 是否有放回
    - ​`fraction=0.4`​: 抽样比例
  - 表连接: `df1.join(df2, param)`​

    - ​`on='col'`​: 字段名不统一时`on=((df1['a']==df2['b'])&(df1['a']==df3['c'])`​, 使用&符号时必须注意优先级问题
    - ​`how=`​: inner(默认)、cross、outer、full、full\_outer、left、left\_outer、right、right\_outer、left\_semi、left\_anti
  - 分组: 

    ```python
    df.groupBy('col1', 'col2')

    df.groupBy('col1', 'col2').agg( 
    	f.count('col3').alias('newname'), 
    	f.max(……)…… 
    	f.expr('col4').alias() 
    ).show()
    ```
  - 透视: `df.groupBy().pivot().mean().show()`​
  - 窗口函数: 

    ```python
    from pyspark.sql import Window 
    from pyspark.sql import functions as f

    window = (Window.partitionBy(df['col1'])\  			# 将window对象定义在外面
    				.orderBy(df['col2'].desc())\ 
    				.rowsBetween(Window.unboundedPreceding, 
    							 Window.currentRow/Window.unboundedFollowing) 
    )

    df.select(f.row_number().over(window).alias('newname')).show()
    ```

## 运行词频统计

先启动hadoop集群: `start-all.sh`​

```python
rm word.txt
echo "hello hadoop" >> word.txt
echo "hello word" >> word.txt
hdfs dfs -mkdir /input
hdfs dfs -put word.txt /input
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount /input /output
hdfs dfs -cat /output/*
```

若重新运行, 需删除结果目录: 

1. ​`hdfs dfs -rm -r /output`​

2. 从第5步开始就可以
