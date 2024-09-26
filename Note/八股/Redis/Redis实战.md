### 怎么实现动态缓存热点数据？

在电商平台场景中，要求只缓存用户经常访问的 Top 1000 商品，可以通过 Redis 的**有序集合（Sorted Set）ZSet** 来实现。使用 `zadd` 和 `zrange` 等方法，可以维护一个排序队列，系统能够根据商品访问时间来更新队列，定期过滤排名靠后的商品，并从数据库中随机读取新商品加入队列。

#### 1. **排序队列的实现**

在 Redis 中，有序集合是一个很好的工具，它可以为每个元素分配一个分数（score），并根据这个分数自动排序。在这个例子中，我们可以用有序集合来保存商品 ID，并用商品的最近访问时间（或其他相关权重）作为分数。
##### **zadd 方法**：

`zadd` 命令可以将一个元素（商品 ID）插入到有序集合中，并指定它的分数（score）。如果该商品已经存在，那么它的分数会更新。

```
ZADD product_rankings <timestamp> <product_id>
```

- `product_rankings`：存储商品的有序集合名称。
- `<timestamp>`：可以使用商品的最后访问时间或相关分数，更新访问时间时用当前的 Unix 时间戳。
- `<product_id>`：商品的唯一标识符。

每当有用户访问某个商品时，系统会用 `zadd` 命令将商品 ID 插入到有序集合中，如果商品已经存在，则更新它的分数（最近访问时间）。

##### **更新排序队列**：

当用户访问某个商品时，可以通过 `zadd` 命令更新该商品的分数。使用当前时间戳或一个动态分数，确保最近访问的商品排在队列前面。

```
ZADD product_rankings <current_timestamp> <product_id>
```

#### 2. **定期过滤掉排名靠后的商品**

为了控制缓存的大小，系统需要定期从有序集合中删除那些排名靠后的商品（即最近访问次数较少或时间较早的商品）。

##### **zremrangebyrank 方法**：

`zremrangebyrank` 命令可以删除有序集合中排名在一定范围内的元素。我们可以使用它来删除排名最后的 200 个商品。

```
ZREMRANGEBYRANK product_rankings 0 199
```

- `product_rankings`：有序集合的名称。
- `0 199`：删除队列中排名最靠后的 200 个商品。

这样做可以有效地移除那些很久没有被访问过的商品，从而为新的商品腾出空间。

#### 3. **从数据库中随机读取 200 个商品并加入队列**

在清理掉最后 200 个商品后，可以从数据库中随机读取 200 个商品，并将这些商品重新插入到有序集合中。这个操作可以通过 Redis 的 `zadd` 命令完成。

##### **zadd 方法**：

将新从数据库中获取的 200 个商品逐个添加到队列中，分配一个合适的分数（如随机数或者当前时间）。


```
ZADD product_rankings <random_score> <new_product_id>
```

- `<random_score>`：可以用一个随机的权重值，或者直接使用当前的时间戳。
- `<new_product_id>`：从数据库中读取的商品 ID。

#### 4. **从队列中获取商品 ID**

当用户发出请求时，系统首先从队列中获取商品 ID。可以使用 `zrange` 命令获取队列中商品的 ID。

##### **zrange 方法**：

`zrange` 命令可以从有序集合中按顺序获取商品 ID。例如，获取队列中的前 1000 个商品：


```
ZRANGE product_rankings 0 999
```

- `product_rankings`：有序集合的名称。
- `0 999`：获取有序集合中排名前 1000 个商品的 ID。

#### 5. **从另一个缓存数据结构中读取实际商品信息**

当商品 ID 被命中时，系统可以使用另一个缓存数据结构（如 Redis 的 `hash` 或 `string` 类型）存储实际的商品信息。商品 ID 将用作键，商品的具体信息作为值，存储在缓存中。

##### **GET 命令**：

可以使用 `GET` 命令从 Redis 中读取商品的详细信息：

