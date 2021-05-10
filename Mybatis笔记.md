#### 1.Mybatis 配置

依赖代码置于 pom.xml 文件中

```
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.4.6</version>
</dependency>
```



#### 2.构建 SqlSessionFactory

在 XML 中构建 SqlSessionFactory

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/girls?useSSL=true"/>
                <property name="username" value="root"/>
                <property name="password" value="010426"/>
            </dataSource>
        </environment>
    </environments>
<!--每一个Mapper.xml都需要在Mybatis核心配置文件中注册-->
    <mappers>
        <mapper resource="com/gzk/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

#### 3.资源文件 

```java
<build>
  <!--在build中配置resourses，防止我们资源出现失败-->
  <resources>
    <resource>
      <directory>src/main/resources</directory>
      <includes>
        <include>**/*.properties</include>
        <include>**/*.xml</include>
      </includes>
      <filtering>true</filtering>
    </resource>
    <resource>
      <directory>src/main/java</directory>
      <includes>
        <include>**/*.properties</include>
        <include>**/*.xml</include>
      </includes>
      <filtering>true</filtering>
    </resource>
  </resources>
</build>
```

####  4.增删改 

1.id：对应namespace中的方法名

2.resultType ：sql语句返回值

3.parameterType：参数类型

```
<select id="getUserList" resultType="com.gzk.pojo.User">
    select * from admin
</select>


```

###### 1.编写接口

```
//根据id查询用户
User getUserById(int id);
```

###### 2.编写mapper中sql语句

```
<select id="getUserList" resultType="com.gzk.pojo.User">
    select * from admin
</select>
```

###### 3.测试

*增删改操作必须提交事务

```java
SqlSession sqlSession = MybatisUtils.getSqlSession();
UserMapper mapper = sqlSession.getMapper(UserMapper.class);
int res = mapper.addUser(new User(7,"老李","4659"));
if (res > 0) {
    System.out.println("插入成功！");
}
//提交事务
sqlSession.commit();
sqlSession.close();
```

#### 5.万能的Map

如果实体类或数据库中的参数过多，用map！

```
int addUser2(Map<String,Object> map);
```

```
<!--传递map中的key-->
    <insert id="addUser2" parameterType="map">
        insert into admin (id,password,username) value (#{userid},#{userpassword},#{username})
    </insert>
```

```
SqlSession sqlSession = MybatisUtils.getSqlSession();
UserMapper mapper = sqlSession.getMapper(UserMapper.class);

Map<String,Object> map = new HashMap<String, Object>();
map.put("userid",6);
map.put("userpassword","2131");
map.put("username","老张");

int res = mapper.addUser2(map);
if (res > 0) {
    System.out.println("插入成功！");
}
//提交事务
sqlSession.commit();
sqlSession.close();
```

#### 6.配置解析

###### 1.属性(properties)

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。

编写一个配置文件 db.properties 放在resources中

```
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/girls?useSSL=true
username=root
password=010426
```

核心配置文件中引入

```xml
<configuration>
    <!--引入外部配置文件-->
    <properties resource="db.properties"/>
```

```xml
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```

###### 2.类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。

```xml
<!--可以给实体类起别名-->
<typeAliases>
    <typeAlias type="com.gzk.pojo.User" alias="User"/>
</typeAliases>
```

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。

```xml
<typeAliases>
  <package name="com.gzk.pojo.User"/>
</typeAliases>
```

#### 7.resultMap

```xml
<!--    结果集映射-->
    <resultMap id="UserMap" type="User">
        <!--column数据库中的字段,property实体类中的属性-->
        <result column="id" property="id"/>
        <result column="username" property="username"/>
        <result column="pwd" property="password"/>
    </resultMap>
    <select id="getUserById2" parameterType="int" resultMap="UserMap">
        select * from admin where id = #{id}
    </select>
```

#### 8.日志

######  1.STDOUT_LOGGING （默认标准日志，可以直接用）

核心配置文件中配置日志。

```xml
<!--日志-->
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

```
Created connection 1995616381.
Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@76f2b07d]
==>  Preparing: select * from admin where id = ? 
==> Parameters: 3(Integer)
<==    Columns: id, username, password
<==        Row: 3, gzk, 0426
<==      Total: 1
User{id=3, username='gzk', password='0426'}
Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@76f2b07d]
Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@76f2b07d]
Returned connection 1995616381 to pool.
```

###### 2.LOG4J

1.导入LOG4J的jar包

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2.log4j.properties

```log
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/gzk.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

3.配置log4j为日志实现

```
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

4.简单实用

导包 org.apache.log4j.Logger;

```
static Logger logger = Logger.getLogger(UserMapperTest.class);
```

#### 9.多对一处理

1.子查询

```xml
<select id="getStudent" resultMap="StudentTeacher">
    select * from student
</select>
<resultMap id="StudentTeacher" type="Student">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>

<select id="getTeacher" resultType="Teacher">
    select * from teacher where id = #{tid}
</select>
```

2.按结果嵌套查询

```xml
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id sid,s.name sname,t.name tname,t.id tid
    from student s,teacher t
    where s.tid = t.id;
</select>

<resultMap id="StudentTeacher2" type="Student">
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
        <result property="name" column="tname"/>
        <result property="id" column="tid"/>
    </association>
</resultMap>
```

#### 10.一对多处理

```xml
<select id="getTeacher" resultMap="TeacherStudent">
    select s.id sid,s.name sname,t.id tid,t.name tname
    from student s,teacher t
    where s.tid = t.id and t.id = #{id}
</select>

<resultMap id="TeacherStudent" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <collection property="students" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```

#### 11.动态SQL

###### 1.if

```xml
<select id="queryBlogTF" parameterType="map" resultType="Blog">
    select * from blog where 1 = 1
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>

</select>
```

###### 2.where

当没有条件符合时将where字段去掉，当一个满足字段开头为and或or时也将其去掉。

```xml
select * from blog
<where>
    <if test="title != null">
        title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</where>
```

###### 3.choose

只能执行其中一个相当于switch。

```xml
select * from blog
<where>
    <choose>
        <when test="title != null">
        title = #{title}
        </when>
        <when test="author != null">
        and author = #{author}
        </when>
        <otherwise>
        and views = #{views}
        </otherwise>
    </choose>
</where>
```

###### 4.set

最后的逗号会自动去掉。

```xml
update blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    <where>
        id = #{id}
    </where>
```

###### 5.sql片段

提取公共部分，不要包含where。

```xml
<sql id="if-title-author">
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
</sql>
```

```xml
<include refid="if-title-author"></include>
```

###### 6.foreach

```xml
select * from blog
        <where>
            <foreach collection="ids" item="id"
                     open="and (" separator="or" close=")" >
                id = #{id}
            </foreach>
        </where>
```

```sql
where 1 = 1 and (id = 1 or id = 2 or id = 3)
```

