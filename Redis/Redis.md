## Redis优点

- Redis是一个开源的key-value存储系统。
- Redis支持多种数据类型
- Redis的操作都是原子性的
- 数据都是缓存在内存中。会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件。
- 实现了master-slave(主从)同步。

## Redis的原子性

所谓**原子**操作是指不会被线程调度机制打断的操作；这种操作一旦开始，就一直运行到结束，中间不会有任何 context switch （切换到另一个线程）。

在单线程中， 能够在单条指令中完成的操作都可以认为是"原子操作"，因为中断只能发生于指令之间。

在多线程中，不能被其它进程（线程）打断的操作就叫原子操作。

Redis单命令的原子性主要得益于Redis的单线程。

## Redis数据类型

#### String

- String类型是二进制安全的。一个Redis中字符串value最多可以是512M

###### 数据结构

- 类似于于Java的ArrayList,采用预分配冗余空间的方式来减少内存的平凡分配

![image-20211003203633620](https://i.loli.net/2021/10/03/41XFHuKpvUlTIjG.png)

内部为当前字符串实际分配的空间capacity一般要高于实际字符串长度len。当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时一次只会多扩1M的空间。需要注意的是字符串最大长度为512M。

#### List

![image-20211003203749318](https://i.loli.net/2021/10/03/dVukrc8yqZaNB2o.png)

单键多值,Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。它的底层实际是个双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差。

###### 数据结构

![image-20211003204412512](https://i.loli.net/2021/10/03/FJV8Z4KDilpmWBb.png)

- List的数据结构为快速链表quickList。
  - 在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，也即是压缩列表。它将所有的元素紧挨着一起存储，分配的是一块连续的内存。
  - 当数据量比较多的时候才会改成quicklist。
- Redis将链表和ziplist结合起来组成了quicklist。也就是将多个ziplist使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

#### Set

- set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以**自动排重**的
- Set是string类型的无序集合。它底层其实是一个value为null的hash表，所以添加，删除，查找的复杂度都是O(1)。

###### 数据结构

内部使用hash结构，所有的value都指向同一个内部值。

#### Hash

- hash 是一个键值对集合。
- hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。类似Java里面的Map<String,Object>

![image-20211003205024362](https://i.loli.net/2021/10/03/iwfMnaVQUgy92Wq.png)

**通过 key(用户ID) + field(属性标签) 就可以操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题**

######  数据结构

Hash类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）。当field-value长度较短且个数较少时，使用ziplist，否则使用hashtable。

#### Zset

- Redis有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。
- 不同之处是有序集合的每个成员都关联了一个**评分（score）**,这个评分（score）被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复了 。
- 访问有序集合的中间元素也是非常快的,因此你能够使用有序集合作为一个没有重复成员的智能列表。

######  数据结构

- zset底层使用了两个数据结构
  - hash，hash的作用就是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到相应的score值。
  - 跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表。

#### Bitmaps

![image-20211003210549198](https://i.loli.net/2021/10/03/MLWoCy2DPtlm5SU.png)

#### HyperLogLog

- HyperLogLog 是用来做基数统计的算法

###### 优点

在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

#### Geospatial

提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。

## 发布和订阅

#### 订阅

subscribe xxx

![image-20211003205919022](https://i.loli.net/2021/10/03/PkZl6fJx41QiC2j.png)

#### 发布

publish xxx something

![image-20211003205924433](https://i.loli.net/2021/10/03/Q7Nos4wvj3Xg8bl.png)

![image-20211003205938383](https://i.loli.net/2021/10/03/mC3V92RyOx6HAQ8.png)

- 只有订阅了才能收到发布的消息

## Redis_Jedis

#### 使用条件

```xml
<dependency>
<groupId>redis.clients</groupId>
<artifactId>jedis</artifactId>
<version>3.2.0</version>
</dependency>
```

```java
Jedis jedis = new Jedis("192.168.137.3",6379);
```

- 连接Redis注意事项
  - 禁用Linux的防火墙：Linux(CentOS7)里执行命令**systemctl stop/disable firewalld.service**
  -   redis.conf中注释掉bind 127.0.0.1 ,然后 protected-mode no

##  Redis与Spring Boot整合

#### 步骤

1. 在pom.xml文件中引入redis相关依赖

   ```xml
   <!-- redis -->
   <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   
   <!-- spring2.X集成redis所需common-pool2-->
   <dependency>
   <groupId>org.apache.commons</groupId>
   <artifactId>commons-pool2</artifactId>
   <version>2.6.0</version>
   </dependency>
   
   ```

2. application.properties配置redis配置

   ```properties
   #Redis服务器地址
   spring.redis.host=192.168.140.136
   #Redis服务器连接端口
   spring.redis.port=6379
   #Redis数据库索引（默认为0）
   spring.redis.database= 0
   #连接超时时间（毫秒）
   spring.redis.timeout=1800000
   #连接池最大连接数（使用负值表示没有限制）
   spring.redis.lettuce.pool.max-active=20
   #最大阻塞等待时间(负数表示没限制)
   spring.redis.lettuce.pool.max-wait=-1
   #连接池中的最大空闲连接
   spring.redis.lettuce.pool.max-idle=5
   #连接池中的最小空闲连接
   spring.redis.lettuce.pool.min-idle=0
   
   ```

3. 添加redis配置类

   ```java
   @EnableCaching
   @Configuration
   public class RedisConfig extends CachingConfigurerSupport {
   
       @Bean
       public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
           RedisTemplate<String, Object> template = new RedisTemplate<>();
           RedisSerializer<String> redisSerializer = new StringRedisSerializer();
           Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
           ObjectMapper om = new ObjectMapper();
           om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
           om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
           jackson2JsonRedisSerializer.setObjectMapper(om);
           template.setConnectionFactory(factory);
   //key序列化方式
           template.setKeySerializer(redisSerializer);
   //value序列化
           template.setValueSerializer(jackson2JsonRedisSerializer);
   //value hashmap序列化
           template.setHashValueSerializer(jackson2JsonRedisSerializer);
           return template;
       }
   
       @Bean
       public CacheManager cacheManager(RedisConnectionFactory factory) {
           RedisSerializer<String> redisSerializer = new StringRedisSerializer();
           Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
   //解决查询缓存转换异常的问题
           ObjectMapper om = new ObjectMapper();
           om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
           om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
           jackson2JsonRedisSerializer.setObjectMapper(om);
   // 配置序列化（解决乱码的问题）,过期时间600秒
           RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                   .entryTtl(Duration.ofSeconds(600))
                   .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                   .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                   .disableCachingNullValues();
           RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                   .cacheDefaults(config)
                   .build();
           return cacheManager;
       }
   }
   
   ```

4. RedisTestController中添加测试方法

   ```java
   @RestController
   @RequestMapping("/redisTest")
   public class RedisTestController {
       @Autowired
       private RedisTemplate redisTemplate;
   
       @GetMapping
       public String testRedis() {
           //设置值到redis
           redisTemplate.opsForValue().set("name","lucy");
           //从redis获取值
           String name = (String)redisTemplate.opsForValue().get("name");
           return name;
       }
   }
   
   ```

## Redis事务

#### 定义

Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

#### 作用

Redis事务的主要作用就是串联多个命令防止别的命令插队。

#### 命令

###### 正确事务的处理方式

从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，直到输入Exec后，Redis会将之前的命令队列中的命令依次执行。组队的过程中可以通过discard来放弃组队。 

![image-20211004123047178](https://i.loli.net/2021/10/04/XpIVo1x4gvPOMeB.png)

###### 组队中命令错误的处理方式

执行时整个的所有队列都会被取消。

![image-20211004123227654](https://i.loli.net/2021/10/04/bEcs3oMl6WuAhx4.png)

###### 执行阶段命令错误的处理方式

只有报错的命令不会被执行，而其他的命令都会执行，不会回滚。

![image-20211004123318947](https://i.loli.net/2021/10/04/gjxHlWGc3As45QF.png)

#### WATCH key [key ...]

在执行multi之前，先执行watch key1 [key2],可以监视一个(或多个) key ，如果在事务执行之前这个(或这些) key被其他命令所改动，那么事务将被打断。

unwatch取消 WATCH 命令对所有 key 的监视

#### Redis事务的三大特性

###### 单独的隔离操作

事务中的所有命令都会序列化,按顺序地执行.事务在执行的过程中,不会被其他客户端发送过来的命令请求所打断.

###### 没有隔离级别的概念

队列中的命令没有提交之前都不会实际执行,因为事务提交前任何指令都不会被执行

###### 不保证原子性

事务中如果有一条命令执行失败,其后的命令仍然会被执行,没有回滚

#### 案例问题解决方案

###### 链接超时问题

- 通过连接池

###### 超卖问题

- 增加乐观锁

###### 库存遗留问题

- 通过LUA脚本

## Redis持久化

#### RDB

###### 定义

在指定的时间间隔内将内存中的数据集快照写入磁盘， 也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里

###### 执行流程

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到 一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。

###### 优点

- 适合大规模的数据回复
- 节省磁盘空间
- 回复速度快

###### 缺点

- Fork的时候,内存中的数据被克隆一份,大致2倍的膨胀性需要考虑
- 虽然Redis在fork时使用了**写时拷贝技术**,但是如果数据庞大时还是比较消耗性能。
- 在备份周期在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就会丢失最后一次快照后的所有修改。

###### 启动与恢复

拷贝备份文件，需要恢复时再拷贝到Redis工作目录下，启动系统即加载。

#### AOF

- AOF和RDB同时开启，系统默认取AOF的数据（数据不会存在丢失）

###### 定义

以日志的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录下来(读操作不记录)， 只许追加文件但不可以改写文件,redis启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

###### 执行流程

- 客户端的请求写命令会被append追加到AOF缓冲区内；

- AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync同步到磁盘的AOF文件中；

  - appendfsync always

    始终同步，每次Redis的写入都会立刻记入日志；性能较差但数据完整性比较好

  - appendfsync everysec

    每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。

  - appendfsync no

    redis不主动进行同步，把同步时机交给操作系统。

- AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量；

  - 当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩， 只保留可以恢复数据的最小指令集.(以更精简的指令回复全部数据)
    - 重写原理:把rdb 的快照，以二级制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作。
    - 重写流程:使用写时复制技术(同RDB)

- Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的；

###### 优势

- 备份机制更稳健，丢失数据概率更低
- 可读的日志文本，通过操作AOF稳健，可以处理误操作

###### 劣势

- 比起RDB占用更多的磁盘空间
- 恢复备份速度要慢。
- 每次读写都同步的话，有一定的性能压力。
- 存在个别Bug，造成恢复不能。

###### 启动与恢复

- 拷贝备份文件，需要恢复时再拷贝到Redis工作目录下，启动系统即加载。

AOF文件损坏，通过/usr/local/bin/redis-check-aof--fix appendonly.aof进行恢复

## 主从复制

- 主从复制的开启，完全是在从节点发起的；不需要我们在主节点做任何事情。

#### 作用

- 读写分离,性能扩展
- 容灾快速回复

![image-20211004162051905](https://i.loli.net/2021/10/04/6J5xNuZLGpE1A4v.png)



#### 一主两从

###### 特点

- 当从机宕机后,再次启动从机后,从机变为独立主机,要想重新加入为从机需要重新配置
- 当主机宕机后,此时从机仍然是从机,重启主机,主机仍然是主机,从机仍然是从机

###### 原理

1. 当从机连接上主机后,从机向主机发送进行数据同步消息 
2. 主机收到从机发送过来同步消息,把主机数据进行持久化,rdb文件,把rdb文件发送到从机,从机拿到rdb文件进行读取
3. 每次主机进行写操作之后,跟从机进行数据同步

#### 薪火相传

- 上一个slave（从机）是下一个slave（从机）的Master（主机）

###### 优点

从机同样可以接收其他从机的连接和同步请求，那么该从机作为了链条中下一个的主机, 可以有效减轻主机的写压力,去中心化降低风险.

###### 缺点

一旦某个从机宕机,后面的从机都无法备份

#### 反客为主

- 当一个master宕机后，后面的slave可以通过 slaveof no one设置自己为主机，其后面的slave不用做任何修改。之前的主机重新连上后，与之前的从机就没有联系了

#### 哨兵模式

- 反客为主的自动版

- 主机宕机后,从机变为主机,若之前的主机恢复则之前的主机变为现在主机的从机

- 从机变为主机的选择条件
  1. 选择优先级靠前的(slave-priority 100，值越小优先级越高)
  2. 选择偏移量大的(从机和主机匹配度最高的)
  3. 选择runid最小的从机(每个redis实例启动后都会随机生成一个40位的runid)

## 集群

#### 定义

Redis 集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

#### slots

不在一个slot下的键值，是不能使用mget,mset等多键操作。

![image-20211004175225536](https://i.loli.net/2021/10/04/GNimhT3OFEwZCzM.png)

可以通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去。

![image-20211004175229042](https://i.loli.net/2021/10/04/GNimhT3OFEwZCzM.png)

查询集群中的值

`cluster getkeysinslot <slot><count> ` 返回 count 个 slot 槽中的键。

#### 集群中Jedis开发

```java
public class JedisClusterTest {
  public static void main(String[] args) { 
     Set<HostAndPort>set =new HashSet<HostAndPort>();
     set.add(new HostAndPort("192.168.31.211",6379));
     JedisCluster jedisCluster=new JedisCluster(set);
     jedisCluster.set("k1", "v1");
     System.out.println(jedisCluster.get("k1"));
  }
}

```

#### 优点

- 实现扩容
- 分摊压力
- 无中心配置相对简单

###### 缺点

- 多键操作是不支持的
- 多键的Redis事务是不被支持的,lua脚本不被支持

## Redis常见问题

#### 缓存穿透

key对应的数据在数据源并不存在，每次针对此key的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源。

###### 解决方案

1. 对空值缓存(无论数据是否存在,都把空结果进行缓存,设置空结果的过期时间会很短)
2. 设置可访问的名单(白名单)
3. 采用布隆过滤器
4. 设置黑名单

#### 缓存击穿

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

###### 解决方案

- 预先设置热门数据
- 实时调整(实时调整)
- 使用锁(效率低)

![image-20211004181724557](https://i.loli.net/2021/10/04/rigJlVUSs7YGh6B.png)

#### 缓存雪崩

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

缓存雪崩与缓存击穿的区别在于这里针对很多key缓存，缓存击穿则是某一个key

###### 解决方案

- 构建多级缓存架构：nginx缓存 + redis缓存 +其他缓存（ehcache等）
- 使用锁或队列：证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况
- 设置过期标志更新缓存:设置提前量,如果过期会触发通知另外的线程在后台去更新实际key的缓存。
- 将缓存失效时间分散开:让每一个key的失效时间不相同

#### 分布式锁

- 使用setnx上锁,通过del释放锁
- 锁一直没释放,设置key过期时间,自动释放(不是原子操作,上锁后突然出现异常,无法设置key过期时间)
- set users 10 nx ex 12(原子操作)

```java
@GetMapping("testLock")
public void testLock(){
    //1获取锁，setne 设置过期时间
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111",3,TimeUnit.SECONDS);
    //2获取锁成功、查询num的值,num判断执行的次数仅供测试
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            return;
        }
        //2.2有值就转成成int
        int num = Integer.parseInt(value+"");
        //2.3把redis的num加1
        redisTemplate.opsForValue().set("num", ++num);
        //2.4释放锁，del
        redisTemplate.delete("lock");

    }else{
        //3获取锁失败、每隔0.1秒再获取
        try {
            Thread.sleep(100);
            testLock();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

###### UUID防误删

![image-20211004190140393](https://i.loli.net/2021/10/04/b3x48JVXUHTEBSd.png)

###### LUA脚本保证删除的原子性

## Redis6特性

- 支持IO多线程其实指**客户端交互部分**的**网络IO**交互处理模块**多线程**，而非**执行命令多线程**。Redis6执行命令依然是单线程。

