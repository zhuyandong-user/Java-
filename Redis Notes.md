# Redis

[TOC]

## 1. SpringDataRedis序列化方式

### 1.1 在configration中使用默认序列化器（会加入类相关信息，冗余字段）

```java
@Bean

public RedisTempelate<String,Object>redisTemplate (RedisConnectionFactory redisConnectionFactory) throw ...{
    //创建Template
     RedisTemplate redisTemplate = new RedisTemplate();
    //设置redis连接工厂对象
    redisTemplate.setConnectionFactory(redisConnectionFactory);
    //设置序列化工具
    redisTemplate.setKeySerializer(new StringRedisSerializer());
    return redisTemplate;
}
```

### 1.2 使用StringRedisTemplate

```java
    @Autowired
    private  StringRedisTemplate stringRedisTemplate;

    @Autowired
    private static final ObjectMapper mapper = new ObjectMapper();

    void testSaveUser() throws Exception {
        //创建对象
        CreateUserResponse.User user = new CreateUserResponse.User();

        //手动序列化
        String json = mapper.writeValueAsString(user);

        //写入数据
        stringRedisTemplate.opsForValue().set("user:200", json);

        //获取数据
        String jsonUser = stringRedisTemplate.opsForValue().get("user:100");

        //手动反序列化
        mapper.readValue(jsonUser, CreateUserResponse.User.class);
    }
}
```

## 2. 优惠券秒杀

### 2.1 订单ID设计

1. 订单表中加入字段：购买的代金券id
2. 主键不自增。订单表数据量大，会受到单表数据量的限制，采用自增id会在多张表中出现重复订单id号，不便于后期售后管理
3. 代替自增主键，使用全局ID生成器，生成一个全局唯一ID的工具
4. 不直接使用redis的自增id，而是拼接其他信息（Long型，因为数值型在数据库中占用空间更小，建立索引时更方便），ID的组成：时间粗
   1. 符号位：1bit，永远为0
   2. 时间戳：31bit，以秒为单位，可以使用69年
   3. 序列号：32bit,支持每秒产生2^32个不同ID
   4. 时间戳和序列号均为递增，不再是简单的id自增，复杂度增加，不易被破解

### 2.2 优惠卷表设计

#### 2.2.1 普通卷

![image-20250319103811301](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250319103811301.png)

#### 2.2.2 秒杀卷（同时拥有普通卷的数据表信息）

![image-20250319103958096](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250319103958096.png)

#### 2.2.3 java中保存优惠卷

![image-20250319105401475](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250319105401475.png)

#### 2.3 实现秒杀卷下单

![image-20250319104645494](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250319104645494.png)

## 3.  Redis三大问题

### 3.1 缓存穿透

#### 3.1.1 使用布隆过滤器

在进行数据预热时，可以将缓存中所有的id计算hash值，存入布隆过滤器中

![image-20250320213708439](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320213708439.png)



存在问题：会误判，解决方式：设置误判率，一般在5%

![image-20250320214159715](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320214159715.png)

#### 3.1.2 存储空值入redis中

### 3.2 缓存击穿

![image-20250320212857639](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320212857639.png)

- 使用互斥锁 ----------保证强一致性，但是性能差
- 使用逻辑过期------允许有一定时间的不一致，性能优异，

### 3.3 双写一致性

#### 3.3.1 先写数据库还是先写缓存？

![image-20250320215157669](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320215157669.png)

#### 3.3.2 如何加强一致性？

1. 延迟双删

![image-20250320215323945](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320215323945.png)

2. 添加分布式锁

![image-20250320215525612](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320215525612.png)

![image-20250320215656527](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320215656527.png)

## 4 Redis持久化（RDB和AOF）

### 4.1 RDB实现方式：

![image-20250320220334568](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320220334568.png)

### 4.2 RDB的执行原理

- 子进程拷贝页表时只需要纳秒级别的时间，可以忽略不计

![image-20250320220729375](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320220729375.png)

- 在子线程写RDB文件过程中，如果主进程进行写操作改变了物理内存，会出现冲突，如何处理？

![image-20250320221020129](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320221020129.png)

### 4.3 AOF

![image-20250320221508627](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320221508627.png)

![image-20250320221611227](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320221611227.png)

一般采用everysec

缺点：

![image-20250320221918447](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320221918447.png)

### 4.4 RDB对比AOF

![image-20250320222007252](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320222007252.png)

## 5 数据过期策略

### 5.1 惰性删除策略

![image-20250320222655268](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320222655268.png)

### 5.2 定期删除策略

![image-20250320223028560](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320223028560.png)

Ps：***10hz指的是，每秒执行十次***

**Redis中的过期删除策略是两种配合使用**

## 6 数据淘汰策略

### 6.1 八种淘汰策略

![image-20250320223845072](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320223845072.png)

### 6.2 策略选择建议

![image-20250320224150682](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320224150682.png)

![image-20250320224237858](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320224237858.png)