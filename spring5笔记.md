

#### 1.Spring

###### 1.导入Spring5依赖

```
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.2.0.RELEASE</version>
</dependency>
```

```
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>5.2.0.RELEASE</version>
</dependency>
```

###### 2.优点

轻量级、非入侵框架；控制反转（IOC），面向切面编程（AOP）。

###### 3.组成

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20210419202050766.png" alt="image-20210419202050766" style="zoom:80%;" />

#### 2.IOC理论

###### 1.配置beans.xml 

bean = 对象     new Hello();

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

   <bean id="hello" class="com.gzk.pojo.Hello">
        <property name="str" value="Spring"/>
    </bean>

</beans>
```

![image-20210419223751715](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20210419223751715.png)

###### 2.获取spring上下文对象

```java
ApplicationContext context =
        new ClassPathXmlApplicationContext( "beans.xml");
```

###### 3.获取对象

```
//我们的对象都在spring中管理了，我们要使用，直接去里面取
Hello hello = (Hello) context.getBean("hello");
System.out.println(hello.toString());
```

#### 3.IOC创建对象方式

###### 1.默认使用无参构造创建对象。   

###### 2.使用有参方式创建对象。

```xml
//下标赋值 
<bean id="hello" class="com.gzk.pojo.Hello">
    <constructor-arg index="0" value="hi"/>
</bean>
```

```
//类型创建
<bean id="hello" class="com.gzk.pojo.Hello">
        <constructor-arg type="java.lang.String" value="hi"/>
</bean>
```

```
//直接通过参数名 
<bean id="hello" class="com.gzk.pojo.Hello">
        <constructor-arg name="str" value="hi"/>
</bean>
```

#### 4.Spring配置

###### 1.别名

```
<alias name="hello" alias="hello2"/>
```

###### 2.bean的配置

id: 唯一的标识符，相当于对象名

class: bean对象所对应的全限定名（包名+类型）

name: 也是别名，并且可以同时存在多个

###### 3.import

```xml
applicationContext.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="beans.xml"/>

</beans>
```

#### 5.依赖注入

###### 1.构造器注入

###### 2.set方式注入

```xml
<bean id="address" class="com.gzk.pojo.Address">
    <property name="address" value="火星"/>
</bean>

<bean id="student" class="com.gzk.pojo.Student">
    <property name="name" value="gzk"/>
    <!--bean注入-->
    <property name="address" ref="address"/>
    <!--数组注入-->
    <property name="books">
        <array>
            <value>红楼梦</value>
            <value>西游记</value>
            <value>三国演义</value>
        </array>
    </property>
    <!--List注入-->
    <property name="hobbies">
        <list>
            <value>唱</value>
            <value>跳</value>
            <value>篮球</value>
        </list>
    </property>
    <!--map注入-->
    <property name="card">
        <map>
            <entry key="身份证" value="123165465"/>
            <entry key="银行卡" value="156156116"/>
        </map>
    </property>
    <!--Set注入-->
    <property name="games">
        <set>
            <value>CF</value>
            <value>LOL</value>
        </set>
    </property>
    <!--空值注入-->
    <property name="wife">
        <null/>
    </property>
    <!--properties注入-->
    <property name="info">
        <props>
            <prop key="学号">1165165</prop>
            <prop key="性别">男</prop>
        </props>
    </property>
</bean>
```

###### 3.拓展方式注入

p命名空间和c命名空间（首先要导入约束）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
        
    <!--p命名空间注入，直接注入属性：property-->
    <bean id="user" class="com.gzk.pojo.User" p:age="18" p:name="gzk"/>

    <!--c命名空间注入，通过构造器注入：construct-args-->
    <bean id="user2" class="com.gzk.pojo.User" c:name="你好" c:age="16"/>
</beans>
```

#### 6.bean的作用域

![image-20210420231157912](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20210420231157912.png)

###### 1.单例模式

（spring5默认）

```
<bean id="user2" class="com.gzk.pojo.User" c:name="你好" c:age="16" scope="singleton"/>
```

###### 2.原型模式

每次从容器内获得都会产生一个新的对象

```
<bean id="user2" class="com.gzk.pojo.User" c:name="你好" c:age="16" scope="prototype"/>
```

#### 7.bean的自动装配

spring中三种装配方式

1.在xml中显示配置

2.在java中显示配置

3.隐式的自动装配

###### 1.byName

自动在容器上下文查找和自己对象set方法后面的值对应的beanId

