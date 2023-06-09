# 一、Redis基础

## 1.1 Redis有哪些优缺点？

### 1.1.1 优点

- **读写性能优异**。
- **支持数据持久化**，支持AOF和RDB两种持久化方式。
- **支持事务**，Redis的所有操作都是原子性的，同时还支持对几个操作合并后的原子性执行。
- 数据结构丰富，除了支持String类型的value外，还支持Hash、Set、ZSet、List等数据机构。
- **支持主从复制**，主机会自动将数据同步到从机，可以进行读写分离。

### 1.1.2 缺点

- **数据库容量**受到物理内存限制，不能用作海量数据的高性能读写，只适用于**较小数据量**的高性能操作和运算上。
- **Redis不具备自动容错和恢复功能**。
- 无法支持复杂查询。
- 数据容易丢失。
- **Redis较难支持在线扩容。**

## 1.2 Redis为什么这么快？

1. Redis是基于内存的，内存的访问速度是磁盘的上千倍；
2. Redis 基于 Reactor 模式设计开发了一套高效的**事件处理模型**，主要是**单线程事件循环**和 **IO 多路复用**；
3. Redis 内置了多种优化过后的数据结构实现，性能非常高。

## 1.3 为什么要用Redis？（为什么要用缓存？）

1. 高性能

   假如用户第一次访问数据库中的某些数据的话，是从硬盘中读取的，这个过程是比较慢。但是，如果用户访问的数据属于高频数据并且不会经常改变的话，可以将该用户访问的数据存在缓存中。这样保证用户下一次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。

2. 高并发

   直接操作缓存能够承受的数据库请求数量是远远大于直接访问数据库的，所以可以考虑把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接到缓存而不用经过数据库。进而，也就提高了系统整体的并发。

## 1.4 说一说Redis和Memcached的区别和共同点？

### 1.4.1 共同点

1. 都是基于内存的数据库，一般都用来当做缓存使用。
2. 都有过期策略。
3. 两者性能都非常高。

### 1.4.2 区别

1. **数据类型支持不同**， Memcached 仅支持简单的 key-value 结构的数据记录，Redis 支持的数据类型要丰富得多。最为常用的数据类型主要有五种：String、Hash、List、Set 和 Sorted Set。 
2. **内存管理机制不同**，在 Redis 中，并不是所有的数据都一直存储在内存中的。这是和 Memcached 相比一个最大的区别。当物理内存用完时，Redis 可以将一些很久没用到的 value 交换到磁盘。 
3. **数据持久化支持不同**，Redis 虽然是基于内存的存储系统，但是它本身是支持内存数据的持久化的，而且提供两种主要的持久化策略：RDB 快照和 AOF 日志。而 memcached 是不支持数据持久化操作的。 
4. **集群的管理不同**，Memcached 本身并不支持分布式，因此只能在客户端通过像一致性哈希这样的分布式算法来实现 Memcached 的分布式存储。相较于 Memcached 只能采用客户端实现分布式存储，Redis 更偏向于在服务器端构建分布式存储。

## 1.5 说一说Redis中常用的缓存读写策略？

### 1.5.1 旁路缓存模式

适用于**请求比较多**的场景。

旁路缓存模式中服务端需要同时维系DB和Cache，并且以DB的结果为主。

**写：**

- 先更新DB；
- 然后直接删除Cache。

