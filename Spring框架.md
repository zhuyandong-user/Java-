# Spring框架

[TOC]

## 1 单例bean的线程安全问题

### 1.1 如何设置bean单例多例模式

![image-20250321085038330](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130502326.png)

### 1.2 单例bean是线程安全的吗？

![image-20250321085556724](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130459345.png)

## 2.  AOP

梳理苍穹外卖里的aop和javaweb中的日志记录aop

## 3 事务失效的场景

### 3.1 异常捕获处理

![image-20250321095243041](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130456236.png)

### 3.2 抛出检查异常

![抛出检查异常](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130452796.png)

ps：默认只会回滚运行时异常

### 3.3 使用了非public方法

![使用了非public方法](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130432898.png)

## 4 SpringIOC和Bean

### 4.1 Bean的声明方式

1. @Component @Service ......
2.  xml文件中的<bean>
3. Configuration配置类中声明@Bean

### 4.2 IOC工作流程

![image-20250321100959212](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130429671.png)

![](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130423913.png)

#### 4.2.1 阶段一 IOC容器初始化阶段



1. 根据XML或注解或配置的声明方式，通过解析和加载后成BeanDefinition,并将BeanDefinition注册到IOC容器里面

   ![image-20250321103841713](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130420831.png)

2. 解析得到一个BeanDefinition实体，实体当中包含Bean的一些定义和基本的一些属性

3. 将BeanDefinition保存到一个Map集合当中，从而去完成一个IOC的初始化，IOC的作用就是对注册的Bean的定义信息进行处理和维护，是IOC容器控制反转的核心

#### 4.2.2 阶段二 Bean的初始化和依赖注入

进入第二个阶段以后，这阶段会做两件事情

1. 对没有设置lazy-init属性的单例bean进行初始化

![image-20250321104436868](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130417537.png)

2. 完成bean的依赖注入
3. bean的使用，通常使用AutoWired注解从IOC容器里面去获取一个指定的bean实例
4. 针对设置了lazy-init属性、以及非单例bean的实例化，会在每次获取bean对象的时候调用bean的初始化方法去完成实例化，并且IOC容器不会去管理这些bean

### 4.3 SpringBean生命周期 ※※

#### 4.3.1 阶段一 创建前准备![image-20250321105233935](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130413801.png)

#### 4.3.2 阶段二 正式创建Bean

这个阶段的主要作用是通过构造器和反射去创建bean的实例对象，并且会扫描并解析Bean声明的一些属性

![image-20250321105403740](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130411284.png)

#### 4.3.3 阶段三 依赖注入

1. 如果被实例化的bean存在依赖其他bean对象的一些情况，则需要对这些依赖的bean进行对象注入，比如常见的AuroWired注解
2. 同时在这个阶段会触发一些扩展的调用，比如BeanPostProcessors#before会被调用，用来去实现bean初始化前以及初始化后的扩展回调，像BeanFactoryAware等![image-20250321105805033](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130407985.png)

#### 4.3.4 阶段四容器缓存

Bean保存到IOC容器中和Spring缓存中，这个阶段就可以被开发者使用，同时BeanPostProcessors#after会被调用

![image-20250321110355969](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130405310.png)

#### 4.3.5 阶段五销毁实例

自动销毁bean，如果是实现了DIsposableBean接口，则在这个阶段被调用

![image-20250321110450737](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130403232.png)

### 4.4 Bean的循环依赖

#### 4.3.1 什么是循环依赖

![image-20250321110902047](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130400231.png)

#### 4.3.2 三级缓存结构

在Spring框架中有一个类叫做Default...，在这个类中定义了三个集合也叫三级缓存

![image-20250321111607423](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130355490.png)

**PS：**

- 一级缓存中不存放多例Bean，在获取Bean时才会创建实例对象
- 二级缓存中的早期的bean对象，指的是执行了构造函数的半成品bean对象

#### 4.3.3 三级缓存如何解决循环依赖

1. ***当对象为普通对象时，一级缓存结合二级缓存 一起使用***。
   - 调用空参构造，将A和B都构造出来后用set注入。
   - 在对象完成实例化存储到singletonObjects（一级缓存）后，将earlySing....（二级缓存中）的半成品bean删除。

![image-20250321112155628](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130351043.png)

2. ***当对象为增强后的代理对象时，使用 三级缓存 解决循环依赖***

   ObjectFacory对象会自动根据你是否为代理对象创建代理对象或普通对象

![image-20250321113110556](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130233888.png)

3. **使用@Lazy注解解决循环依赖**。当在构造方法中出现了循环依赖，上述的方法1和方法2并不能解决，

![image-20250321113455793](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130222909.png)

## 5 SpringMVC执行流程

###  5.1 视图阶段（返回的是ModelAndView

![视图阶段](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130141039.png)

![image-20250321131208254](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321131210509.png)

### 5.2  前后端分离阶段（返回的是Json

![前后端分离阶段](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321130956157.png)

![image-20250321131344655](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321131347153.png)

## 6 SpringBoot自动配置原理***

![image-20250321134336998](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321134338246.png)

![image-20250321133350542](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321133406382.png)

## 7 Spring框架常见注解

### 7.1 Spring的常见注解

![image-20250321140434665](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321140436676.png)

### 7.2 SpringMVC常见注解

![image-20250321141327016](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321141343849.png)

### 7.3 SpringBoot常见注解

![image-20250321141637880](https://java-sky-take-outzyd.oss-cn-beijing.aliyuncs.com/typora/20250321141637925.png)