```
<bean id="cat" class="com.gzk.pojo.Cat"/>
<bean id="dog" class="com.gzk.pojo.Dog"/>
<bean id="people" class="com.gzk.pojo.People" autowire="byName">
    <property name="name" value="gzk"/>
</bean>
```

###### 2.byType

自动在容器上下文查找和自己对象属性类型相同的beanId

```
<bean id="cat" class="com.gzk.pojo.Cat"/>
<bean id="dog111" class="com.gzk.pojo.Dog"/>
<bean id="people" class="com.gzk.pojo.People" autowire="byType">
    <property name="name" value="gzk"/>
</bean>
```

###### 3.使用注解方式

The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML. 

导入context约束；

配置注解支持<context:annotation-config/>；

@Autowired：在属性上用即可

通过byname方式实现

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

<context:annotation-config/>

</beans>
```

如果自动装配无法通过一个注解@Autowired完成，我们可以使用@Qualifier(value = "xxx")

去配置@Autowired的使用，指定一个唯一的bean对象的注入。

@Resource

@Resource(name = "xxx")

默认通过byname方式实现，如果找不到则通过bytype实现

#### 8.使用注解开发

必须保证aop的包已经导入。

导入Context约束。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

<context:annotation-config/>

</beans>
```

// 指定扫描某个包，那这个包下的注解会生效
<context:component-scan base-package="com.gzk.pojo"/>

###### 1.@Component

组件，放在类上，说明这个类被Spring管理了，就是bean。

等价于<bean id = "user" class = "com.gzk.pojo.User"/>

###### 2.@Value("xxx")

放在某个属性上边，为其赋值。

```java
@Component
public class User {
    @Value("郭泽凯")
    public String name;
}
```

###### 3.衍生表示

分层中的@Component 

1.dao ：@Repository

2.service：@Service

3.controller：@Controller

这四个注解功能相同，都是将某个类注册到Spring当中，装配bean。

###### 4.作用域@Scope

```
@Component
@Scope("singleton")//单例
public class User {
    @Value("郭泽凯")
    public String name;
}
```

#### 9.java配置Spring

```
@Component
public class User {
    @Value("郭泽凯")
    private String name;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

```
//配置文件
//@Configuration代表这是一个配置类，相当于beans.xml
@Configuration
@ComponentScan("com.gzk.pojo")
//引入多个配置
@Import(com.gzk.config2.class)
public class GuoConfig {
    //注册一个bean，相当于之前写的一个bean标签
    //这个方法的名字，相当于bean标签的id属性
    //这个方法的返回值，相当于bean标签的的class属性
    @Bean
    public User getUser(){
        return new User();
    }
}
```

```
public class myTest {
    public static void main(String[] args) {
    //此时必须用AnnotationConfigApplicationContext
        ApplicationContext context =
               new AnnotationConfigApplicationContext(GuoConfig.class);
        User getUser = (User) context.getBean("getUser");
        System.out.println(getUser.getName());
    }
}
```

#### 10.代理模式

这个是SpringAOP的底层。

其分类：静态代理，动态代理

###### 1.静态代理

 <img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20210422110352036.png" alt="image-20210422110352036" style="zoom:67%;" />

###### 2.动态代理

proxy：代理    invocationHandler：调用处理程序（接口）

```
//用这个类自动生成代理
public class ProxyInvocationHandle implements InvocationHandler {

    private Object target;//被代理的接口

    public void setTarget(Object target) {
        this.target = target;
    }

    //生成得到的代理类
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),
                target.getClass().getInterfaces(), this);
    }

    //处理代理实例，并返回结果
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());
        Object result = method.invoke(target, args);
        return result;
    }

	//日志
    public void log(String msg) {
        System.out.println("执行了" + msg + "方法");
    }
}
```

```
public static void main(String[] args) {

    //真实角色
    UserServiceImpl userService = new UserServiceImpl();

    //代理角色
    ProxyInvocationHandle pih = new ProxyInvocationHandle();

    //生成要代理的对象
    pih.setTarget(userService);

    //动态生成代理类
    UserService proxy = (UserService) pih.getProxy();

    proxy.delete();
    proxy.add();
}
```

#### 11.AOP

###### 1.导入依赖包

```
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

###### 2.使用spring的aop接口

 增加日志范例

```
public class Log implements MethodBeforeAdvice {

    //method:要执行目标对象的方法
    //objects:参数
    //target:目标对象
    @Override
    public void before(Method method, Object[] objects, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"被执行了！");
    }
}
```

