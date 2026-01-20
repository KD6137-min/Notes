# SQL

# 概述

- 概念:

  - DB: DataBase, 按照数据结构来组织、存储和管理数据的仓库, 长期存储在计算机内的、有组织的、可共享的、统一管理的大量数据的集合

    - 数据持久化: 将数据存到能长久保存数据的存储介质中, 掉电时也不会丢失

      - 普通文件/CSV文件
      - Excel工作簿/工作表
      - 数据库: 关系型、NoSQL
    - 有结构

      - 类型: 存放的数据有类型
      - 关系: 数据与数据之间的关系
    - 大量: 数据存储在磁盘的文件中
    - 共享: 可实现多应用程序间数据共享
  - DBA: database administrator 数据库系统管理员
  - DBMS: database management system 数据库管理系统(运行在操作系统之上), 系统安装后自带一个超级管理员root
  - MySQL: 数据库软件(文件系统), 以系统服务形式存在
  - DBS: database system 数据库系统管理员、数据库管理系统和数据库组成整个单元
  - 范式理论: 范式级别越高, 数据冗余越低, 数据操作异常越少

    - 1NF: 列不可分割(单值列)-- JSON类型违背1NF的设计
    - 2NF: 满足1NF + 非主键列必须完全依赖于主键列
    - 3NF: 满足2NF + 消除传递依赖
    - 4NF: 满足3NF + 消除非平凡多值依赖
  - 反范式: 违反范式理论, 增加冗余数据或违反范式规则来达到以下目标: 

    - 提高查询性能、简化数据操作、空间换取时间
  - 数据完整性

    - 实体完整性: 每条记录都是独一无二, 主键、唯一约束
    - 参照/引用完整性: 主表没有的数据, 从表也不能有, 外键
    - 域/领域完整性: 数据都是有效的, 数据类型、长度、非空约束、默认值约束、检查约束
  - 数据一致性

    - 事务: 若干个写数据的操作视为不可分割的原子性操作
    - 事务ACID特性

      - A: atomicity, 原子性, 要么全成功, 要么全失败
      - C: consistency, 一致性, 事务前后数据状态要一致
      - I: isolation, 隔离性, 多个事务不能看到彼此的中间状态, 若能, 称为读到脏数据
      - D: duration, 持久性, 事务提交后, 数据要完成持久化
    - 五种数据并发访问的问题

      - 第一类丢失更新: 两个事务同时相同数据, 如果一个事务提交, 另一个事务回滚, 第一个事务会被回滚
      - 第二类丢失更新: 多个事务相同的数据, 并完成各自的事务提交, 导致最后一个事务提交会覆盖前面所有事务对数据的改变
      - 脏读: 第二个事务查询到第一个事务未提交的更新数据, 第二个事务根据该数据执行, 但第一个事务回滚, 第二个事务操作脏数据
      - 不可重复读: 一个事务查询到了另一个事务已经修改的数据, 导致多次查询数据不同
      - 幻读: 一个事务查询到了另一个事务已经提交的新数据, 导致多次查询数据不同
    - 事务隔离级别

      - 使数据库自动选择适当的锁来保护数据
      - 查看事务隔离级别:

        ```sql
        show variables like 'transaction_isolation';
        ```
      - 修改事务隔离级别: 

        ```sql
        set [session|global] transaction isolation level [level];
        ```

        level: 

        - read uncommitted
        - read committed
        - repeatable read
        - serializable

          ![截屏2024-12-30 22.43.17](../assets/截屏2024-12-30%2022.43.17-20241230224320-18mo57a.png)
  - 建表必备的三个字段

    - xxx_id: 主键, 确保唯一性
    - xxx_created: 记录创建时间, 方便时间追踪
    - xxx_updated: 记录最后修改时间, 了解数据修改情况

- 分类

  - `RDB`​关系型数据库: relational database

    - 理论基础: 关系代数(集合论、一阶逻辑、关系运算)

      - 一阶逻辑`FOL`: First Order Logic

        - 个体: 具体的事物或抽象概念
        - 谓词: 表示个体性质或彼此关系
        - 量词: 表示数量的词, 全称量词∀, 存在量词∃
        - 连接符: 与、或、非、蕴含、双向蕴含
      - 关系运算: 选择、投影、笛卡尔积、并集、差集、重命名
    - 具体表象: 

      - 用二维表组织数据

        - 数据: data value
        - 行: 记录, record, 元组

          - 势: 行的数量
        - 列: 字段, field, 属性名, 标记这一列属于那个特证的信息

          - 度: 列的数量
        - 域: 取值范围
        - 关系键: 主键、外键
        - 实体(entity): 表, 关系
      - 用关系模型存储数据: 一对一、一对多、多对多

        - 实体关系图(ER图)

          - 实体: 矩形框, 表
          - 属性: 椭圆框, 字段
          - 关系: 菱形框
        - 数据库建模工具, 正向工程/反向工程

          - PowerDesigner、ER/Studio、Enterprise Architect、Visual Paradigm
    - 编程语言`SQL`: Structured Query Language, 结构化查询语言

      - 声明式编程语言, 不关注过程, 只关注结果
      - 强类型编程语言, 定义字段的时候必须指明数据类型
      - 1981年IBM推出, ANSI组织规范
      - 改进

        - MySQL: limit
        - SQLServer: top
        - Oracle: rownum
    - 应用

      - MySQL

        - MariaDB
        - Percona Server
      - Oracle、DB2、SQL Server、PostgreSQL、Greeplum、Teradata、snowflake、Redshift、Access、Sybase
      - 达梦数据库
  - `NoSQL`​非关系型数据库: Not only SQL

    - 采用**键值对**模型存储数据, 不记录数据间关系
    - 常见应用

      - 面向检索的列式存储: Column-Oriented

        - HaBase(Hadoop子系统)
        - BigTable(Google)
      - 面向高并发的缓存存储/键值存储: Key-Value(KV数据库)

        - 性能极好, 用于游戏开发、IoT应用、实时分析
        - Redis、DynamoDB、MemcacheDB
      - 面向海量数据访问的文档存储: Document-Oriented(文档数据库)

        - 价值低体量大, 扩展能力好, 用于内容管理、日志处理、实时协作同步
        - MongoDB、CouchDB、DynamoDB
      - 列族数据库

        - 大数据场景下提数性能好, 用于实时查询
        - Cassandra、HBase
      - 图数据库

        - 用于社交网络、推荐系统、流数据处理
        - Neo4j、MemGraph、Cypher
      - 时序数据库

        - 时间序列分析, 用于金融、监控
        - InfluxDB、Prometheus、DolphinDB
      - 搜索引擎

        - 倒序索引, 用于全文检索
        - ElasticSearch、Solr

‍

---

‍

# 数据类型

- 整数, `int[(M)] [unsigned] [zerofill]`​, unsigned: 表数据无符号, 不能做减法, zerofill: 零填充, 结合(M)数据宽度使用

  - `tinyint`: 微整型, 1B
  - `smallint`: 小整型, 2B
  - `mediumint`: 中等整型, 3B
  - `int`​/`integer`: 整型, 4B
  - `bigint`: 长整型, 8B
- 浮点, `float[(M, D)] [unsigned] [zerofill]`​, D为小数位数

  - float(已**不再使用**): 单精度, 4B
  - double(已**不再使用**): 双精度, 8B
  - `decimal`: 精度比float、double高
  - `numeric`: 同decimal
