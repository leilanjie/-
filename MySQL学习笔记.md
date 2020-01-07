## MySQL学习笔记

### 一、基础语言

#### 1、select

##### 1.1 基础查询

示例表：

```sql
//查询表中所有数据
SELECT * FROM T_Product;
```

![1572341754519](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572341754519.png)

```sql
//查询表中某些字段
SELECT product_name,stock FROM T_Product;
```

![1572341949947](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572341949947.png)

```sql
//去除查询字段中的重复值
select distinct product_name from T_Product;
select distinct product_name from T_Product where price>55;
```

![1572342205127](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572342205127.png)![1573440597949](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1573440597949.png)

```sql
//根据条件查询
SELECT * FROM T_Product where product_name="java书";
```

![1572342349144](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572342349144.png)

##### 1.2 根据查询条件



![1572403064012](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572403064012.png)



###### 1.2.1 根据范围(BETWEEN AND)

查找属性值在指定范围内的元组

```sql
//查询库存在8-30之间的产品名称、价格、库存
SELECT product_name,price,stock FROM T_Product WHERE stock BETWEEN 8 AND 30;
```

![1572403375915](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572403375915.png)

```sql
////查询库存不在8-30之间的产品名称、价格、库存
SELECT product_name,price,stock FROM T_Product WHERE stock NOT BETWEEN 8 AND 30;
```

![1572403446713](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572403446713.png)

###### 1.2.2 确定集合(IN)

查找属性值属于指定集合的元组

```sql
SELECT product_name,price FROM T_Product WHERE VERSION IN (2019,2018);
```

![1572404254857](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572404254857.png)

```sql
SELECT product_name,price FROM T_Product WHERE VERSION NOT IN (2019,2018);
```

![1572404300349](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572404300349.png)

###### 1.2.3 字符匹配(LIKE)

进行字符串的匹配：

语法：[NOT] LIKE '<匹配串>' [ESCAPE '<换码字符>']

<匹配串>可以是完整的字符串，也可以含有通配符%和_。

%代表任意长度的字符串。

_代表任意单个字符。

```sql
SELECT * FROM T_Product WHERE price LIKE 50.00;
```

![1572404933628](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572404933628.png)

```sql
SELECT product_name,price FROM T_Product WHERE note  LIKE 'java%';
```

![1572405234538](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572405234538.png)

注意：若查询条件中的字符串本身就含有通配符 % 或 _，此时需要使用ESCAPE '<换码字符>'短语对通配符进行转义。

```sql
//此时%不再具有通配符的含义及作用
SELECT product_name,price FROM T_Product WHERE note  LIKE 'java\%高级' ESCAPE '\';
```

###### 1.2.4 空值查询(NULL)

```sql
SELECT product_name FROM T_Product WHERE price IS NULL;
```

注意： IS不能用（=）代替

###### 1.2.5 多重条件查询

```sql
//and & or的应用
SELECT * FROM T_Product WHERE (product_name="java书" or stock=99) and price=55;
```

![1572342553871](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572342553871.png)

###### 1.2.6 ORDER BY子句

  对查询结果按照一个或多个属性列的升序或降序（DESC）排列，默认为升序

```sql
//产品名称按照字母顺序排序，价格反序排列
SELECT product_name,stock FROM T_Product order by product_name,stock DESC;
```

![1572348561616](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572348561616.png)

###### 1.2.7GROUP BY

GROUP BY 语句用于结合合计函数，根据一个或多个列对结果集进行分组

```sql
//以产品名称分组，查询总价格
SELECT product_name,SUM(price) FROM T_Product GROUP BY product_name;
```

![1572351155897](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572351155897.png)



###### 1.2.8 HAVING

```sql
//以产品名称分组，查询库存大于20的产品
SELECT product_name,SUM(stock) FROM T_Product GROUP BY product_name HAVING SUM(stock)>20;
```

![1572351731394](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572351731394.png)

#### 2、insert

```sql
//增加一行数据
INSERT INTO T_Product(product_name,stock,price,VERSION,note) VALUES ('javascript书',30,99,2019.3,'javascript高级学习');
```

#### 3、update

```sql
//更改某个字段值
UPDATE T_Product SET product_name="java进阶教程" WHERE note="java进阶";
```

![1572348060531](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572348060531.png)

#### 4、delete

```sql
//删除某行
delete from T_Product where price=60;
```

### 二、SQL函数

#### 1、avg()

```sql
//查询价格平均值
SELECT AVG(price) FROM T_Product;
//查询价格大于平均值的产品名
SELECT product_name FROM T_Product WHERE price>(SELECT AVG(price) FROM T_Product);
```

![1572349924088](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572349924088.png)

![1572349942972](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572349942972.png)



#### 2、count()

```sql
//查询java书的总数
SELECT COUNT(product_name) FROM T_Product WHERE product_name="java书";
//查询不同产品名的数目
SELECT COUNT(DISTINCT product_name) AS product_name_diff FROM T_Product;
```

![1572350179174](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572350179174.png)

![1572350508922](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572350508922.png)

#### 3、ucase()/lcase()

UCASE 函数把字段的值转换为大写

LCASE函数把字段的值转换为小写

```sql
//将product_name字段值转换成大写
SELECT UCASE(product_name) FROM T_Product;
//将product_name字段值转换成小写
SELECT LCASE(product_name) FROM T_Product;
```

![1572351885532](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572351885532.png)

#### 4、mid()

MID 函数用于从文本字段中提取字符

```sql
//提取product_name字段中前三个字符
SELECT MID(product_name,1,3) FROM T_Product;
```

![1572352177674](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572352177674.png)

#### 5、len()

LEN 函数返回文本字段中值的长度

