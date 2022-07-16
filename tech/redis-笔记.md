## 1. 数据库类型

### 1.1 基本概念

1. 一般在服务器端使用
2. 关系型数据库 - sql
   - 操作必须使用sql语句
   - 数据存储在磁盘
   - 存储数据量大 
   - 举例：
     - mysql
     - oracle
     - sqllite - 文件数据库 
     - sql server
3. 非关系型数据库 - nosql
   - 操作不适用sql语句
   - 数据默认是存储到内存的
     - 速度快、效率高
     - **缺点**：存储的数据量小
   - 不需要数据库表
     - 以键值对的方式存储

### 1.2 关系/非关系型数据库搭配使用

![image-20220710150117458](assets/redis-%E7%AC%94%E8%AE%B0/image-20220710150117458.png)

> RDBMS: Relational Database Management System
>
> 1. 所有的数据模型存储在关系型数据库中
> 2. 客户端访问服务器，有一些数据，服务器要频繁的查询数据
>    - 服务器首先将数据从关系型数据库中读出来 -> 第一次
>      - 将数据写入到redis中
>    - 客户端第二次包含以后访问服务器
>      - 服务器从redis中读取
> 3. 非关系型数据库和关系型数据库数据的同步通过应用层控制

## 2. Redis

> 1. 知道redis是什么？
>    - 非关系型数据库，也可以叫内存数据库
> 2. 能干什么？
>    - 存储访问频率高的数据
>    - 共享内存
>      - 服务器端 -> redis
> 3. 怎么使用？
>    - 常用的操作命令
>      - 各种数据类型 -> 会查
>    - redis的配置文件
>    - redis的数据持久化
>    - 写程序的时候如何对redis进行操作
>      - 客户端 -> 服务器

### 2.1 基本知识点