- 文本类型

  - 字符串(文本): **单引号**包含

    - `char[(M)]`: **固定长度**的字符串,

      - M表字符数, 范围0-255, 默认为1, 即使长度未到达M, 也按照M个长度进行存储, 空格右填充
    - `varchar(M)`: **可变长度**的字符串

      - M表最大字符数, 范围0-65535, 按长度分配存储
    - `tinytext`: 短字符串, 存储字符个数同char
    - `mediumtext`: 中等字符串
    - `text`: 长字符串
    - `longtext`: 极长字符串, 4GB
  - 字节串(二进制): binary、varbinary、longlob(极长字节串, 4GB)
- 时间类型

  - `date`: 日期, 格式`yyyy-mm-dd`​
  - `time`: 时间, 格式`HH: MM: SS`​
  - `datetime`: 日期和时间, 格式`yyyy-mm-dd  HH: MM: SS`​
  - `timestamp`​(不推荐使用): 时间戳, 指定时间到1970年1月1日凌晨的秒数

    - 底层为整数, 2038年1月19日溢出, 一般设置为bigint
- 其他类型

  - `boolean`: 布尔类型, 底层为tinyint
  - `set`​/`enum`: 方言, **不推荐使用**
  - `json`: 灵活, 但违反关系型数据库的范式理论, 可达到4G, 分为json数组、json对象
  - `null`: 空值, 不等于空字符串

    - 参与算术运算结果必为null
    - 参与逻辑运算, 三值逻辑, 结果为真、假、unknown

‍

---

‍

# 基本语法

## 规范

- 不区分大小写
- 每条语句以分号`;`​结束
- 关键字之间以空格分隔
- 不限制换行, 有空格的地方就可以换行
- 忽略空格

## 注释

- 单行注释: `-- 注释内容`​, 空格不可省
- 多行注释: ​`/* 多行注释内容 */`​
- 快捷键: command+/

## 变量

- 系统变量: `transaction_isolation / max_connections`​
- 用户变量: `@变量名`​, 自定义变量, 全局变量不加`@`​
- 局部变量: `declare 变量名 类型;`​

## 运算符

- 算术运算符: `+`​、`-`​、`*`​、`/`​、`mod`​、`pow`​
- 比较运算符: `=`​、`<>`​==不等于==、`>`​、`>=`​、`<`​、`<=`​
- 范围运算: `between A and B`​, 闭区间
- 逻辑运算符: `and`​、`or`​、`not`​、`xor`​, 优先级: and \> or
- 空值运算: `is null`​、`is not null`​, 因为三值问题, 不能用=或<>
- 赋值运算符: 

  - `set`​语句: 专门用于赋值, 不返回结果集

    ```sql
    set @x = (select……);
    set @x = 10;
    ```
  - `:=`: 海象运算符, 语句中定义变量同时赋值

    ```sql
    -- select可用于赋值, 返回结果集, 包含赋值后的值, 即10
    select @x := 10;
    ```
- 位运算符: `&`​、`|`​、`^`​、`~`​、`>>`​、`<<`​
- 成员运算符: 可用元组, 如记录中的多个值, `where (name, age) in (select * from t1)`​

  - `in (values...)`: 等价于  `=any(values...)`​
  - `not in`​
- 其他运算: 

  - `all`​、`any`​

    - `<> all(select)`​ 等价于`not in (select)`​
  - `exists`​、`not exists`: 

    - `where not exists (select 1/"x" from...)`​, select 1或"x"是看存不存在, 此处x必须用双引号
- 模糊查询: 

  - `like + 通配符`: 

    - `_`: 任一符号
    - `%`: 任意个符号, 不能匹配null
    - `\`: 转义符
  - regexp正则表达式: 

    - `where conditions regexp '\\bDIAB1.*'`​
    - 使用`\\`​转义特殊字符如`.`​

## 流程控制

- 分支语句

  - 标准sql: 

    ```sql
    case 字段
    	when 1 then '男'
    	else '女'
    end as 性别 
    -- 别名不能在then后加, 一般在end后加
    ```
  - 用于存储过程或函数中: 

    ```sql
    if ... then...;
    elseif... then...;
    else...;
    end if;
    ```
- 循环语句

  ```sql
  repeat ...;
  until done end repeat; -- 重复, 直到done为真
  ```

## CTE

common table expression, 公共表表达式

使用场景: 需在同一查询中多次引用相同的子查询的结果

可链式调用: 如同时定义了t1、t2、t3, 则t2中可以调用t1, t3可以调用t2和t1

```sql
with 临时表名(column ...) as (
	select ...
) 			-- with后无分号
select ... from 临时表名...;
```

## 视图

将一组查询指令构成的结果集组合成可查询的数据表的对象, 即虚拟的表, 将实体数据表隐藏, 可链接查询

- 创: `create view 视图名 as select...;`​
- 删: `drop view if exists 视图名;`​
- 优点: 

  - 实现数据的预处理
  - 隐藏真实的表结构
  - 将用户的权限限制到**列**
- 特性

  - 可使用insert、update、delete

    - 不能**更新**的视图类型: 

      - 使用了聚合函数、distinct、group by、having、union、union all
      - select包含了自查询
      - from包含了一个不能更新的视图
      - where的自查询引用了from表的视图
      - 强制更新: 先删后创: ​`create [or replace] view 视图名 as select...;`​
  - 视图本身不包含数据
  - 可嵌套, 可和表一起使用
  - 创建视图可使用order by, 若从视图中检索数据时使用了order by, 会覆盖原order by
  - 视图无法使用索引, 不会激发触发器
  - 必须唯一命名

## 封装函数

```sql
delimiter $$
create function fn_truncate_string(
    content varchar(10000),
    max_length int unsigned
) returns varchar(10000) no sql 	-- no sql声明未使用sql语句, reads sql data为使用sql语句
begin
    declare result varchar(10000) default content;
    if char_length(content) > max_length then
        set result = left(content, max_length);
        set result = concat(result, '……');
    end if;
    return result;
end $$
delimiter ;

-- 调用函数
select fu_truncate_string('和我在成都的街头走一走, 直到所有的灯都熄灭了也不停留', 10) as short_string;
```

## 过程

procedure, 存储过程, 封装一组常用的sql操作保存在**服务器端,**  不同于函数, 没有返回值

```sql
delimiter $$    -- 临时改变语句分隔符, 使过程可使用分号, 
drop procedure if exists 过程名; 
create procedure 过程名( 	-- 无参也要保留括号
	in     字段    类型,  	-- 要输入的字段
	out    字段    类型,  	-- 要输出的字段
	inout  字段    类型,  	-- 输入/出的字段
) 
begin 
	-- 声明局部变量
	declare flag boolean default 1; 
	-- 声明异常处理器
	declare continue handler for sqlexception set flag = 0; 
	start transaction; 
	update ...; 
	insert into ...; 
	if flag then commit; 
	else rollback; 
	end if; 
