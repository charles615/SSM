# 1. 加载properties文件

先建立一个jdbc.properties文件， 里面包含:

jdbc.driver, jdbc.url, jdbc.username, jdbc.password

1.开启context命名空间

```xml
将五处beans改为context
----------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">
    
    
</beans>
```



2.使用context空间加载properties文件

在配置文件里编写：

```xml
<context:property-placeholder location="jdbc.properties" />

<bean class="com.alibaba.druid.pool.DruidDataSource">
	<property name="driverClassName" value="${jdbc.driver}" />
    <property name="url" value="${jdbc.url}" />
    <property name="username" value="${jdbc.username}" />
    <property nam"password" value="${jdbc.password}" />
</bean>

------------------------------------
其中如果想要不加载系统属性可以使用：
<context:property-placeholder location="jdbc.properties" system-properties-mode="NEVER"/>
------------------------------------
当存在多个配置文件时，可以使用*加载全部：

<context:property-placeholder location="*.properties" />
```

加载配置文件正规书写格式：

```xml
<context:property-placeholder location="classpath*:*.properties" system-property-mode="NEVER" />

---------------------------------------------
location中classpath表示系统只会读取当前项目目录中的配置文件
第一个*表示， 可以读取导入jar包中的配置文件。
第二个*表示，可以读取classpath中的所有jar包。
```



# 2. 容器

BeanFactory是IoC容器的顶层接口，初始化BeanFactory对象时，加载的bean延迟加载

ApplicationContext接口是spring容器的核心接口，初始化时bean立即加载

ApplicationContext接口提供基础的bean操作相关方法，通过其他接口扩展功能

ApplicationContext接口常用初始化类

​	ClassPathXmlApplicationContext

​	FileSystemXmlApplicationContext



## bean相关

```xml
<bean
      id="bookDao"      bean的id
      name="dao bookDaoImpl BookDaoImpl"		bean的别名
      class="com.charles.dao.impl.BookDaoImpl" bean的类型，静态工厂类， FactoryBean类
      scope="singleton"				控制bean的实例数量
      init-method="init"		生命周期初始化方法
      destroy-method="destroy"		生命周期销毁方法
      autowire="byType"				自动装配类型
      factory-method="getInstance"		bean工厂方法，应用于静态工厂或实例工厂
      factory-bean="com.charles.factory.BookDaoFacotry"  实例工厂bean
      lazy-init="true" 					控制bean延迟加载
      />
```

# 3.  注解开发定义bean

使用@Component定义bean

```java
@Component("bookDao")
public class BookDaoImpl implements BookDao {
    
}

@Component
public class BookServiceImpl implements BookService {
    
}
```

核心配置文件中通过组件扫描加载bean

```xml
<comtext:component-scan base-package="com.charles" />
```

定义bean：

​	@Component

​			@Controller,	@Service,	@Repository

# 4. 纯注解开发



Spring3.0 开启了纯注解开发模式，使用Java类代替配置文件，开启了Spring快速开发赛道。

Java类代替Spring核心配置文件。

```java
@Configuration
@ComponentScan("com.charles")
public class SpringConfig {
    
}
```

@Configuration, 	@ComponentScan,	AnnotationConfigApplicationContext

# 5. bean 管理

bean的作用范围：

使用@Scope定义bean作用范围

```java
@Repository
@Scope("singleton")
public class BookDaoImpl implements BookDao {
    
}
```

bean生命周期

@PostConstruct， @Predestroy

# 6. 依赖注入

使用@Qualifier注解开启指定名称装配bean

```java
@Service
public class BookServiceImpl implements BookService {
    @Autowired
    @Qualifier("bookDao")
    private BookDao bookDao;
}
```

使用@Value实现简单类型注入

```java
@Repository("book Dao")
public class BookDaoImpl implements BookDao {
    @Value("100")
    private String connectionNum;
}
```

# 7. 第三方bean管理

@Bean

导入式 @Import

