1. 安装包的下载

   - [英文官方](https://redis.io/)
   - [中文官方](http://redis.cn/)

2. redis安装

   - make
   - make install

3. redis中的两个角色

   ~~~shell
   # 服务器 - 启动
   redis-server  # 默认启动
   redis-server confFileName  # 根据配置文件设置启动
   # 客户端
   redis-cli  # 默认连接本地，绑定了6379端口的服务器
   redis-cli -p 端口号  # 连接本地知道端口的服务器
   redis-cli -h IP地址 -p 端口  # 连接远程主机指定的服务器，要配置服务器才行
   # 通过客户端关闭服务器
   shutdown
   # 客户端的测试命令
   ping [MSG] # 中括号都是可选项
   ~~~

4. redis中的数据组织格式
   - 键值对
     - key：必须是字符串
     - value：可选的
       - string类型
       - list类型
       - set类型
       - sortedset类型
       - hash类型
5. redis中常用的数据类型
   - string类型
     - 字符串
   - list类型（更像是数组，按照数组理解）
     - 来存储多个sting字符串的
   - set类型
     - 集合
       - stl集合
         - 默认是升序序列，元素不重复
       - redis集合
         - 元素不重复，数据是无序的
   - sortedset类型
     - 排序集合，集合中的每个元素分为两部分
       - [分数，成员] -> [66, "tom"]
         - 按照第一个值排序
   - hash类型
     - 很map数据组织方式一样，都是key:value类型
       - Qt -> QHash，QMap
       - map -> 底层红黑树实现
       - hash -> 底层数组实现
         - a[index] = xx

### 2.2 redis常用命令（官方文档可查）

- String类型

  ~~~shell
  key -> string
  value -> string
  
  # 设置一个键值对 -> sting:string
  SET key value  # 设置一个新的string值
  # 例如
  SET hello world
  
  # 查询所有key
  keys *
  
  # 获取key对应的value
  GET key  
  
  # 同时设置一个或多个 key-value
  MSET key value [key value ...]
  # 例如
  MSET h1 world1 h2 world2 h3 world 
  
  # 同时查看多个key对应的value
  MGET key [key ...]
  
  # 如果key已经存在并且是一个字符串，APPEND命令将value追加到key原来值得末尾
  # key：hello，value：world，append:12345 -> hello:world12345
  APPEND key value
  
  # 返回 key 对应字符串的长度
  STRLEN key
  
  # 将 key 中存储的数字值减一。
  # 前提，value必须是一个数字字符串 - "123456"
  DECR key
  
  # 使用SET设置一个存在 key，会覆盖原来的 value
  
  # 将 key 中存储的数字值加一。
  # 前提，value必须是一个数字字符串 - "123456"
  INCR key
  
  # 将key值对应的 value 数值减去指定的值
  DECRBY key decrement
  
  # 将key值对应的 value 数值加上指定的值
  INCRBY key increment
  ~~~

- List类型 - 存储多个字符串

  ~~~shell
  key -> string
  value -> list
  # 将一个或多个值插入到列表key的表头，如果不存在，则创建一个
  LPUSH key value [value ...]
  # 例如：1 2 3 4 5 -> 5 4 3 2 1。默认当做字符串处理，
  # "hello world" 当做一个字符串处理
  # hello world 当做两个字符串处理
  
  # 将一个或多个值插入到列表key的表尾巴（最右边）。
  RPUSH key value [value ...]
  
  # 删除最左侧元素
  LPOP key
  # 删除左右侧元素
  RPOP key
  
  # 遍历
  LRANGE key start stop
  	start：起始位置，0
  	stop：结束位置，-1 # -1表示倒数第一个位置 -2：表示倒数第二个位置
  	
  # 通过下表得到对应位置的字符串
  LINDEX key index
  
  # list中字符串的个数
  LLEN key
  
  # 将值 value 插入到列表 key 的表头，当且仅当 key 存在且是一个列表，当 key 不存在，什么也不做
  LPUSHX key value
  
  # 根据 count 的值，移除列表中与参数 value 相等的元素。
  # count > 0:从表头开始向表尾搜索，移除与 value 向等的元素，个数为count
  # count < 0:从表尾开始向表头搜索，移除与 value 向等的元素，个数为count
  # count = 0:删除所有与 value向等的元素
  LREM key count value
  ~~~

- Set类型

  ~~~shell
  key -> string
  value -> set类型（"string", "string"）
  
  # 添加元素
  # 将一个或多个元素元素添加到集合 key 中，已经存在的 member 将被忽略
  SADD key member[member ...]
  
  # 遍历
  SMEMBERS key
  
  # 返回集合的差集
  SDIFF key [key ...]
  
  # 交集
  SINTER key [key ...]
  
  # 并集
  SUNION key [key ...]
  
  # 将结果保存到 destination 集合
  SDIFFSTORE destination key [key ...]
  
  # ... 交集、并集同上
  
  # 移除key集合中，一个或多个member元素，不存在member元素则背忽略
  SREM key member [member ...] 
  
  # 移除key集合中一个随机的元素
  SPOP
  ~~~

- SortedSet

  ~~~shell
  key -> string
  value -> sorted([socre, member], [socre, member], ...)
  
  # 添加元素
  ZADD key score member [[score member], [score member], ...]
  
  # 遍历
  ZRANGE key start stop [WITHSCORES] # -> 升序 
  ZREVRANGE  key start stop [WITHSCORES] # -> 降序
  
  # 指定分数区间内元素的个数
  ZCOUNT key min max
  
  # 返回 key 中member的排名
  ZRANK key member
  
  # 移除key中一个或多个member
  ZREM key member [member ...]
  
  # 返回 key 中member的排名
  ZREVRANK
  
  # 返回key中member的score值
  ZSCORE key member
  
  # 检测key剩下的存在时间，返回-2表示不存在，返回-1表示存在，但是没设置剩余存在时间
  TTL key
  
  # 移除给定key的生存时间
  PERSIST key
  ~~~

- Hash类型

  ![image-20220710224548940](assets/redis-%E7%AC%94%E8%AE%B0/image-20220710224548940.png)

  ~~~shell
  key -> string
  value -> hash ([key:value],[key:value],[key:value],...)
  
  # 添加数据，不存在则创建，存在则覆盖，可以在同一个key上追加
  HSET key field value
  
  # 取数据
  HGET key field
  
  # 批量插入键值对
  HMSET key field [key field ...]
  
  # 批量取数据
  HMGET key field [field ...]
  
  # 删除键值对
  HDEL key field [field ...]
  
  # 判断哈希表key中，给定域field是否存在，如果有返回1，没有返回0
  HEXISTS key field
  
  # 返回哈希key中所有值和域
  HGETALL key 
  
  # 为哈希表key中的field的值加上增量increment
  HINCRBY key field increment
  
  # 返回哈希表key中所有的域
  HKEYS key
  
  
  # 返回哈希表key中域的数量
  HLEN key
  
  # 返回哈希表key的所有域的值
  HVALUE key
  ~~~

- key 相关的命令

  ~~~shell
  # 删除减值对
  DEL key [key ...]
  
  # 匹配指定key值，正则表达式
  KEYS pattern
  
  #检测key是否存在，返回1表示存在，返回0表示不存在
  EXISTS key 
  
  # 给key设置一个生存时间，当key过期时，会自动删除
  EXPIRE key seconds
  
  # 返回key的value的类型
  TYPE key
  ~~~

  ### 2.3 配置文件

> 配置文件是给redis服务器使用的

  1. 配置文件位置

     - 从源码安装目录中找 -> redis.conf

  2. 配置文件配置项

     ~~~shell
     # redis绑定谁之后，谁就能访问redis服务器
     # 注释表示任意一台电脑都能访问
     bind 127.0.0.1 192.168.1.100
     
     # 保护模式，如果要远程客户端访问服务器，该模式要关闭
     protected-mode yes
     
     # redis服务器启动的时候绑定的端口，默认是6379
     port 6379
     
     # 超时时长，0表示关闭，大于0表示开启
     timeout 0
     
     # 表示tcp活跃时长
     tcp-keepalive 300
     
     # no表示启动之后不是守护进程，yes表示是守护进程
     daemonize no/yes
     
     # 如果服务器是守护进程，那么就会生成一个pid文件，路径如下
     # ./ 表示服务器启动的目录，redis-server 目录
     pidfile /var/run/redis_6379.pid
     
     # 日志级别
     loglevel notice
     	# 如果服务器是守护进程，才会写日志文件
     	logfile "" -> 这是没写
     	logfile ./redis.log
     
     # redis中数据库格式
     databases 16
     	- 切换 select dbID [dbID = 0 ~ 16-1]
     
     ~~~

### 2.4 redis数据持久化

> 持久化：数据从内存到磁盘的过程

持久化的两种方式

- rdb方式
  - 这是一种默认的持久化方式，默认打开
  - 磁盘的持久化文件xxx.rdb
  - 将内存数据以二进制的方式直接写入磁盘文件
  - 文件比较小，恢复的时候时间段，效率高
  - 以用户设定的频率 -> 容易丢数据
  - 数据完整性相对较低
- aof方式
  - 默认是关闭的
  - 磁盘的持久化文件xxx.aof
  - 直接将生成数据的命令写入磁盘文件
  - 文件比较大，恢复时间长，效率低
  - 以某种频率 -> 1sec
  - 数据完整性高

~~~shell
# rdb 同步频率，任意一个满足都可以
save 900 1
sava 300 10
save 60 10000
# rdb文件的名字
dbfilename dump.rdb
# 生成的持久化文件保存的那个目录，rdb和aof
dir ./
# aof模式是不是要打开
appendonly no/yes
# 设置aof文件的名字
appendfinename "appendonly.aof"
# aof更新的频率
appendfsync always
appendfsync everytsec
appendfsync no  # 不是不同步，操作系统强制刷新才同步
~~~

1. aof和rdb能不能同时打开？

   - 可以

2. aof和rdb能不能同时关闭？

   - 可以
   - rdb如何关闭

   ~~~shell
   save ""
   ~~~

   

3. 两种模式同时开启，如何要进行数据恢复，如何选择？

   - 效率上考虑：rdb模式
   - 数据完整性：aof模式

## 3. Hiredis的使用

1. 

## 4. 复习

1. fastDFS

   - 是什么？

     - 分布式文件系统

   - 干什么？

     - 提供文件上传
     - 提供文件下载 

   - 怎么使用？

     - 根据主机的角色->修改对应的配置文件
     - 启动各个角色

     ~~~SHell
     fdfs_trackerd /etc/fdfs/tracker.conf
     fdfs_storaged /etc/fdfs/storage.conf
     ~~~

     - 客户端编写

      