![img](https://oss.javaguide.cn/github/javaguide/database/redis/cache-aside-write.png)

**写：**

- 从Cache中读取数据，读取到就直接返回；
- Cache中读取不到的话，就从DB中读取数据返回；
- 再把数据放到Cache中。

![img](https://oss.javaguide.cn/github/javaguide/database/redis/cache-aside-read.png)

#### 在写数据的过程中，可以先删除Cache，然后再更新DB吗?

**不行！**因为这样可能会造成DB和Cache中的**数据不一致**的问题。

比如，请求1先把Cache中的A数据删除 -> 请求2从DB中读取数据 -> 请求1再把DB中的A数据更新。

#### 在写数据的过程中，可以先更新DB，然后再删除Cache吗？

理论上来说还是有可能出现数据不一致性的问题，不过概率比较小，因为Cache的写入速度比DB写入速度快很多。

比如，请求1从DB中读数据A -> 请求2更新DB中的数据A -> 请求1将数据A写入Cache。

**旁路缓存模式的缺点：**

1. 首次请求的数据一定不在Cache中。

   **解决方法：**可以将热点数据提前放入Cache中。

2. 写操作比较频繁的话会导致Cache中的数据会被频繁删除，这样会影响缓存的命中率。

   **解决方法：**

   - DB和Cache数据一致的情况下，同步更新DB和Cache，但需要加锁保证更新Cache时不存在线程安全问题。
   - DB和Cache数据不一致的情况下，同步更新DB和Cache，但是给Cache加一个比较短的过期时间。

### 1.5.2 读写穿透模式

该模式中服务端把Cache视为主要数据存储，从中读取数据并将数据写入其中，Cache负责将数据读取和写入DB。

**写穿透：**

- 先查Cache，Cache中不存在，直接更新DB；
- Cache中存在，则先更新Cache，然后Cache服务自己更新DB（同步更新Cache和DB）。

![img](https://oss.javaguide.cn/github/javaguide/database/redis/write-through.png)

**读穿透：**

- 从Cache中读取数据，读取到就直接返回；
- 读取不到的话，先从DB加载，写入到Cache后返回响应。

![img](https://oss.javaguide.cn/github/javaguide/database/redis/read-through.png)

### 1.5.3 异步缓存写入模式

与读写穿透模式类似，都是由Cache服务来负责Cache和DB的读写。

但是，读写穿透模式是**同步更新Cache和DB**，而异步缓存写入模式**只是更新Cache，不直接更新DB，通过异步批量的方式来更新DB。**

# 二、Redis数据结构

## 2.1 Redis常用的数据结构有哪些？

- **5种基本数据结构：**String（字符串）、List（列表）、Hash（散列）、Set（集合）、Zset（有序集合）。
- **3种特殊数据结构：**HyperLogLogs（基数统计）、Bitmap（位存储）、Geospatial（地理位置）。

## 2.2 String的应用场景有哪些？

String是一种二进制安全的数据结构，可以用来存储任何类型的数据，比如字符串、整数、浮点数、图片（图片的base64编码或解码或图片的路径）、序列化后的对象。

- **常规数据的缓存**，比如session、token、序列化后的对象、图片的编码或路径等。
- **计数**，比如用户单位时间的请求数、页面单位时间的访问数等。
- **分布式锁**。

## 2.3 存储对象数据使用String还是Hash更好？

- String存储的是序列化后的对象数据、存储的是整个对象。Hash是对对象的每个字段单独存储，可以获取部分字段的信息，也可以修改或添加部分字段。如果对象中的某些字段需要经常变动或需要单独查询对象中某些字段信息，就更适合用Hash。
- String存储相对来说更节省内存，缓存相同数量的对象数据，String消耗的内存约是Hash的一半。并且存储具有多层嵌套的对象时也方便很多。

总结：绝大多数情况下，更建议使用String来存储对象数据。

## 2.4 String的底层实现是什么？

Redis虽然是基于C语言编写的，但是Redis中的String类型的底层并不是C语言中的字符串，而是作者自己编写了SDS（简单动态字符串）作为底层实现的。

SDS相比于C语言的字符串有以下提升：

1. **可以避免缓冲区溢出：**C 语言中的字符串被修改（比如拼接）时，一旦没有分配足够长度的内存空间，就会造成缓冲区溢出。SDS 被修改时，会先根据 len 属性检查空间大小是否满足要求，如果不满足，则先扩展至所需大小再进行修改操作。
2. **获取字符串长度的复杂度较低：** C 语言中的字符串的长度通常是经过遍历计数来实现的，时间复杂度为 O(n)。SDS 的长度获取直接读取 len 属性即可，时间复杂度为 O(1)。
3. **减少内存分配次数：**为了避免修改（增加/减少）字符串时，每次都需要重新分配内存（C 语言的字符串是这样的），SDS 实现了空间预分配和惰性空间释放两种优化策略。当 SDS 需要增加字符串时，Redis 会为 SDS 分配好内存，并且根据特定的算法分配多余的内存，这样可以减少连续执行字符串增长操作所需的内存重分配次数。当 SDS 需要减少字符串时，这部分内存不会立即被回收，会被记录下来，等待后续使用（支持手动释放，有对应的 API）。
4. **二进制安全：**C 语言中的字符串以空字符 `\0` 作为字符串结束的标识，这存在一些问题，像一些二进制文件（比如图片、视频、音频）就可能包括空字符，C 字符串无法正确保存。SDS 使用 len 属性判断字符串是否结束，不存在这个问题。

## 2.5 购物车信息是用String还是Hash存储更好？

上面2.3中分析过了，由于购物车中的商品频繁需要修改和变动，所以购物车信息建议使用Hash存储：

- 用户ID为Key
- 商品ID为field，商品数量为Value

![Hash维护简单的购物车信息](https://oss.javaguide.cn/github/javaguide/database/redis/hash-shopping-cart.png)

### 2.5.1 用户购物车信息的维护具体应该怎么操作？

1. 用户添加商品就是往Hash里面添加field和value；
2. 查询购物车信息就是遍历Hash；
3. 更改商品数量就修改对应的value值；
4. 删除商品就是删除Hash中对应的field；
5. 清空购物车就直接删除对应Key；

## 2.6 用Redis实现一个排行榜怎么做？

可以用到Redis中的Zset数据结构，可以实现各种排行榜，比如送礼物排行榜、微信步数排行榜、段位排行榜等等。

其中一些常见的Redis命令：`ZRANGE`（从小到大排序）、`ZREVRANGE`（从大到小排序）、`ZREVRANK`（指定元素排序）。

## 2.7 Set的应用场景是什么？

Redis 中 `Set` 是一种无序集合，集合中的元素没有先后顺序但都唯一，有点类似于 Java 中的 `HashSet` 。

- **存放数据不能重复的场景。**比如文章点赞、动态点赞等等。
- **需要获取多个数据源交集、并集和差集的场景。**比如共同好友、共同粉丝、共同关注、好友推荐等等。
- **需要随机获取数据源中的元素。**比如抽奖系统、随机点名等等。

## 2.8 如何用Set实现抽奖系统？

- `SADD key member1 member2 ...`：向指定集合添加一个或多个元素。
- `SPOP key count`：随机移除并获取指定集合中一个或多个元素，适合不允许重复中奖的情况。
- `SRANDMEMBER key count`：随机获取指定集合中指定数量的元素，适合运行重复中奖的情况。

## 2.9 如何用Bitmap统计活跃用户？

Bitmap 存储的是连续的二进制数字（0 和 1），通过 Bitmap, 只需要一个 bit 位来表示某个元素对应的值或者状态，key 就是对应元素本身 。我们知道 8 个 bit 可以组成一个 byte，所以 Bitmap 本身会极大的节省储存空间。

可以将 Bitmap 看作是一个存储二进制数字（0 和 1）的数组，数组中每个元素的下标叫做 offset（偏移量）。

如果想要使用 Bitmap 统计活跃用户的话，可以使用日期（精确到天）作为 key，然后用户 ID 为 offset，如果当日活跃过就设置为 1。

初始化数据：

```sh
SETBIT 20210308 1 1
(integer) 0
SETBIT 20210308 2 1
(integer) 0
SETBIT 20210309 1 1
(integer) 0
```

统计 20210308~20210309 总活跃用户数:

```sh
BITOP and desk1 20210308 20210309
(integer) 1
BITCOUNT desk1
(integer) 1
```

统计 20210308~20210309 在线活跃用户数:

```sh
BITOP or desk2 20210308 20210309
(integer) 1
BITCOUNT desk2
(integer) 2
```

# 三、Redis持久化机制

## 3.1 说一说Redis支持的持久化机制？

- 快照（RDB）
- 只追加文件（AOF）
- RDB和AOF的混合持久化（Redis 4.0新增）

## 3.2 RDB持久化

### 3.2.1 什么是RDB持久化？

Redis 可以通过创建快照来获得存储在内存里面的数据在 **某个时间点** 上的副本。

Redis 创建快照之后，可以对快照进行备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（Redis 主从结构，主要用来提高 Redis 性能），还可以将快照留在原地以便重启服务器的时候使用。

快照持久化是 Redis **默认**采用的持久化方式，在 `redis.conf` 配置文件中默认有此下配置：

```clojure
save 900 1           #在900秒(15分钟)之后，如果至少有1个key发生变化，Redis就会自动触发bgsave命令创建快照。

save 300 10          #在300秒(5分钟)之后，如果至少有10个key发生变化，Redis就会自动触发bgsave命令创建快照。

save 60 10000        #在60秒(1分钟)之后，如果至少有10000个key发生变化，Redis就会自动触发bgsave命令创建快照。
```

### 3.2.2 RDB创建快照时会阻塞主线程吗？

Redis提供了两个命令来生成RDB快照文件：

- `save`：同步保存操作，会阻塞Redis主线程；
- `bgsave`：fork出一个子进程，子进程执行，不会阻塞Redis主线程，默认选项。

## 3.3 AOF持久化

### 3.3.1 什么是AOF持久化？

与RDB持久化相比，AOF持久化的实时性更好。

默认情况下Redis没有开启AOF，但是**Redis 6.0之后默认是开启的**。

开启 AOF 持久化后每执行一条会更改 Redis 中的数据的命令，Redis 就会将该命令写入到 AOF 缓冲区 `server.aof_buf` 中，然后再写入到 AOF 文件中（此时还在系统内核缓存区未同步到磁盘），最后再根据持久化方式（ `fsync`策略）的配置来决定何时将系统内核缓存区的数据同步到硬盘中的。

### 3.3.2 AOF的基本流程？

1. **命令追加：**所有的写命令会追加到AOF缓冲区中。
2. **文件写入：**将AOF缓冲区的数据写入到AOF文件中。这一步需要调用`writer`函数，将数据写入到系统内核缓冲区后直接返回。（此时并没有同步到磁盘）
3. **文件同步：**AOF缓冲区根据对应的持久化方式向磁盘做同步操作。
4. **文件重写：**随着AOF文件越来越大，需要定期对AOF文件进行重写，达到压缩的目的。
5. **重启加载：**当Redis重启时，可以加载AOF文件进行数据恢复。

![AOF 工作基本流程](https://oss.javaguide.cn/github/javaguide/database/redis/aof-work-process.png)

### 3.3.3 持久化方式有哪些？

Redis配置文件中有三种不同的AOF持久化方式：

1. `appendfsync always`：主线程调用 `write` 执行写操作后，后台线程（ `aof_fsync` 线程）立即会调用 `fsync` 函数同步 AOF 文件（刷盘），`fsync` 完成后线程返回，这样会严重降低 Redis 的性能（`write` + `fsync`）。
2. `appendfsync everysec` ：主线程调用 `write` 执行写操作后立即返回，由后台线程（ `aof_fsync` 线程）每秒钟调用 `fsync` 函数（系统调用）同步一次 AOF 文件（`write`+`fsync`，`fsync`间隔为 1 秒）
3. `appendfsync no` ：主线程调用 `write` 执行写操作后立即返回，让操作系统决定何时进行同步，Linux 下一般为 30 秒一次（`write`但不`fsync`，`fsync` 的时机由操作系统决定）。

可以看出：**这 3 种持久化方式的主要区别在于 `fsync` 同步 AOF 文件的时机（刷盘）**。

### 3.3.4 AOF为什么是在执行完命令后记录日志？

关系型数据库（如 MySQL）通常都是执行命令之前记录日志（方便故障恢复），而 Redis AOF 持久化机制是在执行完命令之后再记录日志。

![AOF 记录日志过程](https://oss.javaguide.cn/github/javaguide/database/redis/redis-aof-write-log-disc.png)

- 避免额外的检查开销，AOF记录日志不会对命令进行语法检查；
- 在命令执行完之后再记录，不会阻塞当前命令执行。

**缺点：**

- 如果刚执行完命令Redis就宕机，会导致对应的修改丢失；
- 可能会阻塞后续其他命令的执行，因为AOF记录日志是在Redis主线程中进行的。

### 3.3.5 AOF文件重写了解吗？

当 AOF 变得太大时，Redis 能够在后台自动重写 AOF 产生一个新的 AOF 文件，这个新的 AOF 文件和原有的 AOF 文件所保存的数据库状态一样，但体积更小。

![AOF 重写](https://oss.javaguide.cn/github/javaguide/database/redis/aof-rewrite.png)

由于 AOF 重写会进行大量的写入操作，为了避免对 Redis 正常处理命令请求造成影响，Redis 将 AOF 重写程序放到子进程里执行。

AOF 文件重写期间，Redis 还会维护一个 **AOF 重写缓冲区**，该缓冲区会在子进程创建新 AOF 文件期间，记录服务器执行的所有写命令。当子进程完成创建新 AOF 文件的工作之后，服务器会将重写缓冲区中的所有内容**追加**到新 AOF 文件的末尾，使得新的 AOF 文件保存的数据库状态与现有的数据库状态一致。最后，服务器用新的 AOF 文件替换旧的 AOF 文件，以此来完成 AOF 文件重写操作。

## 3.4 Redis 4.0对持久化机制做了什么优化？

由于 RDB 和 AOF 各有优势，于是，Redis 4.0 开始支持 RDB 和 AOF 的混合持久化（默认关闭，可以通过配置项 `aof-use-rdb-preamble` 开启）。

如果把混合持久化打开，AOF 重写的时候就直接把 RDB 的内容写到 AOF 文件开头。

这样做的好处是可以结合 RDB 和 AOF 的优点, **快速加载**同时**避免丢失过多的数据**。

当然缺点也是有的， AOF 里面的 RDB 部分是压缩格式不再是 AOF 格式，**可读性较差。**

## 3.5 如何选择RDB和AOF？

### 3.5.1 RDB的优势

- RDB文件存储的内容是经过压缩的二进制数据，保存着某个时间点的数据集，文件很小，适合做数据的备份，灾难恢复。
- 使用RDB文件恢复数据，直接解析还原数据即可，不需要一条条地执行命令，速度非常快。

### 3.5.2 AOF的优势

- AOF的数据安全性更高，可以实时或秒级持久化数据。RDB会对机器的CPU资源和内存资源产生严重的影响。
- RDB可能会存在版本不兼容的问题。
- AOF以一种易于理解和解析的格式包含所有操作的日志。方便导出AOF文件进行分析。

**总结：**

- Redis保存的数据丢失一些也没有影响的话，可以使用RDB；
- 不建议单独使用AOF，因为时不时创建一个RDB快照可以进行数据库备份、更快的重启以及解决AOF引擎错误。
- 如果保存的数据要求安全性比较高的话，建议同时开启RDB和AOF持久化或者开启混合持久化。

# 四、Redis线程模型

## 4.1 Redis单线程模型了解吗？

**Redis 基于 Reactor 模式设计开发了一套高效的事件处理模型**，这套事件处理模型对应的是 Redis 中的文件事件处理器（file event handler）。由于文件事件处理器（file event handler）是单线程方式运行的，所以我们一般都说 Redis 是单线程模型。

文件事件处理器主要包含4个部分：

- 多个socket
- IO多路复用程序
- 文件事件分派器
- 事件处理器

![文件事件处理器（file event handler）](https://oss.javaguide.cn/github/javaguide/database/redis/redis-event-handler.png)

### 4.1.1 既然是单线程，那怎么监听大量的客户端连接呢？

Redis通过**IO多路复用**来监听来自客户端的大量连接，将感兴趣的事件及类型注册到内核中并监听每个事件是否发生，**降低了资源消耗**。

## 4.2 Redis 6.0之前为什么不使用多线程？

虽然Redis是单线程模型，但是实际上**Redis在4.0之后的版本中就已经加入了对多线程的支持。**

1. 单线程编程容易，并且更容易维护；
2. Redis的性能瓶颈不在CPU，而在于内存和网络；
3. 多线程就会存在死锁、线程上下文切换等问题，甚至会影响性能；

## 4.3 Redis 6.0之后为什么引入了多线程

引入多线程主要是为了**提高网络IO读写性能**。

Redis 6.0的多线程默认是禁用的，只使用主线程。

# 五、Redis内存管理

## 5.1 为什么Redis要给缓存设置过期时间？

因为内存是有限的，如果缓存中的数据一直保存的话，会造成内存溢出，所以设置过期时间有助于环节内存消耗。

### 5.1.1 设置过期时间除了缓解内存消耗还有什么用？

很多时候，业务场景对于某些数据的需求只在某段时间内存在，比如说短信验证码只在一分钟内有效、用户登录的token只在一天内有效等等。如果使用传统的数据库来处理的话，一般还需要自己判断是否过期，这样会导致数据库性能差很多，而且很麻烦。

## 5.2 Redis如何判断数据是否过期？

Redis通过一个过期字典（可以看做是hash表）来保存数据过期时间。过期字典的key指向Redis数据库中的某个key，过期字典的value是一个long long型整数，这个整数保存了key所指向的数据库key的过期时间。

![redis过期字典](https://oss.javaguide.cn/github/javaguide/database/redis/redis-expired-dictionary.png)

过期字典是存储在redisDb这个结构中：

```c
typedef struct redisDb {
    ...

    dict *dict;     //数据库键空间,保存着数据库中所有键值对
    dict *expires   // 过期字典,保存着键的过期时间
    ...
} redisDb;
```

## 5.3 过期数据的删除策略了解吗？

Q：加入设置了一批key只能存活1分钟，那么一分钟后Redis是怎么对这批key进行删除的呢？

**常用的过期数据删除策略就两个：**

1. **惰性删除：**只会在取出key的时候才对数据进行过期检查。这样对CPU最友好，但是可能会造成太多过期key没有被删除。
2. **定期删除：**每隔一段时间抽取一批key执行删除过期key操作。并且，Redis底层会通过限制删除操作执行的时长和频率来减少删除操作对CPU时间的影响。

惰性删除对CPU更友好。定期删除对内存更友好。所以Redis采用的是**惰性删除+定期删除**。

但是以上两种删除策略还是可能存在遗漏很多过期key的情况，这样同样会造成内存溢出的情况。

怎么解决这个问题呢？

Redis内存淘汰机制

## 5.4 Redis的内存淘汰机制了解吗？

Q：MySQL中有2000w条数据，Redis中只有20w条数据，如何保证Redis中的数据都是热点数据？

Redis提供了6中数据淘汰策略：

1. **volatile-lru**：从已设置过期时间的数据集中挑选**最近最少使用**的数据淘汰。
2. **volatile-ttl**：从已设置过期时间的数据集中挑选**将要过期**的数据淘汰。
3. **volatile-random**：从已设置过期时间的数据集中**任意选择**数据淘汰。
4. **allkeys-lru**：当**内存不足以容纳新写入数据**时，在键空间中，移除**最近最少使用**的key。（最常用）
5. **allkeys-random**：从数据集中**任意选择**数据淘汰。
6. **no-eviction**：禁止驱除数据，当内存不足时，不准新写入数据。

Redis 4.0之后增加了两种淘汰策略：

1. **volatile-lfu**：从已设置过期时间的数据集中挑选**最不经常使用**的数据淘汰。
2. **allkeys-lfu**：当内存不足以容纳新写入数据时，在键空间中，移除**最不经常使用**的key。

# 六、Redis事务

## 6.1 什么是Redis事务？

**Redis事务提供了一种将多个命令请求打包的功能。然后，再按顺序执行打包的所有命令，并且不会被中途打断。**

Redis 事务实际开发中使用的非常少，功能比较鸡肋，不建议在日常开发中使用，不要将其和我们平时理解的关系型数据库的事务混淆了。

## 6.2 如何使用Redis事务？

1. 开始事务，使用`MULTI`
2. 命令入队，批量操作Redis命令，按照先进先出顺序
3. 执行事务，使用`EXEC`

## 6.3 Redis事务支持原子性吗？

不支持！Redis事务在运行错误的情况下，除了执行过程中出现错误的命令外，其他命令都能正常正常执行。并且Redis事务是不支持回滚操作的，因此，Redis是不支持原子性。

## 6.4 Redis事务支持持久性吗？

支持！Redis支持三种持久化方式：RDB、AOF和混合持久化。

# 七、Redis性能优化

## 7.1 使用批量操作减少网络传输

一个Redis命令的执行可以简化为4步：

1. 发送命令；
2. 命令排队；
3. 命令执行；
4. 返回结果。

其中第一步和第四步耗费时间之和成为RTT（往返时间），也即数据在网络上传输的时间。

使用批量操作可以减少网络传输次数，进而有效减小网络开销，大幅减少RTT。

## 7.2 大量key集中过期的问题

对于过期的key，Redis采用的是**定期删除+惰性删除**策略。

那么在定期删除的过程中，如果遇到大量key集中过期问题怎么解决呢？

1. 给key设置随机过期时间。
2. 开启lazy-free（惰性删除/延迟释放）。让Redis采用异步方式延迟释放key使用的内存，将该操作交给单独的子线程处理，避免阻塞主线程。

## 7.3 Redis bigkey

### 7.3.1 什么是bigkey？

简单来说，如果一个key对应的value所占用的内存较大，那么这个key就可以看做是bigkey。

那具体要占多大才算大呢？

String类型的value超过10kb，符合类型的value包含的元素超过5000个。

### 7.3.2 bigkey有什么危害？

消耗更多的内存空间、对性能有较大的影响。

### 7.3.3 如何发现bigkey？

1. 使用Redis自带的`--bigkeys`参数来查找。
2. 分析RDB文件

## 7.4 Redis内存碎片

### 7.4.1 什么是内存碎片？

简单来说，内存碎片就是不可用的空闲内存。

举个例子：操作系统为你分配了 32 字节的连续内存空间，而你存储数据实际只需要使用 24 字节内存空间，那这多余出来的 8 字节内存空间如果后续没办法再被分配存储其他数据的话，就可以被称为内存碎片。

![内存碎片](https://oss.javaguide.cn/github/javaguide/memory-fragmentation.png)

### 7.4.2 为什么会有Redis内存碎片？

1. Redis存储数据的时候想操作系统申请的内存空间可能会大于实际需求的存储空间。（要多了）
2. 频繁修改Redis中的数据。

### 7.4.3 如何查看Redis内存碎片信息？

可以使用`info memory`命令查看Redis内存相关信息。

Redis内存碎片率的计算公式：

`mem_fragmentation_ratio` （内存碎片率）= `used_memory_rss` (操作系统实际分配给 Redis 的物理内存空间大小)/ `used_memory`(Redis 内存分配器为了存储数据实际申请使用的内存空间大小)

**多大的内存碎片率才需要清理呢？**

通常情况下，我们认为 `mem_fragmentation_ratio > 1.5` 的话才需要清理内存碎片。 `mem_fragmentation_ratio > 1.5` 意味着你使用 Redis 存储实际大小 2G 的数据需要使用大于 3G 的内存。

### 7.4.4 如何清理Redis内存碎片？

Redis4.0-RC3 版本以后自带了内存整理，可以避免内存碎片率过大的问题。

直接通过 `config set` 命令将 `activedefrag` 配置项设置为 `yes` 即可。

# 八、Redis生产问题

## 8.1 缓存穿透

### 8.1.1 什么是缓存穿透？

简单来说，就是大量请求的key是不合理的，**根本不存在于缓存中，也不存在与数据库中**。

这就导致这些请求直接到了数据库上，根本没有经过缓存这一层，对数据库造成了巨大的压力，甚至会直接宕机。

![缓存穿透](https://oss.javaguide.cn/github/javaguide/database/redis/redis-cache-penetration.png)

举个例子：某个黑客故意制造一些非法的 key 发起大量请求，导致大量请求落到数据库，结果数据库上也没有查到对应的数据。也就是说这些请求最终都落到了数据库上，对数据库造成了巨大的压力。

### 8.1.2 如何解决缓存穿透？

最基本的就是要做好参数校验，一些不合法的参数请求直接抛出异常给客户端。

#### 1. 缓存无效key

如果缓存和数据库都查不到某个 key 的数据就写一个到 Redis 中去并设置过期时间，这种方式可以解决请求的 key 变化不频繁的情况。

#### 2. 布隆过滤器

通过它我们可以非常方便地判断一个给定数据是否存在于海量数据中。

具体是这样做的：把所有可能存在的请求的值都存放在布隆过滤器中，当用户请求过来，先判断用户发来的请求的值是否存在于布隆过滤器中。不存在的话，直接返回请求参数错误信息给客户端，存在的话才会走下面的流程。

![加入布隆过滤器之后的缓存处理流程图](https://oss.javaguide.cn/github/javaguide/database/redis/redis-cache-penetration-bloom-filter.png)

但是，需要注意的是布隆过滤器可能会存在误判的情况。

总结： **布隆过滤器说某个元素存在，小概率会误判。布隆过滤器说某个元素不在，那么这个元素一定不在。**

布隆过滤器的原理：

1. 使用布隆过滤器中的哈希函数对元素值进行计算，得到哈希值（有几个哈希函数得到几个哈希值）。
2. 根据得到的哈希值，在位数组中把对应下标的值置为1.

当需要判断一个元素是否存在于布隆过滤器的时候：

1. 对给定元素再次进行相同的哈希计算；
2. 得到值后判断位数组中的每个元素是否都为1，如果值都为1，那说明这个值在布隆过滤器中，如果存在一个值不为1，说明该元素不在。

所以，一定会出现不同的字符串可能哈希计算出的位置相同，所以会导致说某个元素存在，但其实不存在。

## 8.2 缓存击穿

### 8.2.1 什么是缓存击穿？

请求的 key 对应的是 **热点数据** ，该数据 **存在于数据库中，但不存在于缓存中（通常是因为缓存中的那份数据已经过期）** 。这就可能会导致瞬时大量的请求直接打到了数据库上，对数据库造成了巨大的压力，可能直接就被这么多请求弄宕机了。

![缓存击穿](https://oss.javaguide.cn/github/javaguide/database/redis/redis-cache-breakdown.png)

举个例子 ：秒杀进行过程中，缓存中的某个秒杀商品的数据突然过期，这就导致瞬时大量对该商品的请求直接落到数据库上，对数据库造成了巨大的压力。

### 8.2.2 如何解决缓存击穿？

- 设置热点数据永不过期或过期时间较长。
- 针对热点数据提前预热，将其存入缓存中，并设置合理的过期时间。比如秒杀场景中，数据在秒杀结束前都不会过期。
- 请求数据库写数据到缓存之前，先获取互斥锁，保证只有一个请求会落到数据库上，减少数据库的压力。

## 8.3 缓存穿透和缓存击穿有什么区别？

缓存穿透中，请求的key既不存在于缓存中，也不在数据库中。

缓存击穿中，请求的key对应的是热点数据，存在于数据库中，但是不存在于缓存中。

## 8.4 缓存雪崩

### 8.4.1 什么是缓存雪崩？

缓存在同一时间大面积失效，导致大量的请求直接落到数据库上，对数据库造成巨大压力。

![缓存雪崩](https://oss.javaguide.cn/github/javaguide/database/redis/redis-cache-avalanche.png)

举个例子 ：数据库中的大量数据在同一时间过期，这个时候突然有大量的请求需要访问这些过期的数据。这就导致大量的请求直接落到数据库上，对数据库造成了巨大的压力。

### 8.4.2 如何解决缓存雪崩？

#### 1. 针对Redis服务不可用的情况

1. 采用Redis集群，避免单机出问题整个缓存服务都无法使用的情况。
2. 限流，避免同时处理大量的请求。

#### 2. 针对热点缓存失效的情况

1. 设置不同的失效时间，比如随机设置缓存失效时间。
2. 缓存永不失效（不推荐）。
3. 设置二级缓存。

## 8.5 缓存击穿和缓存雪崩有什么区别？

两者比较类似，缓存雪崩的原因是缓存中大量或所有数据失效，缓存击穿导致的原因主要是某个热点数据不存在于缓存中。

## 8.6 怎么保证Redis和Mysql双写一致性？

有两种情况，分别是强一致性情况和允许延迟一致性情况。

对于要求强一致性的业务，可以采用redisson提供的读写锁来实现双写一致性；

- 读锁可以使其他线程共享读操作；
- 写锁可以拒绝其他线程的写操作；

对于允许延迟一致性的业务，可以采用异步的方案同步数据；

- 使用MQ中间件，更新数据后，通知缓存删除；
- 利用canel中间件，可以不修改业务代码，通过读取binlog数据更新缓存；

# 九、Redis主从复制

## 9.1 什么是主从复制？

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点(master/leader)，后者称为从节点(slave/follower)； 

数据的复制是单向的，只能由主节点到从节点。Master 以写为主，Slave 以读为主。默认情况下，每台 Redis 服务器都是主节点；且一个主节点可以有多个从节点(或没有从节点)，但一个从节点只能有一个主节点。

## 9.2 主从复制的作用？

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复。
3. 负载均衡：由主节点提供写服务、从节点提供读服务，分担服务器负载，提高Redis服务器的并发量。
4. 集群基础：主从复制还是哨兵和集群能够实施的基础。

## 9.3 一主二从

主从复制，读写分离！ 80% 的情况下都是在进行读操作！减缓服务器的压力！架构中经常使用！ 一主二从！主机可以写，从机不能写只能读！主机中的所有信息和数据，都会自动被从机保存！

 Slave 启动成功连接到 master 后会发送一个 sync 同步命令 Master 接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master 将传送 整个数据文件到 slave，并完成一次完全同步。

### 9.3.1 数据的同步方式： 

1. 全量复制：用于初次复制或其它无法进行部分复制的情况，将主节点中的所有数据都发送给从节点。当数据量过大的时候，会造成很大的网络开销。
2. 增量复制：只复制新增的数据，也就是 master 变化不大，被更改的指令都保存在内存时，会触发增量复制。

## 9.4 哨兵模式

哨兵模式是一种特殊的模式，首先 Redis 提供了哨兵的命令，哨兵是一个 独立的进程，作为进程，它会独立运行。

其原理是**哨兵通过发送命令，等待 Redis 服务器响应，从而监控运行的多个 Redis 实例**。 如果故障了**根据投票数自动将从库转换为主库**。

![image-20230504120300238](pictures\image-20230504120300238.png)

### 9.4.1 优点

1. 哨兵集群，基于主从复制模式，拥有所有主从配置的优点。
2. 主从可以切换，故障可以转移，系统可用性更好。
3. 哨兵模式就是主从模式升级，手动到自动。

### 9.4.2 缺点

1. Redis不好在线扩容，一旦集群容量达到上线，扩容十分麻烦。
2. 哨兵模式配置十分麻烦。
