# Mybatis框架

## 1 Mybatis执行流程

### 1.1 总体流程

![image-20250321144500947](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321144501010.png)

### 1.2 mybatis-config.xml

***配置文件主要负责两个工作***

- 配置数据库连接信息：操作的是哪个数据库，数据库的账号密码
- 数据库连接池
- 加载sql映射文件，两种方式：
  1. 直接指定文件名
  2. 指定对应的包名，该包下的所有映射文件都会加载

![image-20250321142907529](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321142907573.png)

### 1.3 SqlSessionFaction构建会话工厂

- Mybatis主要通过一个sqlSession对象来操作数据库，Sqlsession对象在SqlSessionFaction中批量产生
- SqlSessionFaction在全剧中只有一个

### 1.4 创建会话

![image-20250321143836732](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321143836776.png)

### 1.5 Executor执行器

封装了JDBC指令，在操作数据库之前要读取一些信息，在1.6中说明

![image-20250321143934703](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321143934737.png)

### 1.6 MapperStatement对象

定义某些标签中的信息

![image-20250321144134144](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321144134198.png)

## 2 Mybatis的延迟加载

### 2.1 什么是延迟加载

***在Mybatis中，延迟加载默认是没有开启***

![image-20250321144920803](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321144920852.png)

### 2.2 如何实现：

- ***fetchType=“lazy”，局部的单词语句生效***

  ![image-20250321150855164](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321150855233.png)

- ***在mybatis-config.xml设置，实现全局延迟加载***

![image-20250321151020915](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321151020951.png)

### 2.3 延迟加载底层实现

![image-20250321152423303](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321152423348.png)

### 2.4 一级缓存

![image-20250321154543140](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321154543199.png)

![image-20250321154604042](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321154604092.png)

2.5 二级缓存

![image-20250321154653006](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321154653049.png)

![image-20250321154758947](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321154758997.png)