```
public class AfterLog implements AfterReturningAdvice {

    //returnValue:返回值
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] objects, Object target) throws Throwable {
        System.out.println("执行了"+method.getName()+"方法,返回结果为："+returnValue);
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="userService" class="com.gzk.service.UserServiceImpl"/>
    <bean id="log" class="com.gzk.log.Log"/>
    <bean id="afterLog" class="com.gzk.log.AfterLog"/>

    <!--配置aop需要导入aop的约束-->
    <aop:config>
        <!--切入点:expression:表达式,execution(要执行的位置 *修饰词 *返回值 *列名 *方法名 *参数)-->
        <!--两个点表示有任意的参数-->
        <aop:pointcut id="pointcut" expression="execution(* com.gzk.service.UserServiceImpl.*(..))"/>

        <!--执行环绕增加-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

```
public void test1() {
    ApplicationContext context =
            new ClassPathXmlApplicationContext("applicationContext.xml");
    //动态代理代理的是接口
    UserService userService = context.getBean("userService", UserService.class);
    userService.add();
}
```

###### 3.使用自定义类  

```
<aop:config>
<!--        自定义切面-->
        <aop:aspect ref="diy">
<!--            切入点-->
            <aop:pointcut id="pointcut" expression="execution(* com.gzk.service.UserServiceImpl.*(..))"/>
<!--            通知-->
            <aop:before method="before" pointcut-ref="pointcut"/>
            <aop:after method="after" pointcut-ref="pointcut"/>
        </aop:aspect>
    </aop:config>
```

```
public class DiyPointCut {
    public void before() {
        System.out.println("------------before");
    }

    public void after() {
        System.out.println("------------after");
    }
}
```

###### 4.使用注解实现

```java
@Aspect//标注这个类是一个切面
public class AnnotationPointCut {

//    指明切入点
    @Before("execution(* com.gzk.service.UserServiceImpl.*(..))")
    public void before() {
        System.out.println("------------before");
    }

    @After("execution(* com.gzk.service.UserServiceImpl.*(..))")
    public void after() {
        System.out.println("------------after");
    }

    //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点
    @Around("execution(* com.gzk.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");

        Signature signature = jp.getSignature();//获取签名
        System.out.println(signature);


        Object proceed = jp.proceed();
        System.out.println("环绕后");

    }
}
```

```xml
<bean id="annotationPointCut" class="com.gzk.diy.AnnotationPointCut"/>
<!--    开启注解支持-->
    <aop:aspectj-autoproxy/>
```

#### 12.mybatis-spring

1.环境搭建

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.49</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.4</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.2</version>
    </dependency>
</dependencies>
```

2.配置

mybat-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>

    <!--别名-->
    <typeAliases>
        <package name="com.gzk.pojo"/>
    </typeAliases>

    <!--设置-->
<!--    <settings>-->
<!--        <setting name="" value=""/>-->
<!--    </settings>-->
    

</configuration>
```

spring-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--使用spring的数据源替换为mybatis的配置-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/girls?useSSL=true"/>
        <property name="username" value="root"/>
        <property name="password" value="010426"/>
    </bean>

    <!--sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--绑定mybatis配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/gzk/mapper/*.xml"/>
    </bean>

    <!--sqlSessionTemplate:就是我们能使用的sqlSession-->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <!--只能用构造器注入sqlSessionFactory，因为没有set方法-->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>

</beans>
```

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

   <import resource="spring-dao.xml"/>

    <bean id="userMapper" class="com.gzk.mapper.UserMapperImpl">
        <property name="sqlSession" ref="sqlSession"/>
    </bean>

</beans>
```

test

```
public void test1() throws IOException {
    ApplicationContext context =
            new ClassPathXmlApplicationContext("applicationContext.xml");
    UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
    List<User> users = userMapper.selectUser();
    for (User user : users) {
        System.out.println(user);
    }
}
```

#### 13.声明式事务

###### 1.ACID原则

原子性，一致性，隔离性（多个业务可能操作同一个资源，防止数据损坏），持久性

###### 2.spring中的事务管理

 spring-dao.xml

```xml
<!--配置声明式事务-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>

<!--结合AOP实现事务的织入-->
<!--配置事务通知-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!--给那些方法配置事务-->
    <!--配置事务的传播特性 propagation=REQUIRED(默认)-->
    <tx:attributes>
        <tx:method name="add" propagation="REQUIRED"/>
        <tx:method name="delete" propagation="REQUIRED"/>
        <tx:method name="update" propagation="REQUIRED"/>
        <tx:method name="query" read-only="true"/>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>

<!--配置事务切入-->
<aop:config>
    <aop:pointcut id="txPointCut" expression="execution(* com.gzk.mapper.*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>

</aop:config>
```