end $$ 
delimiter ;
```

- 调用: ​`call 过程名(params)`​
- 删除过程: `drop procedure if exists name;`​
- 显示过程创建语句: `show create procedure status;`​

## 数据库维护

- `analyse table 表名;`: 检查表键是否正确
- `check table 表名s...;`: 针对许多问题对表进行检查
- `optimize table 表名;`: 若从一个表中删除了大量数据, 应收回所用的空间以优化表的性能

## 字符集和校对顺序

- 概念: 

  - 字符集: 为字母和符号的集合
  - 编码: 为某个字符集成员的内部表示
  - 校对: 为规定字符如何比较的指令
- `show character set;`: 显示所有可用的字符集及每个字符集的描述和默认校对
- `show collation;`: 显示所支持校对的完整列表/查看所有排序规则

  - 区分大小写: \_cs, 不区分大小写: \_ci
- `show variables like 'character%' / 'collation%';`​
- 设置: 

  ```sql
  -- 库级别
  create database 库名 
  	charset utf8mb4 / default character set utf8mb4;
  
  -- 表级别, 一般均在表级别
  create table 表名() 
  	default character set 字符集名 
  	collate 校对顺序;
  
  -- 列级别, 可对每列单独设置
  create table 表名 (
  	字段名 类型 character set 字符集名 
  			  collate 校对顺序名
  );
  
  -- 子句级别: order by、group by、having、聚合函数、别名
  -- eg:
  select ... order by a, b collate 校对顺序名; 		-- 指定了一个备用的校对顺序
  ```
- 若只指定字符集, 则使用该字符集对应的默认校对
- 转换函数: ​`cast()`​、​`convert()`​

‍

---

‍

# 约束

- 默认值约束`default`: 默认参数, 若赋值则存新值

  ```sql
  create table if not exists 表名(
  	字段名 数据类型 default 值
  );
  ```
- 非空约束`not null`: 

  ```sql
  create table if not exists 表名(
  	字段名 数据类型 not null
  );
  ```
- 唯一约束`unique`: 允许出现空值null

  ```sql
  create table if not exists 表名(
  	字段名 数据类型 unique 
  );
  ```
- 检查约束`check`: check可写表达式

  ```sql
  create table if not exists 表名(
  	性别 varchar(5) check (性别='男' or 性别='女')  -- 或性别in('男', '女') 
  );											 -- 或enum('男', '女')
  ```
- 主键约束`primary key`: 结合`not null`​、`unique`​, 数据唯一标识

  - 每个表只允许一个主键列, 且必须被索引
  - 一般选择没有业务意义的自动编号/流水号当主键
  - 可为一个或多个字段(联合主键)

  ```sql
  create table if not exists 表名(
  	学号 int primary key
  );
  
  -- 联合主键
  create table if not exists 表名(
  	学号    int, 
  	课程号  int, 
  	成绩    float, 
  	primary key(学号, 课程号) 
  );
  ```
- 外键约束`foreign key`: 实际通常去掉外键避免性能开销, 不能跨引擎使用外键

  - 表和表的关系: 

    - 一对一: 属于多对一的特例, 给外键列添加唯一约束
    - 一对多: 主表、从表
    - 多对多: 需要中间联系表, 外键放在中间表中(类型要一致)
  - [on `delete`​/`update`​] + [`restrict`​/`cascade`​/`set null`​]: 可选, 定义约束行为, on delete和on update可同时出现

    - `restrict`: 默认, 检查子表是否有关联记录, 若有则父表不能删或改
    - `cascade`: 级联删除, 删除父表记录, 则子表中关联记录也被删除
    - `set null`: 删除父表记录, 则子表关联记录设为null, 若子表对应字段有非空约束则报错

    ```sql
    -- 写外键名为了删除方便
    alter table 表名 add constraint 外键名 foreign key(字段名) references 表名(字段名)
    
    create table users (
        user_id int primary key,
        username varchar(100)
    );
    
    create table orders (
        order_id int primary key,
        user_id int,
        foreign key (user_id) references users(user_id)
        on delete cascade
        on update set null
    );
    ```
- 自增长约束`auto_increment`: 结合**整型主键**, 实现主键自增长, 默认从1开始

  - 添加值时, 自增长字段可用default关键字代替

    - 若插入值大于现有最大值, 后续从插入值开始增长
    - 若插入值小于现有最大值, 报错

    ```sql
    create table if not exists 表名(
    	学号 int primary key auto_increment
    )[autu_increment=100];   -- 可自定义起始值
    ```
- 注解约束`comment`​

  ```sql
  create table if not exists 表名(
  	学号 int primary key comment  '这个是主键'
  );
  ```

‍

---

‍

# 函数

- `help 'functions';`: 查看内置函数信息
- 数值运算函数 numeric functions

  - `abs()`​/`ceil()`​/`floor()`​/`round()`​/`mod()`​/`sign()`​
  - `sin()`​/`cos()`​/`tan()`​...
  - `degrees()`​/`radians()`​
  - `exp()`​/`pow()`​/`log()`​/`log2()`​/`log10()`​/`sqrt()`​...
  - `rand()`: 0-1的随机数, 不包括1

    - [-100, 100)内随机数表示为rand( )\*200-100
  - `conv()`: 将一个数从一种进制转换成另一种进制
  - `crc32`: 计算循环冗余校验码
  - `truncate()`: 截断一个数到指定的精度
- 字符串函数 string functions

  - `length()`: 获取长度
  - `locate`: 查找子串位置
  - `char_length()`​
  - `repeat(str, 次数)`: 重复
  - `concat(str1, str2...)`: 字符串拼接
  - `group_concat()`: 将分组后的字符串拼接

    ```sql
    group_concat(distinct name order by name separator ',')
    ```
  - `format()`: 格式化
  - `to_base64()`: 以base-64形式编码

    - Base64: 基于64个可打印字符来表示二进制数据的编解码方式, 不在乎安全性, 在乎传输效率, 完整性

      - 组成: 大写字母A-Z、小写字母a-z、数字0-9、`+`​和`/`​
      - 3B24b为一组, 分四小份, 即每6bit前加两个0, 再根据索引转换为4个Base64编码字符(3变4, 体积增大三分之一)
      - 不够时用\=替代, 等号只能有不多于2个

      ![截屏2024-12-31 10.58.25](../assets/截屏2024-12-31%2010.58.25-20241231105828-dkjty1p.png)

      ![截屏2024-12-31 11.00.31](../assets/截屏2024-12-31%2011.00.31-20241231110034-bg8800u.png)
  - `from_base64()`: 解码to\_base64编码的字符串
  - `left(str, n)`: 取左边的n个字符
  - `right()`:
  - `substr`: MySQL使用, 同`substring`​
  - `substring(str, index, [len])`: 截取, 从1开始计数
  - `lpad(原字符, 长度, 填充字符)`: 在左侧填充, 直到长度足够
  - `rpad()`​
  - `trim()`: 去掉两端多余的空格

    - `trim([both|leading|trailing] substr from str)`:

      - both: 从两端去掉substr
      - leading: 从左侧去掉substr
      - trailing: 从右侧去掉substr
  - `ltrim()`​
  - `rtrim()`​
  - `replace(str, 旧, 新)`​
  - `instr()`​/`locate(查找字符, 原字符)`: 定位, 返回位置
  - `lower()`​/​`upper()`​
  - `bin()`​/`oct()`​/​`hex()`​
  - `coalesce(值1, 值2...)`: 返回参数列表中第一个非空值

    - 空值处理: `coalesce(字段, 0/指定值)`​
  - `ord()`​/`char()`: 返回字符对应的编码/返回编码对应的字符
  - `strcmp()`: 比较字符串, 返回-1、0、1分别表示小于、等于、大于
- 日期时间函数 date and time functions

  - 改变日期格式, 也用于聚合日期, 如将日聚合为月: `date_format(col, '%Y-%m')`​
  - 获取系统时间:`select + [options] + [from dual]`​

    - `current_date()`​
    - `current_time()`​
    - `current_timestamp()`​

      - 方言: `now()`​/curtime/cudate
  - 获取时间信息

    - `year()`​、`quarter()`​、`month()`​、`date()`​
    - `day()`​、​`hour()`​、​`minute()`​、​`second()`​
    - `weekday()`​
    - `weekofyear()`: 日期所在年份的第多少周
    - `dayofweek()`: 周日为1, 周一为2……, 周六为7
  - 计算时间差: 

    - `datediff(时间1, 时间2)`​, 返回天数, 时间1更大, 放前
    - `timediff()`: 返回天/小时
    - `timestampdiff(返回类型, 起始时间, 计算时间)`: 返回类型有year, month, day, hour, minute, second
    - `to_days()`: 返回该年第一天到给定日期的天数
    - `makedate()`​/`maketime()`: 制造一个日期/时间
    - 时间推导运算: 

      - `date_add()`​/`adddate()`​
      - `date_sub()`​/`subdate()`​
- json数据函数: 

  - json_unquote()
  - json_extract()
  - member of ()
  - json_contains(): 检查json数组是否包含指定的元素
  - json_overlaps(): 检查json数组是否与指定的数组有重叠部分

  ```sql
  insert into `tb_test` values 
      (1, '{"tel": "13122335566", "QQ": "654321", "wechat": "jackfrued"}'), -- 第二列为json对象
      (2, '{"tel": "13599876543", "weibo": "wangdachui123"}');
  
  select 
      `user_id`,
      json_unquote(json_extract(`login_info`, '$.tel')) as 手机号,
      json_unquote(json_extract(`login_info`, '$.wechat')) as 微信 
  from `tb_test`;
  
  -- 便捷写法
  select 
  	`user_id`,
      `login_info` ->> '$.tel' as 手机号,
      `login_info` ->> '$.wechat' as 微信
  from `tb_test`;
  ```
- 流程控制函数 flow control functions

  - `if()`​

    - `decode(布尔表达式, '真值', '假值')`: oracle方言
    - `if (布尔表达式/字段, '真值', '假值')`: mysql方言
  - `ifnull(字段, 替代值)`: 如果为NULL则返回指定的值否则就返回本身
  - `nullif()`: 两个表达式相等就返回NULL 否则返回第一个表达式的值, 常用于判0
- 聚合函数 aggregate functions and modifiers

  - 获取数据的描述性统计信息(集中趋势和离散趋势), 自动忽略空值
  - `min()`​、​`max()`​、​`avg()`​、​`sum()`​、
  - `count()`: `count(*)`​数行数
  - `var_pop()`​/`var_asmp()`: 方差(总体/样本)
  - `stddev_pop()`​/`stddev_samp()`: 标准差(总体/样本)
  - `json_arrayagg()`​ / `json_objectagg()`​
- 窗口函数: `函数(字段) + over + (窗口)`​

  - 与group by 的区别

    - `group by`​数据有几类, 结果有几行, 窗口函数每一行后产生一个结果

  - 函数

    - 聚合函数

      - `sum() over()`​
      - `avg() over()`​
      - `max() over()`​
      - `min() over()`​
      - `count() over()`​
    - 排名函数: 相当于双层遍历, 外层取值, 内层找比当前大的个数n, 排名就是n+1

      - `row_number() over()`: 没有并列
      - `rank() over()`: 有并列, 而且并列占用了下一个名次
      - `dense_rank() over()`: 有并列, 但是不占用下一个名次
      - `percent_rank() over()`​
    - 取数函数

      - `first_value() over()`: 返回分组内的第一个
      - `last_value() over()`: 返回分组内的最后一个
      - `nth_value() over()`: 返回分组内的第n个
      - `ntile(n) over()`: 分成n层, 按顺序打上几层的标签
    - 偏移函数

      - `lag() over()`: 往前n个
      - `lead() over()`: 往后n个
  - `over()`: 不可省略

    - over(partiton by 分组归类字段)
    - over(order by 排序字段): 窗口函数的order by强制对数据排序, 导致结果姐顺序改变
    - over(frame 子窗口): 首先要确保数据的顺序, 一定有order by, 写在order by后面

      - `rows/range between +`​ 以下三选二, and连接:

        - `unbounded | n`​ + `preceding | following`​
        - `current row`​, 可放在前面
        - `interval n`​ + `preceding | following`​

        > 有则选中, 无不会报错, 数据不连续但行连续时, 用rows会报错, 可用range
        >
  - `windows`​定义窗口: 实现窗口的复用

    ```sql
    select rank() over w as r1
    	 , dense_rank() over w as r2
    	 , row_number() over w as r3 
      from 表 
    window w as (partition by ... order by ...)
    ```
- 其他函数

  - 类型转换: `cast()`​、`convert()`​
  - 信息获取

    - `version()`: 返回当前数据库版本
    - `user()`​ / `current_user()`: 返回当前用户
    - `found_rows()`​ / `row_count()`: 返回查询到的行数/受影响的行数
    - `last_insert_id()`: 返回最后一个自增主键的值
    - `current_role()`​
    - `database()`: 返回当前数据库名
    - `MD5()`​ / `SHA1()`​ / `SHA2()`: 返回字符串对应的哈希摘要
    - `charset()`​ / `collation()`: 返回字符集/校对规则
  - 全局唯一标识符

    - `uuid()`​ / `uuid_to_bin()`: 可用作主键, 通过uuid函数生成全局唯一标识符, 再通过uuid\_to\_bin( )函数将其处理为二进制串
    - `bin_to_uuid()`​
- 用户自定义函数UDF: 跨库调用需要在函数前加`库名.`​

  ```sql
  delimiter $$  		-- 临时修改终止符(因为若函数中有if语句会使语句提前结束), 定义后必须改回分号
  create function 函数名 (
  	*args 类型
  ) returns 返回类型 [deterministic | no sql | read sql data] 
  begin 
  	declear 新变量名 类型 default 默认值;
  	函数体 
  end $$ 
  delimiter ;
  ```

  - `deterministic`: 声明函数为确定性函数, 传入相同的自变量一定会返回相同的因变量

    - MySQL会缓存函数的执行结果来避免重复运算, 提高函数的执行性能, 缓存装饰器`@lru_cache(maxsize)`​
  - `no sql`: 函数定义中不会调用sql语句
  - `read sql data`: 函数定义中有sql语句

  - Oracle语法: `create or replace...`​

‍

---

‍

# 索引

- 概念

  - 底层结构: InnoDB引擎下, 一个数据页16K, 索引底层结构为B+树, I/O性能极好

    - B+树: 基于磁盘的平衡多叉树, 通常只有3\~4层, 由根节点、中间节点、叶子节点构成, 排序后的数据保存在叶子节点上  
      ​![截屏2024-12-31 13.49.42](../assets/截屏2024-12-31%2013.49.42-20241231134945-muq36ej.png)
  - 聚集索引: 索引组织表, 数据就根据主键排序放在索引中(有且只有一个)
  - 非聚集索引: 二级索引, 额外创建一棵树来加速数据检索(可以创建多个)
  - 主键/外键/唯一约束自动设为索引键
  - 索引覆盖查询和回表

    - index covered query : 要投影的字段已经被索引覆盖到, 此时不需要回表, 性能非常好
    - 回表: 投影的字段没有被索引完全覆盖到, 需要重新通过主键定位到数据, 性能会受到影响
  - 执行计划

    - 生成执行计划: ​`explain select...`​
    - type: 查询类型, 以下性能降序: 

      - const: 常量级扫描
      - eq\_ref: 唯一索引扫描
      - ref: 非唯一索引扫描
      - range: 范围索引扫描
      - index: 索引全扫描
      - all: 全表扫描
    - possible\_keys: 可能用到的索引
    - key: 实际用到的索引
    - key\_len: 索引的长度
    - rows: 预估扫描的行数
    - extra: 额外的信息
  - 索引设计要点

    - 注意索引的最左匹配原则, 避免失效的情况
    - 在作为查询条件的字段上建索引(覆盖到where子句)
    - 避免回表: 让索引覆盖到查询要投影的列 - 复合索引
    - 索引不是越多越好: 索引加速了查询但是让增删改变得更慢  - 索引更新问题
    - 避免额外排序: 索引覆盖到order by子句的字段
    - 最适合索引的列是出现在WHERE子句和连接子句中的列
    - 索引列的基数越大(取值多、重复值少), 索引的效果就越好
- 创: ​`create index 索引名 on 表(字段s);`​

  - 前缀索引: 可减少索引占用空间, 内存中可缓存更多索引

    - `create index 索引名 on 表(字段(1));`: 对字段的第一个字符建索引
  - 复合索引: `create index 索引名 on 表(字段1, 字段2);`​, 遵从最左匹配原则:

    - 只给字段2时索引失效
    - 字段1和2互换位置不影响
    - 用or连接字段1和2, 索引失效
    - 可用模糊匹配(仅限给定开头)
- 删:

  - `drop index 索引名 on 表名;`​
  - `alter table 表名 drop index 索引名;`​
- 设置索引可见性: ​`alter table 表名 alter index 索引名 invisible/visible;`​

‍

---

‍

# 游标

一个select语句的结果集, 定义之后可按需滚动/浏览/更改, 主要用于交互式应用

- 创: `declare 游标名 cursor for select...`​

  - 必须放在过程中, 过程处理完成后游标即消失(局限于过程)
  - 定义游标时, 只定义要用的select语句, 但不执行select, 打开游标后才执行
  - declare语句次序: declare局部变量 \> declare游标 \> declare句柄
- 打开: `open 游标名;`​, 执行select查询
- 使用: fetch语句

  - `fetch 游标名 into 变量名;`: 存储到局部声明的变量中

    ```sql
    -- 局部变量常和游标一起声明
    begin 
    declare a int; 
    declare cursor_eg cursor for select ...;
    ```

    - 访问一行: 自动前移内部行指针, 下一次执行时, 自动访问下一行
    - 循环检索, 直到最后一行: 

      - 声明异常处理器

        ```sql
        declare continue handler for sqlstate'02000' set done = 1; -- 无后续行时done为1
        -- sqlstate'02000': '未找到'的错误代码, 即没有后续的行
        -- 自定义了一个布尔值, 当出现特定错误时, 设置为1
        ```
      - 循环语句

        ```sql
        repeat fetch 游标 into 变量;  
        until done end repeat;		-- 重复, 直到done为真
        ```
- 关: `close 游标名;`​, 若不明确关闭, 则会在过程的end语句自动关闭

‍

---

‍

# 触发器

让指定事件发生前后自动执行某些语句

- 响应delete、insert、update, 其他语句不支持触发器, 视图、临时表不支持触发器
- 按每个表每个事件每次地定义

  - 每个表每个事件每次只允许一个触发器
  - 每个表最多支持6个触发器(insert、update、delete的前和后)
  - 单一触发器不能与多个事件或多个表关联
- before用于数据验证和净化, 保证所插为所需, 若before触发器失败, 则不执行请求的操作, 若before出发器或语句本身失败, 则不执行after触发器
- 自动触发, 若语句本身能执行, 则其前后的触发器也能执行

- 有锁表的风险, 严重影响数据操作性能
- 分类

  - insert触发器: new虚拟表

    - insert触发器代码内可引用名为new的虚拟表访问被插入的行
    - before insert触发器中new可被更新
    - auto\_increment列, new在insert执行前包含0, 执行后包含新的自动生成值
  - delete触发器: old虚拟表

    - delete触发器代码内可引用名为old的虚拟表访问被删除的行
    - old的值全都是只读的, 不能更新
  - update触发器: 可访问old虚拟表的值(更新前) 可访问new虚拟表的值(更新后)

    - before update中, 允许更改new的值
    - old的值均为只读, 不能更新
- 创

  ```sql
  create trigger 触发器名  	-- 触发器名须唯一
  	before/after insert/update/delete on 表名  
  	for each row  
  [begin]  					-- 使用begin和end可容纳多条语句
  	select/insert/set ...
  [end];
  ```
- 删: `drop trigger 触发器名;`​

‍

---

‍

# 命令行

- 终端进入mysql:

  ```bash
  mysql -uroot -hlocalhost -P3306 -p密码
  # -u 用户名
  # -h host数据服务所在IP地址
  # -P 数据库服务对应的端口号, 默认为3306
  # -p 密码, 不显示在屏幕
  ```
- show

  - `show databases;`​
  - `show tables;`​
  - `show charset;`: 显示所有字符集
  - `show collation;`: 显示所有排序规则
  - `show engines;`: 显示存储引擎
  - `show binary logs;`: 显示所有日志文件
  - `show errors;`: 显示服务器错误信息
  - `show grant;`: 显示授予用户(所有用户或特定用户)的安全权限
  - `show status;`: 显示广泛的服务器状态信息
- `help`​/`?`: 获取帮助信息, `?show`​、`?contents`​、`?functions`​、`?data types`​
- 新建/重建服务器连接: `connect`​ / `resetconnection`​
- `\c`: 清空当前输入
- `delimiter 终止符`: 修改终止符(定界符)
- `edit`: 打开系统默认编辑器, 编辑完成保存关闭之后, 命令行会自动执行编辑的内容
- `status`: 查看服务器状态
- `prompt`: 修改默认提示符
- `system`: 执行系统命令, 可缩写为`\!`​
- `source xx.sql`: 执行SQL文件, 后面跟SQL文件路径
- `tee`​ / `notee`: 重定向输出, 可以将命令的输出重定向到指定的文件中
- `warnings`: 显示警告信息
- `quit`​ / `exit`: 退出命令行

‍

---

‍

# DDL

Data Definition Language, 数据定义语言

- 库

  - 创: 

    - `create database [if not exists 库名];`: 所有的database都可用schema替换
    - `create database 库名 charset utf8mb4;`​或`create database 库名 default character set utf8mb4;`: 指定字符集

      - 修改编码: `alter database 数据库名 charset utf8mb4;`​
      - utf8mb4比utf8编码可以识别更多的特殊符号
      - mysql5的编码为lanti1, 对应单字节编码的ISO-8859-1
      - mysql8的默认编码为utf8mb4
    - `create temporary table ...;`: 创建临时表
  - 查: 

    - `show databases;`: 显示当前mysql中的数据库列表
    - `show database 库名;`: 显示指定名称的数据库, 可查看数据库编码格式
  - 删: `drop database if exists 库名;`​
- 表

  - 创: 需要先解读实体的属性, 实体与实体的关系

    - 建议以tb\_开头, 起表名或字段名, 用`反引号`​包含时可解决名与关键字冲突问题

      ```sql
      create table if not exists 表名 (
      	字段名 数据类型 [约束] 
      	字段名1 数据类型 [约束]
      )
      [engine = InnoDB]		-- 引擎类型可以混用
      [default character set 字符集名 
      collate 校对顺序名];
      ```
  - 查: 

    - `use 库名;`: 切换到指定数据库上下文环境
    - `show tables;`: 查看当前库中所有表
    - `desc 表名;`: 查看表结构
    - `show index from 表名;`: 查看所有索引字段
    - `show columns from 表名;`​
  - 改: 

    - 添加字段:

      ```sql
      -- 默认, 加在最后
      alter table 表名 add [column] 字段名 类型 约束;	-- column可写可省	
      -- 加在开头
      alter table 表名 add 字段名 类型 约束 first;
      -- 加在指定字段后
      alter table 表名 add 字段名 类型 约束 after 指定字段名;
      ```
    - 删除字段:

      ```sql
      alter table 表名 drop [column] 字段名;		 -- column可写可省
      ```
    - 改字段类型:

      ```sql
      alter table 表名 modify 字段名 新类型;
      ```
    - 修改字段信息:

      ```sql
      alter table 表名 change (column) 字段名 新字段名 新类型 新约束; 	-- 不变的也要写出来
      ```
    - 重命名: 

      ```sql
      alter table 表名 rename to 新表名;
      -- 或
      rename table 
      	表名1 to 新表名1 
      	表名2 to 新表名2
      	...
      ;
      ```
    - 改约束: 

      - 增: 

        ```sql
        alter table 表名 add constraint 约束名 关键字...;
        
        alter table 表名 add primary key(字段名);
        
        alter table 表名 add unique (字段名); 	-- 自动添加为索引, 索引名与字段名一致
        
        alter table 表名 add constraint 字段名 foreign key(字段名) references 表名(字段名);
        ```
      - 删: 

        ```sql
        alter table 表名 drop contraint 约束名; 
        
        alter table 表名 drop primary key;
        
        alter table 表名 drop foreign key 字段名;
        
        alter table 表名 drop index 索引名; 	-- 此处用drop unique会报错
        ```
    - 增加计算列/虚拟列: 

      ```sql
      alter table 表名 add column 字段名 类型 as 运算表达式;
      ```
  - 删: 

    ```sql
    drop table (if exists) 表名;		-- 删除内容, 删除定义, 释放空间
    ```
  - 截取: `truncate 表名;`​, 删除内容, 释放空间, 保留定义(表结构)

    - 本质上是删除原表后按结构重建

    - 速度比delete from快, 因为直接删除全部行, 在日志中只记录页的释放

‍

---

‍

# DML

Data Manipulation Language, 数据操作/操纵语言

写操作不能违反关系型数据库对数据完整性的要求

- 增: 

  - ```sql
    insert into 表名 
    	[columns...]  	-- 若不指定字段, 则values添加数据个数、顺序要和表结构中字段一致
    values  			-- 字段和值一一对应
    	(values...)...; -- 没有指定的字段被赋予默认值, 无默认值则为null(该字段必须允许null值)
    ```
  - 批处理: 

    ```sql
    insert into 表名 		-- MySQL方言
    	[columns...]  		-- 若不指定字段名, 则values添加数据个数、顺序要和表结构中字段一致
    values  				-- 字段和值一一对应
    	(values...),  
    	(values...),
    	...;
    ```
  - 降低优先级: ​`insert low_priority into`​
  - insert select语句: 

    ```sql
    insert into 表1
    	[字段s] 			-- 这里的字段按照位置对应, 而不按照字段名对应
    select 字段s 
      from ...
    ```
- 改: 

  - 安全等级问题: 安全等级高时不允许修改, 需要降低安全等级参数, 0(低)或1(高)

    ```sql
    -- 查看系统变量
    show variables;	
    
    --可自定义局部/全局
    set [session/global] sql_safe_updates = off;
    ```
  - ```sql
    update 表名 
    	set 字段名1=新值 
    	  , 字段名2=新值   
      where 筛选条件;	-- 若不加子句, 则该字段这一列全部被修改
    ```

    - 若更新时出错则取消整个更新操作, 可使用ignore忽略错误强制更新: `update ignore 表名...`​
    - `=null`​(若字段允许)表删除改值
- 删: 若数据被外键依赖着, 删除会报错

  ```sql
  delete from 表名; 
  -- 删除内容, 不释放空间, 保留定义(表结构)
  -- 逐行删除, 每删除一行, 在日志中记录一次
  
  delete from 表名 where 条件; 
  
  -- eg:
  delete t1 
    from Person t1		-- 从t1中删除
       , Person t2 
   where t1.id > t2.id  
     and t1.email = t2.email;
  ```

‍

---

‍

# DQL

Data Query Language, 数据查询语言, 在查询中不能直接修改数据

- select子句: 投影

  - 去重: `distinct`​, 多个字段时须加括号
  - 别名: `as`​
  - 所有: `*`​
- from子句: 表连接, 不涉及表的常量查询可省略from

  - 子查询: 把select查询结果应用到sql语句中, 本质为select嵌套, 必须起别名
  - 连接查询: MySQL支持两种join算法: `nest loop join`​、`hash join`​

    - 笛卡尔连接

      - `from 表1, 表2;`​, 多个表中间用逗号隔开

        - `where 表1.字段 = 表2.字段;`​, 索引用点.运算符, 可以一次引多个字段
        - `from t as a, t as b`: 自连接, 自己连接自己, 使用别名, 比自查询速度快
      - `表1 cross join 表2`: 没有on...
    - 内连接

      - `表1 [inner] join 表2 on 条件;`: 展示两张表同时满足连接条件的数据, inner可省略

        - 外键字段的值可以为空, 不为空的时候, 数据必须来自主表
        - 保留所有数据, 包括相同列
      - `表1 natural join 表2`: 自然连接, 自动匹配同名列, 只保留一个, 其他列全部保留

        - 前提: 必须有同名列
    - 外连接

      - 左外连接: `表1 left join 表2 on 关系;`​, 关系可有多个

        - 左表显示全部, 右表中显示满足连接条件的数据
        - 右表没有匹配项的在右表显示为null
      - 右外连接: `表1 right join 表 on 关系;`​, 关系可有多个

        - 右表显示全部, 左表中显示满足连接条件的数据
        - 左表没有匹配项的在左表显示为null
      - 全外连接: `full join`​, 展示两表全部信息, 满足条件的进行匹配, 不满足的空值填充

        - MySQL不支持全外连接, 替代方案: 左连接+union+右连接

    ![截屏2024-12-31 22.58.51](../assets/截屏2024-12-31%2022.58.51-20241231225856-0ydm2lx.png)
  - 组合查询

    - `union`: 自动去重
    - `union all`: 不会去重, 效率更高
    - 特性

      - 加在select语句之间, 只有一个有分号, 至少两个select语句
      - 必须有相同的列、表达式、聚合函数
      - 列数据类型必须兼容(不必完全相同)
      - 一个组合查询只能有一个order by, 且放在最后
  - 别名: `as`​
- where子句: 分组前筛选, 筛选字段必须来自表中, 结果集中字段需定义临时表

  - 可以有两个筛选: `where (a, b) = any(select a, b from ...)`​
- group by子句: 分组统计

  - 聚合函数: `sum`​、`avg`​、`max`​、`min`​、`count`​、`count(distinct)`​、`variance`​、`var_pop`​、`var_samp`​、`stddev`​、`stddev_pop`​、`stddev_samp`​
  - `with rollup`: 总计, 在order by之前
  - `group by 1, 2, 3`: 根据第一、二、三列分组
- having子句: 分组后筛选, 可写聚合函数
- order by子句: 排序, <u>对结果集的操作</u>, <u>必须先有结果集</u>

  - 默认升序, asc, 可省略, 降序desc
  - 多个字段排序时, 主排序字段为第一个, 若第一个数值一样, 再按照第二个字段
  - 实现随机抽样: `order by rand() limit n`​, 抽取n个样本
- limit/offset子句: 分页, MySQL方言, 第一行的位置默认为0, 在order by之后

  - `limit 跳过行数, 要显示的行数;`​, 不包括跳过的最后一行, 如limit10, 则从第11行开始
  - `offset + 起始位置`​
- 执行顺序&书写顺序: 窗口函数在select之前

  ![截屏2024-12-31 22.59.35](../assets/截屏2024-12-31%2022.59.35-20241231225939-cevml94.png)

‍

---

‍

# DCL

Data Control Language, 数据控制语言

- 管理用户&角色

  - 查:

    ```sql
    use mysql;
    select user from user;
    ```
  - 创: 

    - 用户: `create user '用户名'@'主机IP' identified by '密码';`​

      - `@localhost`: 仅本机登录
      - `@'10.11.51.%'`: 通配符, 10.11.51子网内所有主机
      - `@'%'`: 所有人
      - `identified by 指定文本口令/指定散列值口令`​
    - 角色: `create role 角色名;`​, 角色即可以分配给多个用户的一组命名权限

      - 一个角色可以分配给多个用户, 一个用户可以分配给多个角色
  - 改: 重命名: `rename user 用户名 to 新用户名;`​
  - 删: `drop user if exists 'xxx'@'xxx'`​、​`drop role 角色名;`​
- 访问控制

  - 查: `show grants for 用户;`​
  - 授予权限: `grant 操作 on 库名.表名 to ['xxx'@'xxx'｜角色名];`​, 可使用通配符\*

    ```sql
    -- 多条操作间用逗号隔开
    -- 不能操作库
    grant insert, delete, update, select on ...
    
    -- 不能授权别人
    grant all privileges on *.* to 'xxx'@'xxx';
    
    -- 可授权别人
    grant all privileges on *.* to 'xxx'@'xxx' with grant option;
    
    -- 通过角色授权
    grant 角色名 to 'xxx'@'xxx';
    ```
  - 召回权限: 

    ```sql
    revoke 操作 on 库名.表名 from 'xxx'@'xxx'; 
    
    revoke all privileges on *.* from 'xxx'@'xxx';
    ```
  - 权限生效: `flush privileges;`​

    ![截屏2024-12-31 23.14.01](../assets/截屏2024-12-31%2023.14.01-20241231231404-3516e9v.png)

    ![截屏2024-12-31 23.14.21](../assets/截屏2024-12-31%2023.14.21-20241231231424-a9r3s08.png)
  - 修改密码: 

    ```sql
    -- 设置新密码
    alter user 'xxx'@'xxx' identified by '新密码';
    
    set password [for 用户名] = password('xxx')	-- 新口令必须传递到password()函数中进行加密
    -- 若未指定用户名, 更新当前登录用户的口令
    
    -- 密码过期
    alter user 'xxx'@'xxx' password expire;
    ```

‍

---

‍

# TCL

Transaction Control Language, 事务控制语言, MyISAM引擎不支持

- 开启事务环境: `start transaction;`​, 执行commit/rollback之后, 事务环境自动关闭
- 事务提交: `commit;`​, 仅在不出错时提交, 若出错, 自动撤销
- 事务回滚: `roll back;`​, 不能回退select、create、drop, 可执行但回退不会撤销该操作
- 保留点: 若回退, 可回退到特定占位符, 而不是回退全部, 保留点越多越好

  - 创: ​`savepoint 保留点名;`​
  - 使用: `rollback to 保留点名;`​
  - 释放: 事务完成后(执行一条commit/rollback之后)自动释放

    - 可以明确释放: `release savepoint;`​
- 更改提交行为: `set autocommit = 0 / 1;`​, autocommit标志针对每个连接而不是服务器

  - 不管有没有commit, 0则不自动提交, 1则自动提交

‍

---

‍

# 数据备份

## 数据导入/导出

- 导出数据: 

  - 导出到文本文件: `select ... into outfile '...';`​
  - Python脚本代码: CSV / Excel
  - 图形化客户端工具
- 导入数据: 

  - `load data infile ...;`​
  - Python脚本代码: CSV / Excel
  - 图形化客户端工具
- 导出SQL: 

  ```bash
  mysqldump -u root -p school > school1.sql
  
  mysqldump -u root -p --databases school > school2.sql
  ```
- 导入SQL

  ```bash
  mysql -u root -p school < school1.sql


  mysql -u root -p < school2.sql
  ```

## 备份

- 先刷新未写数据: `flush table`​语句
- 命令行程序`mysqldump`​: 转储所有数据库内容到某个外部文件
- 命令行程序`mysqlhotcopy`​: 从一个数据库复制所有数据
- MySQL: `backup table`​语句/`select into outfile`​

  - 转储所有数据到某个外部文件, 接受文件名, 此文件必须不存在, 可以用`restore table`​来复原
- 停机冷备份: 停机, 复制数据文件
- 联机热备份: Percona XtraBackup、MySQL Enterprise Backup

‍

---

‍

# 查询技巧

- 最大值: 

  - 排序法: `order by 字段 desc limit 1;`​, 不推荐
  - 嵌套查询: `where salary = (select max(salary) ...)`​
  - all运算符: `where salary >= all (select salary ...)`​
  - 计数法: `where (select count(*) where t2.sal > t1.sal ) = 0`​, t1和t2为同一表
  - 存在性判断: `where not exists (select 'x' where t2.sal > t1.sal)`​
- 常用: ​`ifnull`​、​`if()/case when`​、​`avg(expr)`​、​`date_format(col, expr)`​
- 前n名: 

  ```sql
  -- 计数法
  from ... as t1  
  where (select count(*) 
  		 from ... as t2  
  		where t2.sal > t1.sal) < n;
  ```
- 中位数:

  ```sql
  select round(avg(g_income)) as 中位数
    from (select g_income
               , row_number() over (order by g_income) as rk
               , count(*) over() as total
            from tb_graduate) tmp
   where rk between total/2 and (total+2)/2;
  ```
- 众数:

  ```sql
  with t1 as (select g_income
                   , count(*) freq
                from tb_graduate
               group by g_income)
  select *
    from t1
   where freq = (select max(freq)
                   from t1);
  ```
- 统计表: 

  ![截屏2025-01-01 08.00.34](../assets/截屏2025-01-01%2008.00.34-20250101080037-2gbeody.png)

  ```sql
  -- 双重case
  case when sum(case when month like '%6' then 1 else 0 end) > 0 then 'o' else 'x' end as Jun
  , case when sum(case when month like '%7' then 1 else 0 end) > 0 then 'o' else 'x' end as Jul
  , case when sum(case when month like '%8' then 1 else 0 end) > 0 then 'o' else 'x' end as Aug
  ```

  或者多union合并

  ![截屏2025-01-03 19.32.33](../assets/截屏2025-01-03%2019.32.33-20250103193236-24lcphg.png)
- 比较日期数据:

  ```sql
  -- datediff得到日期差
  select a.ID
  	 , a.日期
  	 , a.销售额
    from 日销 as a 
   cross join 日销 as b 
      on datediff(a.日期, b.日期) = 1
   where a.销售额 > b.销售额;
  
  select w2.id
    from Weather w1, Weather w2
   where datediff(w2.recordDate, w1.recordDate) = 1 
     and w2.Temperature > w1.Temperature
  
  -- date_add、date_sub得到日期
  select w1.id 
    from Weather w1, 
    left join Weather w2 
      on date_sub(w1.recordDate,interval 1 day) = w2.recordDate
   where w1.temperature > w2.temperature;
  
  -- 日期聚合
  date_format(order_date, '%Y-%M')
  -- 或
  left(order_date, 7)
  ```
- 占比: 

  ```sql
  -- 善于运用avg(condition), 不用除法表达式
  -- action为confirmed的占比, 等于则1, 否则为0, 本质为隐式转换
  select round(ifnull(avg(c.action = 'confirmed'), 0), 2) as rate
  ```
- 计数: `sum(state = 'approved')`​, 隐式转换, 等则1, 否则为0
- 归类: 

  ```sql
  select 'Low Salary' as category
       , sum(if(income < 20000, 1, 0)) as accounts_count from Accounts
  union
  select 'Average Salary' as category
       , sum(if(income >= 20000 and income <= 50000, 1, 0)) as accounts_count from Accounts
  union
  select 'High Salary' as category
       , sum(if(income > 50000, 1, 0)) as accounts_count from Accounts
  ```
- 峰值在线人数: 

  ```sql
  with temp1 as (
      select t1.id
           , t1.login_time as time_begin
           , 1 as flag
        from max_num t1
       union all
      select t2.id
           , t2.logout_time as time_begin
           , -1 as flag
        from max_num t2
  ),
       temp2 as (
      select time_begin
           , lead(time_begin, 1) over(order by time_begin) as next_time
           , sum(flag) over(order by time_begin 
  						   rows between unbounded preceding 
  						    and current row) as num_login
        from temp1
  )
  select max(num_login) as peak_online_users
       , time_begin as peak_start_time
       , next_time as peak_end_time
    from temp2
   where num_login = (select max(num_login) 
  					  from temp2)
     and next_time is not null;
  ```
- 连续三天:

  ```sql
  select distinct num as consecutiveNums 
    from logs
   where (id+1, num) in (select * from logs)
     and (id+2, num) in (select * from logs);
  ```
- 输出三角形: 

  ```sql
  set @row := 21;
  select reppeat('*', @row := @row - 1) as pattern
    from information_schema.tables
   where @row > 1;
  ```
- 递归:`with recursive t1 as ...`​, 查询可引用自身, 常用于填充数据, 做辅助表, 有性能问题

  ```sql
  with recursive list as (	-- 定义递归表名为list
  	select 2 as n			-- n的初始值为2
  	 union all
  	select n+1				-- 每次从list中取出上一轮的n并+1
  	  from list
  	 where n < 100			-- 递归终止于n=100
  )
  ```

‍

---

‍

# Python连接数据库

## 常见报错

- `Can't connect to MySQL server on 'localhost' (Connection refused timeout)`: 服务器未启动、防火墙阻止了请求、网络传输原因、IP地址写错、端口号写错
- `Access denied`: 用户名写错、密码写错、无响应的访问权限、数据库不存在
- `Unknown database`​
- `'NoneType' object has no attribute 'encoding'`: 字符集错误导致产生了None对象