```
GET product_info:<product_id>
```

- `product_info:<product_id>`：商品信息的键，`product_id` 是商品的唯一标识符。

如果命中缓存，则返回商品的详细信息；如果没有命中，可以从数据库读取并将其缓存起来。

#### 6. **完整流程总结**

1. 用户访问某商品时，通过 `zadd` 更新商品的访问时间（分数）到有序集合 `product_rankings` 中。
2. 系统会定期使用 `zremrangebyrank` 命令删除队列中排名最后的 200 个商品。
3. 随后，从数据库中随机读取 200 个新商品，使用 `zadd` 将这些商品插入到 `product_rankings` 中。
4. 用户请求时，首先通过 `zrange` 从队列中获取商品 ID。
5. 如果商品 ID 在队列中命中，则通过 `GET` 从另一个缓存数据结构中获取实际商品信息；如果没有命中，则从数据库获取，并将商品信息缓存起来。

#### 7. **代码示例**

```
# 1. 添加或更新商品的访问记录 ZADD product_rankings <timestamp> <product_id>  

# 2. 定期删除排名最后的200个商品 ZREMRANGEBYRANK product_rankings 0 199  

# 3. 从数据库读取200个新商品并插入 ZADD product_rankings <random_score> <new_product_id>  

# 4. 查询排名靠前的商品ID ZRANGE product_rankings 0 999  

# 5. 从缓存中获取商品详细信息 GET product_info:<product_id>`
```


###  Redis 如何实现延迟队列？

延迟队列是指把当前要做的事情，往后推迟一段时间再做。延迟队列的常见使用场景有以下几种：

- 在淘宝、京东等购物平台上下单，超过一定时间未付款，订单会自动取消；
- 打车的时候，在规定时间没有车主接单，平台会取消你的单并提醒你暂时没有车主接单；
- 点外卖的时候，如果商家在10分钟还没接单，就会自动取消订单；

在 Redis 可以使用**有序集合（ZSet）** 的方式来实现**延迟消息队列**的，ZSet 有一个 **Score 属性**可以用来存储延迟执行的时间。

使用 zadd score1 value1 命令就可以一直往内存中生产消息。再利用 zrangebysocre 查询符合条件的所有待处理的任务， 通过循环执行队列任务即可。

##### **延迟队列的具体执行流程：**

1. **插入延迟消息**：通过 `ZADD` 命令将消息插入 ZSet，`score` 设置为未来的时间戳，代表延迟多少秒执行。
2. **定期消费消息**：通过定时任务，每隔固定时间（如 1 秒）调用 `ZRANGEBYSCORE` 命令，获取当前时间点之前到期的消息。
3. **处理消息**：对查询出来的消息进行处理。
4. **移除消息**：处理完消息后，通过 `ZREM` 命令将消息从 ZSet 中删除，确保不重复处理。


![](https://cdn.xiaolincoding.com/gh/xiaolincoder/redis/%E5%85%AB%E8%82%A1%E6%96%87/%E5%BB%B6%E8%BF%9F%E9%98%9F%E5%88%97.png)

### Redis 的大 key 如何处理？

> 什么是 Redis 大 key？

大 key 并不是指 key 的值很大，而是 **key 对应的 value 很大**。

一般而言，下面这两种情况被称为大 key：

- String 类型的值大于 10 KB；
- Hash、List、Set、ZSet 类型的元素的个数超过 5000个；

> 大 key 会造成什么问题？

大 key 会带来以下四种影响：

- **客户端超时阻塞**。由于 Redis 执行命令是单线程处理，然后在操作大 key 时会比较耗时，那么就会阻塞 Redis，从客户端这一视角看，就是很久很久都没有响应。
- **引发网络阻塞**。每次获取大 key 产生的网络流量较大，如果一个 key 的大小是 1 MB，每秒访问量为 1000，那么每秒会产生 1000MB 的流量，这对于普通千兆网卡的服务器来说是灾难性的。
- **阻塞工作线程**。如果使用 del 删除大 key 时，会阻塞工作线程，这样就没办法处理后续的命令。
- **内存分布不均**。集群模型在 slot 分片均匀情况下，会出现数据和查询倾斜情况，部分有大 key 的 Redis 节点占用内存多，QPS 也会比较大。

> 如何找到大 key ？

_**1、redis-cli --bigkeys 查找大key**_

可以通过 redis-cli --bigkeys 命令查找大 key：

```
redis-cli -h 127.0.0.1 -p6379 -a "password" -- bigkeys
```

使用的时候注意事项：

- 最好选择在**从节点**上执行该命令。因为主节点上执行时，会阻塞主节点；
- 如果没有从节点，那么可以选择在 Redis 实例业务压力的低峰阶段进行扫描查询，以免影响到实例的正常运行；或者可以使用 -i 参数控制扫描间隔，避免长时间扫描降低 Redis 实例的性能。

该方式的不足之处：

- 这个方法只能返回每种类型中最大的那个 bigkey，无法得到大小排在前 N 位的 bigkey；
- 对于集合类型来说，这个方法只统计集合元素个数的多少，而不是实际占用的内存量。但是，一个集合中的元素个数多，并不一定占用的内存就多。因为，有可能每个元素占用的内存很小，这样的话，即使元素个数有很多，总内存开销也不大；

_**2、使用 SCAN 命令查找大 key**_

使用 SCAN 命令对数据库扫描，然后用 TYPE 命令获取返回的每一个 key 的类型。

对于 String 类型，可以直接使用 STRLEN 命令获取字符串的长度，也就是占用的内存空间字节数。

对于集合类型来说，有两种方法可以获得它占用的内存大小：

- 如果能够预先从业务层知道集合元素的平均大小，那么，可以使用下面的命令获取集合元素的个数，然后乘以集合元素的平均大小，这样就能获得集合占用的内存大小了。List 类型：`LLEN` 命令；Hash 类型：`HLEN` 命令；Set 类型：`SCARD` 命令；Sorted Set 类型：`ZCARD` 命令；
- 如果不能提前知道写入集合的元素大小，可以使用 `MEMORY USAGE` 命令（需要 Redis 4.0 及以上版本），查询一个键值对占用的内存空间。

_**3、使用 RdbTools 工具查找大 key**_

使用 RdbTools 第三方开源工具，可以用来解析 Redis 快照（RDB）文件，找到其中的大 key。

比如，下面这条命令，将大于 10 kb 的  key  输出到一个表格文件。

```
rdb dump.rdb -c memory --bytes 10240 -f redis.csv
```

> 如何删除大 key？

删除操作的本质是要释放键值对占用的内存空间，不要小瞧内存的释放过程。

释放内存只是第一步，为了更加高效地管理内存空间，在应用程序释放内存时，操作系统需要把释放掉的内存块插入一个空闲内存块的链表，以便后续进行管理和再分配。这个过程本身需要一定时间，而且会阻塞当前释放内存的应用程序。

所以，如果一下子释放了大量内存，空闲内存块链表操作时间就会增加，相应地就会造成 Redis 主线程的阻塞，如果主线程发生了阻塞，其他所有请求可能都会超时，超时越来越多，会造成 Redis 连接耗尽，产生各种异常。

因此，删除大 key 这一个动作，我们要小心。具体要怎么做呢？这里给出两种方法：

- 分批次删除
- 异步删除（Redis 4.0版本以上）

_**1、分批次删除**_

对于**删除大 Hash**，使用 `hscan` 命令，每次获取 100 个字段，再用 `hdel` 命令，每次删除 1 个字段。

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.ScanResult;

public class RedisDeleteLargeHash {
    public static void delLargeHash() {
        try (Jedis jedis = new Jedis("redis-host1", 6379)) {
            String largeHashKey = "xxx"; // 要删除的大 hash 键名
            String cursor = "0";

            do {
                // 使用 hscan 命令，每次获取 100 个字段
                ScanResult<java.util.Map.Entry<String, String>> result = jedis.hscan(largeHashKey, cursor, new redis.clients.jedis.params.ScanParams().count(100));
                cursor = result.getCursor();

                for (java.util.Map.Entry<String, String> item : result.getResult()) {
                    // 再用 hdel 命令，每次删除 1 个字段
                    jedis.hdel(largeHashKey, item.getKey());
                }
            } while (!cursor.equals("0"));
        }
    }
}
```

