# Java基础

## 1. 包装类

### 1.1 自动装箱

```java
Integer i=100； ---->  Integer i = new integer.valueof(100);
```

### 1.2 自动拆箱

```java
int i2 = i; ------> int i2 = Integer.value(i);
```

### 1.3 其他功能

![image-20250320133349372](C:\Users\NTZYD\AppData\Roaming\Typora\typora-user-images\image-20250320133349372.png)

```java
//string类转int
String str = “98”；
int i = Integer.parseInt(str);
int i = Integer.value(str); ///更推荐，因为double等别的类型也可以用
```

```java
//int转string
int i=100；
String rs3 = i+“”；
```

