## MyBatis学习

### 一、Mybatis工作原理

1、mybatis工作原理图：

![1566546222126](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1566546222126.png)

2、步骤详解：

（1）配置XML文件

​      加载配置文件，XMLConfigBuilder对象会进行XML配置文件的解析，实际为configuration节点的解析操作，在configuration节点下会依次解析properties/settings/.../mappers等节点配置。在解析environments节点时，会根据transactionManager的配置来创建事务管理器，根据dataSource的配置来创建DataSource对象，这里面包含了数据库登录的相关信息。在解析mappers节点时，会读取该节点下所有的mapper文件，然后进行解析，并将解析后的结果存到configuration对象中。

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="lazyLoadingEnabled" value="false"/>
    </settings>
    <!--配置环境 -->
    <environments default="development">
        <environment id="development">
            <!-- 使用jdbc事务管理 -->
            <transactionManager type="JDBC" />
            <!-- 数据库连接池 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url"
                          value="jdbc:mysql://localhost:3306/springmybatis?     characterEncoding=utf-8" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>

    <!--映射文件 -->
    <mappers>
        <mapper resource="com/smart/dao/userDao.xml"/>
    </mappers>
</configuration>
```

（2）、配置<mapper>中的映射文件，通过namespace指定映射所在的命名空间，"com.smart.dao.UserDao"是一个接口。每个具体的映射项都有一个id，通过命名空间和映射项的id定位到具体的映射项，例如 id="getUser" 对应的映射项为UserDao接口中的  getUser(User user)； parameterType指定传入的参数对象，可以是全限定类名，也可以是类的别名（在Mybatis主文件中定义）； resultType指定返回对象类型。

```java
<mapper namespace="com.smart.dao.UserDao">
    <select id="getUser" parameterType="com.smart.domain.User"
            resultType="com.smart.domain.User" >
       SELECT * FROM user WHERE username=#{username} AND password=#{password}
    </select>

    <insert id="addUser" parameterType="com.smart.domain.User" flushCache="true">
        INSERT INTO user (id,username,password) VALUES (#{id},#{username},#{password})
    </insert>

    <update id="updateUser" parameterType="com.smart.domain.User">
        UPDATE user SET password=#{password} WHERE id=#{id}
    </update>

    <delete id="deleteUser" parameterType="int">
        DELETE FROM user WHERE id=#{id}
    </delete>
</mapper>
```

（3）、书写接口,与映射文件中的映射项的id对应

```java
public interface UserDao {
    public User getUser(User user);
    public void addUser(User user);
    public void updateUser(User user);
    public void deleteUser(int UserId);
}
```

（4）、创建sqlsession

过程为：加载配置文件、通过SqlSessionFactoryBuilder对象获取SqlSessionFactory、通过SqlSessionFactory的openSession去获取sqlSession

```java
InputStream inputStream = Resources.getResourceAsStream("mybatisConfig.xml");
SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
SqlSession sqlSession = sessionFactory.openSession(true);
```

（5）、sql查询流程（使用映射接口）

``` java
SqlSession sqlSession = sessionFactory.openSession(true);
ForumMapper userMapper = sqlSession.getMapper(UserDao.class);
```

### 二、spring中配置Mybatis

****简化配置

![1566547157450](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1566547157450.png)

1、配置文件

```java
 <!--配置数据源 -->
    <bean id="dataSource"
          class="org.apache.commons.dbcp.BasicDataSource"
          destroy-method="close"
          p:driverClassName="com.mysql.jdbc.Driver"
          p:url="jdbc:mysql://localhost:3306/springmybatis?useUnicode=true&amp;characterEncoding=UTF-8"
          p:username="root"
          p:password="root" />
    <!-- mybatis-spring类包提供了一个sqlSessionFactory -->
    <bean id="sqlSessionFactory"
          class="org.mybatis.spring.SqlSessionFactoryBean"
          p:dataSource-ref="dataSource"
          p:configLocation="classpath:mybatisConfig.xml"
          p:mapperLocations="classpath:com/smart/dao/*.xml"/>
    <!-- mybatis-spring提供了一个模板类SqlSessionTemplate，可以通过模板类访问数据库 -->
     <bean class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg ref="sqlSessionFactory" />
     </bean>
```

2、使用SqlSessionTemplate调用SQL映射项完成数据访问工作

```java
@Repository
public class UserTemplateDao {
    @Autowired
    private SqlSessionTemplate sqlSessionTemplate;
    
    public User getUser(User user){
        UserDao userDao = (UserDao) sqlSessionTemplate.getMapper(UserTemplateDao.class);
        return userDao.getUser(user);
    }
}
```

3、比调用接口更优化的方法,使用MapperScannerConfigure转换器，可以将映射接口直接转换为Spring容器中的bean，就可以在service中注入映射接口的bean。

```java
 <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer"
          p:sqlSessionFactory-ref="sqlSessionFactory"
          p:basePackage="com.smart.dao" />
```

4、注入service层

```java
@Service
public class UserService {
    private UserDao  userDao;
    @Autowired
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
    public void addUser(User user){
        userDao.addUser(user);
    }
```

5、添加用户成功界面

![1566453049241](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1566453049241.png)

![1566453122160](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1566453122160.png)

##### 三、在IDEA中添加mybatis配置文件

![1566380751093](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1566380751093.png)