

# 1.简介

简化开发：

IoC，AOP （事务处理）

框架整合：

MyBatis， MyBatis-Plus， struts，Hibernate

# 2.Spring Framework 系统架构

## **Core Container**

核心容器

Beans, Core, Context, spEL

## **AOP, ASspects**

面向切面编程，aop思想实现

## **Data Access/Integration**

数据访问，集成

## Test

单元与集成测试

# 3.核心概念

## IoC (Inversion of Control) 控制反转

使用对象时，由主动new产生对象转换为由**外部**提供对象，此过程中对象创建控制权转移到外部，此思想称为控制反转。

## Spring技术对IoC思想进行实现

Spring 提供了一个容器，称为IoC容器，用来充当IoC思想中的“外部”

IoC容器负责对象的创建，初始化等一系列工作，被创建或被管理的对象在IoC容器中统称为Bean

## DI (Dependency Injection) 依赖注入

在容器中建立bean与bean之间的依赖关系的整个过程，称为依赖注入



## 目标：充分解耦

使用IoC容器管理bean

在IoC容器内将所有依赖关系的bean进行关系绑定

# 4.IoC举例

1.导入spring的坐标spring-context， 对应版本5.2.0.RELEASE

```xml
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>
```

定义类，接口

```java
package com.charles.service;

public interface BookService {
    public void save();
}

```

```java
package com.charles.service.impl;

import com.charles.dao.BookDao;
import com.charles.dao.impl.BookDaoImpl;
import com.charles.service.BookService;

public class BookServiceImpl implements BookService {

    private BookDao bookDao = new BookDaoImpl();

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}

```



2. 配置bean

   bean标签表示配置bean

   id属性表示命名bean, id不可重复

   class属性表示给bean定义类型

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.charles.dao.impl.BookDaoImpl" />
    <bean id="bookService" class="com.charles.service.impl.BookServiceImpl" />
</beans>
```

3. 获取IoC容器

```java
//获取IoC容器
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   
```

4. 获取bean

```java
BookDao bookDao = (BookDao) ctx.getBean("bookDao");
bookDao.save();
```

这个案例中，还没有实现完全解耦，存在new 对象。

# 5.DI 举例 (XML)

## 修改配置文件

property标签表示配置当前bean的属性

name属性表示配置哪一个**具体属性**

ref属性表示参照哪一个**bean** 和name要区别。

```xml
<bean id="bookService" class="com.charles.service.impl.BookServiceImpl">
<!--        配置service与Dao的关系-->

        <property name="bookDao" ref="bookDao" />
</bean>
```

# 6.bean配置

## bean别名

通过bean标签中的name属性可以定义bean的别名

多个别名可以通过空格和逗号隔开。

```xml
    <bean id="bookService" name="service bookEbi" class="com.charles.service.impl.BookServiceImpl">

```

注意：bean如果无法通过name， id获取会抛出异常， 报错：

​	**NoSuchBeanDefinitionException：No bean named 'bookServiceImpl' available**

## bean作用范围

bean标签下的scope属性可以定义bean的作用范围：

singleton 单例（默认）

prototype 非单例

```xml
    <bean id="bookDao" class="com.charles.dao.impl.BookDaoImpl" scope="prototype"/>
```

**为什么bean默认单例？**

单例适合于：交给容器进行管理的bean

​	表现层对象

​	业务层对象

​	数据层对象

​	工具对象

不适用于：封装实体的域对象

# 7.bean实例化

## 使用静态工厂实例化bean

```java
public class OrderDaoFactory {
    public static OrderDao getOrderDao() {
        return new OrderDaoImpl();
    }
}
```



```xml
<bean id="orderDao" factory-method="getOrderDao" class="com.charles.factory.OrderDaoFactory"></bean>
```



# 8. 实例工厂初始化bean

```java
public class UserDaoFactory {
    public UserDao getUserDao() }
		return new UserDaoImpl();
}
```

```xml
<bean id="userDaoFactory" class="com.charles.factory.UserDaoFactory" />
<bean id="userDao" factory-method="getUserDao" factory-bean="userDaoFactory" />
```

## 使用FactoryBean

```java
public class UserDaoFactoryBean implements FactoryBean<UserDao> {
    public UserDao getObject() throws Exception {
        return new UserDaoimpl();
    }
    public Class<?> getObjectType() {
        return UserDao.class;
    }
}
```

```xml
<bean id = "userDao" class="com.charles.factory.UserDaoFactoryBean" />
```

# 9.bean生命周期

bean生命周期：从创建到销毁

初始化容器

​	1.创建对象（内存分配）

	2. 执行构造方法
	2. 执行属性注入 （set操作）
	2. 执行bean初始化方法

使用bean

​	执行业务操作

关闭/销毁容器

​	执行bean销毁方法



# 10.依赖注入



setter注入

构造器注入

# 11.依赖自动装配

IoC容器根据bean所依赖的资源在容器中自动查找并注入到bean中的过程称为自动装配

方式：按照类型，名称，构造方法

```xml 
<bean id="bookDao" class="..." />
<bean id="bookService" class="..." autowire="byType" />
```

自动装配用于引用类型依赖注入，不能对简单类型进行操作

使用按类型装配时（byType）必须保障容器中相同类型的bean唯一，推荐使用

使用按名称装配时（byName）必须保障容器中具有指定的名称bean，因变量名与配置耦合，不推荐使用

自动装配优先级低于setter注入与构造器注入，同时出现时自动装配配置失效。

































































