对于**删除大 List**，通过 `ltrim` 命令，每次删除少量元素。

```java
import redis.clients.jedis.Jedis;

public class RedisDeleteLargeList {
    public static void delLargeList() {
        try (Jedis jedis = new Jedis("redis-host1", 6379)) {
            String largeListKey = "xxx"; // 要删除的大 list 的键名
            
            while (jedis.llen(largeListKey) > 0) {
                // 每次只删除最右 100 个元素
                jedis.ltrim(largeListKey, 0, -101);
            }
        }
    }
}
```

对于**删除大 Set**，使用 `sscan` 命令，每次扫描集合中 100 个元素，再用 `srem` 命令每次删除一个键。

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.ScanResult;

public class RedisDeleteLargeSet {
    public static void delLargeSet() {
        try (Jedis jedis = new Jedis("redis-host1", 6379)) {
            String largeSetKey = "xxx"; // 要删除的大 set 的键名
            String cursor = "0";

            do {
                // 使用 sscan 命令，每次扫描集合中 100 个元素
                ScanResult<String> result = jedis.sscan(largeSetKey, cursor, new redis.clients.jedis.params.ScanParams().count(100));
                cursor = result.getCursor();

                for (String item : result.getResult()) {
                    // 再用 srem 命令每次删除一个键
                    jedis.srem(largeSetKey, item);
                }
            } while (!cursor.equals("0"));
        }
    }
}
```

对于**删除大 ZSet**，使用 `zremrangebyrank` 命令，每次删除 top 100个元素。

```java
import redis.clients.jedis.Jedis;

