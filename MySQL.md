# MySQL

## 1 慢查询

### 1.1 定位慢查询

![image-20250323214712868](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323214712958.png)

### 1.2 如何在datagrip当中开启慢日志

```
set global slow_query_log = on;
set global long_query_time = 1;
```

### 1.3 一个SQL语句执行很慢，如何分析

#### 1.3.1 指令

![image-20250323215523072](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323215523160.png)

#### 1.3.2 分析

![image-20250323215721431](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323215721523.png)

![image-20250323220000144](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323220000218.png)

![image-20250323220053335](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323220053389.png)

## 2 索引

### 2.1 什么是索引

![image-20250323220312369](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323220312430.png)

### 2.2 索引底层数据结构

#### 2.2.1 B树 

- 灰色为指向子节点的执政，绿色key所所对应的数据，蓝色为Key
- 缺点是会额外加在其他不需要的数据

![image-20250323220627947](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323220628008.png)

#### 2.2.2 B+树

- B+树的数据只存储在叶子节点，非叶子节点只是作为导航找到叶子节点
- 另外，非叶子节点的Key在叶子节点上也有
- 相较于B树 ，磁盘读写代价更低，查找效率更稳定，更便于扫库和区间查询（因为叶子节点之间使用双向指针链接，不需要从根节点再找一次

![image-20250323220922484](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323220922565.png)

2.2.3 总结

![image-20250323221841525](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323221841589.png)

### 2.3 聚簇索引、非聚簇索引、回表查询

![image-20250323222000038](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323222000113.png)

![image-20250323222231742](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323222231831.png)

![image-20250323222319509](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323222319555.png)

### 2.4 覆盖索引

![image-20250323222713718](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323222713770.png)

### 2.5 超大分页优化

![image-20250323222916555](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323222916637.png)

![image-20250323223019910](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323223019972.png)

### 2.6 索引创建原则

![image-20250323223529455](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323223529512.png)

### 2.6 索引失效情况

#### 2.6.1 违反最左前缀法则

**正常情况**

![image-20250323223701864](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323223701968.png)

失效情况

![image-20250323223856987](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323223857114.png)

#### 2.6.2 使用了范围查询

![image-20250323224017830](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323224017901.png)

#### 2.6.3 进行了运算操作

![image-20250323224109735](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323224109823.png)

#### 2.6.4 字符串没有加单引号

![image-20250323224242498](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323224242568.png)

#### 2.6.5 模糊查询%在前面

![image-20250323224316842](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323224316966.png)

## 3 SQL优化经验

### 3.1 insert语句优化

- **选择批量插入，而不是单条插入**

每次执行插入操作需要与数据库进行连接和网络传输

`insert into tb_name values(1,'adsf')(2,'asdfa')...`

- **选择手动提交事务，而不是让数据库自动提交事务**

数据库在提交事务之前需要开启事务，每执行一次都要开启一次。

选择自己手动提交事务，在执行语句之前开启事务，在语句全部执行完成之后提交事务。

`start transaction`

`insert into tb_name values(1,'adsf')(2,'asdfa')...`

`insert into tb_name values(1,'adsf')(2,'asdfa')...`

`insert into tb_name values(1,'adsf')(2,'asdfa')...`

`commit`

- **主键顺序插入，而不是乱序插入**
- **大批量插入数据**

如果一次性插入大批量数据，使用MySQL提供的load指令而不是insert（性能太低）

![image-20250410145225168](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250410145232383.png)

### 3.2 主键优化

- 满足业务需求下，尽量降低主键的长度，因为每个二级索引的叶子节点中会存储主键，主键长度过长会造成内存占用过大

- 插入数据尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键，如果主键乱序可能导致

  [^页分裂]: 什么是页分裂，参考黑马MySQL part90

- 尽量不要使用UUID做主键或者是其他自然主键，如身份证号
- 业务操作时尽量避免对主键的修改

### 3.3 order by语句优化

**MySQL中的两种排序实现方式：**

![image-20250410155914725](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250410155914780.png)

![image-20250410155538852](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250410155538933.png)

![image-20250410155642050](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250410155642164.png)

总结： 

![image-20250410155936709](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250410155936766.png)

### 3.3 group by优化

在分组操作时，可以通过索引来提高效率，同时要满足最左前缀法则

### 3.4 limit语句优化

存在的问题？

![image-20250410161418746](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250410161418807.png)

解决方法

![image-20250410161939948](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250410161940042.png)

### 3.5 update语句优化

没有索引可能会把行锁升级为表锁

InnoDB的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级=为表锁

所以更新的where条件一定要加索引，避免升级为表锁

