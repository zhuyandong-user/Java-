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

## 3 sql优化经验

### 3.1 表的设计优化

![image-20250323224514309](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323224514367.png)

### 3.2 SQL语句优化

![image-20250323224530446](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323224530504.png)

### 3.3 主从复制，读写分离

![image-20250323224903969](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250323224904064.png)