public class RedisDeleteLargeSortedSet {
    public static void delLargeSortedSet() {
        try (Jedis jedis = new Jedis("redis-host1", 6379)) {
            String largeSortedSetKey = "xxx"; // 要删除的大 ZSet 的键名
            
            while (jedis.zcard(largeSortedSetKey) > 0) {
                // 使用 zremrangebyrank 命令，每次删除 top 100 个元素
                jedis.zremrangeByRank(largeSortedSetKey, 0, 99);
            }
        }
    }
}
```

_**2、异步删除**_

从 Redis 4.0 版本开始，可以采用**异步删除**法，**用 unlink 命令代替 del 来删除**。

这样 Redis 会将这个 key 放入到一个异步线程中进行删除，这样不会阻塞主线程。

除了主动调用 unlink 命令实现异步删除之外，我们还可以通过配置参数，达到某些条件的时候自动进行异步删除。

主要有 4 种场景，默认都是关闭的：

```
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del
noslave-lazy-flush no
```

它们代表的含义如下：

- lazyfree-lazy-eviction：表示当 Redis 运行内存超过 maxmeory 时，是否开启 lazy free 机制删除；
    
- lazyfree-lazy-expire：表示设置了过期时间的键值，当过期之后是否开启 lazy free 机制删除；
    
- lazyfree-lazy-server-del：有些指令在处理已存在的键时，会带有一个隐式的 del 键的操作，比如 rename 命令，当目标键已存在，Redis 会先删除目标键，如果这些目标键是一个 big key，就会造成阻塞删除的问题，此配置表示在这种场景中是否开启 lazy free 机制删除；
    
- slave-lazy-flush：针对 slave (从节点) 进行全量数据同步，slave 在加载 master 的 RDB 文件前，会运行 flushall 来清理自己的数据，它表示此时是否开启 lazy free 机制删除。
    
建议开启其中的 lazyfree-lazy-eviction、lazyfree-lazy-expire、lazyfree-lazy-server-del 等配置，这样就可以有效的提高主线程的执行效率。

### Redis 管道有什么用？

管道技术（Pipeline）是**客户端**提供的一种**批处理技术**，用于一次处理多个 Redis 命令，从而提高整个交互的性能。

普通命令模式，如下图所示：

![|550](https://cdn.xiaolincoding.com/gh/xiaolincoder/redis/%E5%85%AB%E8%82%A1%E6%96%87/%E6%99%AE%E9%80%9A%E5%91%BD%E4%BB%A4%E6%A8%A1%E5%BC%8F.jpg)

管道模式，如下图所示：

![|550](https://cdn.xiaolincoding.com/gh/xiaolincoder/redis/%E5%85%AB%E8%82%A1%E6%96%87/%E7%AE%A1%E9%81%93%E6%A8%A1%E5%BC%8F.jpg)

使用**管道技术可以解决多个命令执行时的网络等待**，它是把多个命令整合到一起发送给服务器端处理之后统一返回给客户端，这样就免去了每条命令执行后都要等待的情况，从而有效地提高了程序的执行效率。

但使用管道技术也要注意避免发送的命令过大，或管道内的数据太多而导致的网络阻塞。

要注意的是，管道技术本质上是客户端提供的功能，而非 Redis 服务器端的功能。

### Redis 事务支持回滚吗？

MySQL 在执行事务时，会提供回滚机制，当事务执行发生错误时，事务中的所有操作都会撤销，已经修改的数据也会被恢复到事务执行前的状态。

**Redis 中并没有提供回滚机制**，虽然 Redis 提供了 **DISCARD 命令**，但是这个命令只能用来**主动放弃事务执行**，把暂存的**命令队列清空**，起不到回滚的效果。

下面是 DISCARD 命令用法，当你使用 **MULTI** 命令开始一个事务后，所有后续的命令会被暂存起来，直到你调用 **EXEC** 执行这些命令，或者调用 **DISCARD** 放弃它们：

```
#读取 count 的值
127.0.0.1:6379> GET count
"1"
#开启事务
127.0.0.1:6379> MULTI 
OK
#发送事务的第一个操作，对count减1
127.0.0.1:6379> DECR count
QUEUED
#执行DISCARD命令，主动放弃事务
127.0.0.1:6379> DISCARD
OK
#再次读取a:stock的值，值没有被修改
127.0.0.1:6379> GET count
"1"
```

事务执行过程中，如果命令入队时没报错，而事务提交后，实际执行时报错了，正确的命令依然可以正常执行，所以这可以看出 **Redis 并不一定保证原子性**（原子性：事务中的命令要不全部成功，要不全部失败）。

比如下面这个例子：

```
#获取name原本的值
127.0.0.1:6379> GET name
"xiaolin"
#开启事务
127.0.0.1:6379> MULTI
OK
#设置新值
127.0.0.1:6379(TX)> SET name xialincoding
QUEUED
#注意，这条命令是错误的
# expire 过期时间正确来说是数字，并不是‘10s’字符串，但是还是入队成功了
127.0.0.1:6379(TX)> EXPIRE name 10s
QUEUED
#提交事务，执行报错
#可以看到 set 执行成功，而 expire 执行错误。
127.0.0.1:6379(TX)> EXEC
1) OK
2) (error) ERR value is not an integer or out of range
#可以看到，name 还是被设置为新值了
127.0.0.1:6379> GET name
"xialincoding"
```

> 为什么Redis 不支持事务回滚？

Redis [官方文档](https://redis.io/topics/transactions)的解释如下：

![](https://cdn.xiaolincoding.com/gh/xiaolincoder/redis/%E5%85%AB%E8%82%A1%E6%96%87/redis%E5%AE%98%E6%96%B9%E8%A7%A3%E9%87%8A%E5%9B%9E%E6%BB%9A.png)

大概的意思是，作者不支持事务回滚的原因有以下两个：

- 他认为 Redis 事务的执行时，错误通常都是编程错误造成的，这种错误通常只会出现在开发环境中，而很少会在实际的生产环境中出现，所以他认为没有必要为 Redis 开发事务回滚功能；
- 不支持事务回滚是因为这种复杂的功能和 Redis 追求的简单高效的设计主旨不符合。

这里不支持事务回滚，指的是不支持事务运行时错误的事务回滚。

### 如何用 Redis 实现分布式锁的？

分布式锁是用于分布式环境下**并发控制**的一种机制，用于控制某个资源在同一时刻只能被一个应用所使用。如下图所示：

![](https://cdn.xiaolincoding.com/gh/xiaolincoder/redis/%E5%85%AB%E8%82%A1%E6%96%87/%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81.jpg)

Redis 本身可以被多个客户端共享访问，正好就是一个共享存储系统，可以用来保存分布式锁，而且 Redis 的读写性能高，可以应对高并发的锁操作场景。

Redis 的 SET 命令有个 **NX 参数**可以实现「**key不存在才插入**」，所以可以用它来实现分布式锁：

- 如果 key 不存在，则显示插入成功，可以用来表示加锁成功；
- 如果 key 存在，则会显示插入失败，可以用来表示加锁失败。

基于 Redis 节点实现分布式锁时，对于加锁操作，我们需要满足三个条件。

- 加锁包括了读取锁变量、检查锁变量值和设置锁变量值三个操作，但需要以**原子操作的方式**完成，所以，我们使用 SET 命令带上 NX 选项来实现加锁；
- 锁变量需要**设置过期时间**，以免客户端拿到锁后发生异常，导致锁一直无法释放，所以，我们在 SET 命令执行时加上 EX/PX 选项，设置其过期时间；
- 锁变量的值需要能区分来自不同客户端的加锁操作，以免在释放锁时，出现误释放操作，所以，我们使用 SET 命令设置锁变量值时，每个客户端设置的值是一个唯一值，用于**标识客户端**；

满足这三个条件的分布式命令如下：

```
SET lock_key unique_value NX PX 10000 
```

- lock_key 就是 key 键；
- unique_value 是客户端生成的唯一的标识，区分来自不同客户端的锁操作；
- NX 代表只在 lock_key 不存在时，才对 lock_key 进行设置操作；
- PX 10000 表示设置 lock_key 的过期时间为 10s，这是为了避免客户端发生异常而无法释放锁。

而解锁的过程就是将 lock_key 键删除（del lock_key），但不能乱删，要保证执行操作的客户端就是加锁的客户端。所以，解锁的时候，我们要先判断锁的 unique_value 是否为加锁客户端，是的话，才将 lock_key 键删除。

可以看到，解锁是有两个操作，这时就需要 Lua 脚本来保证解锁的原子性，因为 **Redis 在执行 Lua 脚本时，可以以原子性的方式执行**，保证了锁释放操作的原子性。

```lua
// 释放锁时，先比较 unique_value 是否相等，避免锁的误释放
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