```sql
//查询字段中的字符长度
SELECT Length(product_name) as product_name_length FROM T_Product;
```

![1572352871605](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572352871605.png)

#### 6、round()

ROUND 函数用于把数值字段舍入为指定的小数位数

```sql
//将库存字段的值的小数位设为1
SELECT product_name,ROUND(stock,1) AS price FROM T_Product;
```

![1572399637914](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572399637914.png)

#### 7、now()

NOW 函数返回当前的日期和时间

```sql
//显示当前时间，各个产品的价格
select product_name,price, now() as PerDate from T_Product;
```

![1572399312357](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572399312357.png)

#### 8、format()

FORMAT 函数用于对字段的显示进行格式化

```sql
//将日期显示为指定的格式
SELECT product_name,price, format(now(),'YYYY-MM-DD') as PreDate FROM T_Product;
```

### 三、连接查询

#### 1、inner join

用于根据两个或多个表中的列之间的关系，从这些表中查询数据

```sql
SELECT T_Product.`product_name`,T_Product.`stock`,T_user.`name` FROM T_Product,T_user WHERE T_Product.`id` = T_user.`pro_id`;
```

使用 inner join

```sql
SELECT T_Product.`product_name`,T_Product.`stock`,T_user.`name`
       FROM T_Product inner join T_user 
       on T_Product.`id` = T_user.`pro_id`
       order by T_Product.`stock`;
```

![1572522695798](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572522695798.png)

除了我们在上面的例子中使用的 INNER JOIN（内连接），还有其他几种连接：

JOIN: 如果表中有至少一个匹配，则返回行

LEFT JOIN: 即使右表中没有匹配，也从左表返回所有的行

RIGHT JOIN: 即使左表中没有匹配，也从右表返回所有的行

FULL JOIN: 只要其中一个表中存在匹配，就返回行

#### 2、left join

LEFT JOIN 关键字会从左表 (table_name1) 返回所有的行，即使在右表 (table_name2) 中没有匹配的行。

语法：

```sql
SELECT column_name(s)  FROM table_name1 LEFT JOIN table_name2 
       ON table_name1.column_name=table_name2.column_name
```

example:

```sql
SELECT T_Product.`product_name`,T_Product.`stock`,T_user.`name`
       FROM T_Product LEFT JOIN T_user 
       ON T_Product.`id` = T_user.`pro_id`
       ORDER BY T_Product.`stock`;
```

![1572523787155](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572523787155.png)

#### 3、right join

RIGHT JOIN 关键字会右表 (table_name2) 那里返回所有的行，即使在左表 (table_name1) 中没有匹配的行

语法：

```sql
SELECT column_name(s)
FROM table_name1
RIGHT JOIN table_name2 
ON table_name1.column_name=table_name2.column_name
```

example:

```sql
SELECT T_Product.`product_name`,T_Product.`stock`,T_user.`name`
       FROM T_Product RIGHT JOIN T_user 
       ON T_Product.`id` = T_user.`pro_id`
       ORDER BY T_Product.`stock` DESC;
```

![1572524061926](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572524061926.png)

#### 4、full join

只要其中某个表存在匹配，FULL JOIN 关键字就会返回行

```sql
SELECT T_Product.`product_name`,T_Product.`stock`,T_user.`name`
       FROM T_Product 
       FULL JOIN T_user
       ON T_Product.`id` = T_user.`pro_id`
       ORDER BY T_Product.`stock` DESC;
```

#### 5、创建表

![1572524755857](C:\Users\20190712133\AppData\Roaming\Typora\typora-user-images\1572524755857.png)

##### 5.1 UNIQ约束

约束唯一标识数据库表中的每条记录

UNIQUE 和 PRIMARY KEY 约束均为列或列集合提供了唯一性的保证。

PRIMARY KEY 拥有自动定义的 UNIQUE 约束。

请注意，每个表可以有多个 UNIQUE 约束，但是每个表只能有一个 PRIMARY KEY 约束

##### 5.2 CHECK

CHECK 约束用于限制列中的值的范围

例：CHECK (Id_P>0)

##### 5.3 DEFAULT

用于向列中插入默认值

例：City varchar(255) DEFAULT '西安'       //设置城市的默认值为西安

##### 5.4 Date

MySQL 使用的数据类型：

- DATE - 格式 YYYY-MM-DD
- DATETIME - 格式: YYYY-MM-DD HH:MM:SS
- TIMESTAMP - 格式: YYYY-MM-DD HH:MM:SS
- YEAR - 格式 YYYY 或 YY

### 四、动态SQL语句

#### 1、if

根据条件查询，与 where 搭配使用

例：

```sql
<trim prefix="(" suffix=")" suffixOverrides=",">
            <if test="name != null">
                name,
            </if>
            <if test="age != null">
                age,
            </if>
            <if test="hobby != null" >
                hobby,
            </if>
        </trim>
        <trim prefix="values(" suffix=")" suffixOverrides=",">
            <if test="name != null">
                #{name},
            </if>
            <if test="age != null">
                #{age},
            </if>
            <if test="hobby != null">
                #{hobby},
            </if>
        </trim>
```

#### 2、choose、when、otherwise

当不想应用到所有的条件语句，只想从中择其一项。MyBatis 提供的 choose 元素，类似于 Java 中的 switch 语句。

```sql
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

#### 3、trim、set、where

**where**:

```sql
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG 
  <where> 
    <if test="state != null">
         state = #{state}
    </if> 
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

**trim:**

```sql
<trim prefix="(" suffix=")" suffixOverrides=",">
```

此语句的作用是：为语句添加前缀和后缀值，并且移除语句中最后一个“，”

**set:**

```sql
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

#### 4、foreach

对一个集合进行遍历，通常是在构建 IN 条件语句的时候

```sql
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

