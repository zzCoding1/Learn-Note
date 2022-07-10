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
> 2. 能干什么？
> 3. 怎么使用？

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
  ~~~

  

## 3. Hiredis的使用

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

     