这样一来，就通过使用 SET 命令和 Lua 脚本在 Redis 单节点上完成了分布式锁的加锁和解锁。

> 基于 Redis 实现分布式锁有什么优缺点？

基于 Redis 实现分布式锁的**优点**：

1. **性能高效**（这是选择缓存实现分布式锁最核心的出发点）。
2. 实现方便。很多研发工程师选择使用 Redis 来实现分布式锁，很大成分上是因为 Redis 提供了 setnx 方法，实现分布式锁很方便。
3. **避免单点故障**（因为 Redis 是跨集群部署的，自然就避免了单点故障）。

基于 Redis 实现分布式锁的**缺点**：

- **超时时间不好设置**。如果锁的超时时间设置过长，会影响性能，如果设置的超时时间过短会保护不到共享资源。比如在有些场景中，一个线程 A 获取到了锁之后，由于业务代码执行时间可能比较长，导致超过了锁的超时时间，自动失效，注意 A 线程没执行完，后续线程 B 又意外的持有了锁，意味着可以操作共享资源，那么两个线程之间的共享资源就没办法进行保护了。
    - **那么如何合理设置超时时间呢？** 我们可以基于续约的方式设置超时时间：先给锁设置一个超时时间，然后启动一个守护线程，让守护线程在一段时间后，重新设置这个锁的超时时间。实现方式就是：写一个守护线程，然后去判断锁的情况，当锁快失效的时候，再次进行续约加锁，当主线程执行完成后，销毁续约锁即可，不过这种方式实现起来相对复杂。
