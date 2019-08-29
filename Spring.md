# Spring

### 一、springt体系结构

![1566630714284](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1566630714284.png)

### 二、简单案例

##### 1、配置web.xml文件

```java
 <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:smart-context.xml</param-value>
 </context-param>
    <!-- ContextLoaderListener实现了ServletContextListener接口，只负责监听WEB容器启动和关闭-->
 <listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
 </listener>
```

##### 2、配置spring配置文件

```java
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="helloWorld" class="com.tutorialspoint.HelloWorld">
       <property name="message" value="Hello World!"/>
   </bean>
</beans>
```

##### 3、编写程序

```java
public class HelloWorld {
   private String message;
   public void setMessage(String message){
      this.message  = message;
   }
   public void getMessage(){
      System.out.println("Your Message : " + message);
   }
}
```

```java
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = 
             new ClassPathXmlApplicationContext("Beans.xml");
      HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
      obj.getMessage();
   }
}
```

##### 4、执行程序

```java
Your Message : Hello World!
```

### 三、IoC(控制反转)

#### 1、依赖注入的三种类型：构造函数注入、属性注入和接口注入。

##### 构造函数注入

```java
public class MoAttack{
    private Geli geli;
    public MoAttack(Geli geli){
        this.geli=geli;
    }
}
```

##### 属性注入

```java
public class MoAttack{
    private Geli geli;
    public void setGeli(Geli geli){
        this.geli=geli;
    }
}
```

##### 接口注入

```java
public class MoAttack implements ActorArrangable{
    private Geli geli;
    //实现接口方法
    public void injectGeli(Geli geli){
        this.geli=geli;
    }
}
```

##### ApplicationContext配置

##### 基于注解的配置信息提供类Bean

```java
@Configuration
public class Beans{
    @Bean(name="car")
    public Car buildCar(){     
    }
}
```

##### spring基于xml配置

```java
<beans 
       //默认命名空间、用于springBean的定义
       xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       //使用p命名空间，简化配置
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       //aop的配置定义
       xmlns:aop="http://www.springframework.org/schema/aop"
       //声明式事物配置定义
       xmlns:tx="http://www.springframework.org/schema/tx"
```

##### 基于注解的配置

@Component注解，细分为各层注解：

@Repository:用于对DAO实现类进行标注

@Service:用于对Service实现类进行标注

@Controller:用于对Controller实现类进行标注

扫描注解定义的Bean:

```java
<context:component-scan base-package="com.study.service"/>
```

##### 自动装配Bean

```java
@Service
public class UserService {
    //使用@Qualifier指定注入Bean的名称   @Qualifier("userDao")
    private UserDao userDao;
    private LoginLogDao loginLogDao;
   @Autowired
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
```