## 步骤

- 导包: `import pymysql`​
- 创建连接对象: 

  ```python
  conn = pymysql.connect(		-- 创建连接对象
  	host = 'localhost',		-- 本机, 可用其他主机IP
  	port = 3306,			-- 固定, mysql服务端口
  	user = 'guest',			-- 用户, 可用root
  	password = 'xxx',
  	database = '库名',
  	charset = 'utf8mb4'
  )
  ```
- 获取游标对象: `cursor = conn.cursor()`​
- 执行SQL获取结果: `affected_rows = cursor.execute/executemany('insert into……')`​

  - 返回整数, 表sql语句影响的行数
  - 有SQL注入攻击风险`sql injection`: 永远不要用字符串拼接/格式化的方式动态生成SQL, 要使用安全占位符`%s`:

    `'update 表名 set 字段s... = %s', (eno,...)`​, 此处注意元组中元素顺序, 一元组要有逗号括号, 中间用逗号隔开
- 事务提交: `conn.commit()`​
- 事务回滚: `conn.rollback()`​

  - 用异常捕获, 建立连接之后就要用try语句: `except: rollback  finally: close`​
- 关闭连接, 释放资源: `conn.close()`​

  ![截屏2025-01-01 10.40.09](../assets/截屏2025-01-01%2010.40.09-20250101104011-fm3v0zd.png)​