- **Redis 主从复制模式中的数据是异步复制的，这样导致分布式锁的不可靠性**。如果在 Redis 主节点获取到锁后，在没有同步到其他节点时，Redis 主节点宕机了，此时新的 Redis 主节点依然可以获取锁，所以多个应用服务就可以同时获取到锁。

> Redis 如何解决集群情况下分布式锁的可靠性？

为了保证集群环境下分布式锁的可靠性，Redis 官方已经设计了一个**分布式锁算法 Redlock**（红锁）。

它是基于**多个 Redis 节点**的分布式锁，即使有节点发生了故障，锁变量仍然是存在的，客户端还是可以完成锁操作。官方推荐是至少部署 5 个 Redis 节点，而且都是主节点，它们之间没有任何关系，都是一个个孤立的节点。

Redlock 算法的基本思路，**是让客户端和多个独立的 Redis 节点依次请求申请加锁，如果客户端能够和半数以上的节点成功地完成加锁操作，那么我们就认为，客户端成功地获得分布式锁，否则加锁失败**。

这样一来，即使有某个 Redis 节点发生故障，因为锁的数据在其他节点上也有保存，所以客户端仍然可以正常地进行锁操作，锁的数据也不会丢失。

Redlock 算法加锁三个过程：

- 第一步是，客户端获取当前时间（t1）。
- 第二步是，客户端按顺序依次向 N 个 Redis 节点执行加锁操作：
    - 加锁操作使用 SET 命令，带上 NX，EX/PX 选项，以及带上客户端的唯一标识。
    - 如果某个 Redis 节点发生故障了，为了保证在这种情况下，Redlock 算法能够继续运行，我们需要给「加锁操作」设置一个超时时间（不是对「锁」设置超时时间，而是**对「加锁操作」设置超时时间**），加锁操作的超时时间需要远远地小于锁的过期时间，一般也就是设置为几十毫秒。
- 第三步是，一旦客户端从超过半数（大于等于 N/2+1）的 Redis 节点上成功获取到了锁，就再次获取当前时间（t2），然后计算计算整个**加锁过程的总耗时**（t2-t1）。如果 t2-t1 < 锁的过期时间，此时，认为客户端加锁成功，否则认为加锁失败。

可以看到，加锁成功要同时满足两个条件（_简述：如果有超过半数的 Redis 节点成功的获取到了锁，并且总耗时没有超过锁的有效时间，那么就是加锁成功_）：

- 条件一：客户端从超过半数（大于等于 N/2+1）的 Redis 节点上成功获取到了锁；
- 条件二：客户端从大多数节点获取锁的总耗时（t2-t1）小于锁设置的过期时间。

加锁成功后，客户端需要重新计算这把锁的有效时间，计算的结果是「锁最初设置的过期时间」减去「客户端从大多数节点获取锁的总耗时（t2-t1）」。如果计算的结果已经来不及完成共享数据的操作了，我们可以释放锁，以免出现还没完成数据操作，锁就过期了的情况。

加锁失败后，客户端向**所有 Redis 节点发起释放锁的操作**，释放锁的操作和在单节点上释放锁的操作一样，只要执行释放锁的 Lua 脚本就可以了。