## 其他操作

- 抓取数据: 在execute之后, 从结果集中返回数据

  ```python
  cursor.fetchone()
  cursor.fetchall()
  cursor.fetchmany(size=cursor.arraysize)
  
  while data:= cursor.fetchone():...
  ```
- 从excel导入数据:

  ```python
  # 建立工作表对象
  wb = openpyxl.load_workbook()
  sheet = wb['data']
  
  # 通过循环获取单元格的值
  try:
  	cursor = conn.cursor()
  	data = []
  	for i in range(2, 1947):
  		row = []
  		for j in range(1, 9):
  			row.append(sheet.cell(i, j).value)
  		data.append(row)
  except:...
  
  # 导入mysql
  cursor.executemany(
  	'insert into 表名'
  	'	(字段s)'
  	'values'
  	'	(%s, ...)',		# 最后引号外的逗号不可省略
  	data
  )
  ```
- 从网页爬取数据: 抓取页面, 解析页面, 保存数据

‍

---

‍

# SQLite

关系数据库, 轻量级, 嵌入式, 无需安装, 不支持多用户, 支持ACID, 不适合分布式/大规模应用

本质为一个.dll或.so库文件, 数据存储在一个单独.db文件中, <u>不需额外服务器进程(MySQL为独立进程)</u>

GUI管理工具推荐: DB Browser for SQLite

数据类型: 本身无数据类型, 创表不需指定字段类型, 但按照编程规范规定了数据类型

- `INTERGER`: 有符号的整数类型
- `REAL`: 浮点类型
- `TEXT`: 字符串, 采用utf-8和utf-16字符编码
- `BLOB`: 二进制大对象类型, 能够存放任意二进制数据

映射关系:

![截屏2025-01-01 10.09.14](../assets/截屏2025-01-01%2010.09.14-20250101100917-xta9noq.png)​

导包: `import sqlite3`​

异常类: `except sqlite3.Error as e:...`​

其他同MySQL
