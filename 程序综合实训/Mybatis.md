# MyBatis

学这个的理由很简单：用JDBC写完了数据库实验的我简直就是一个大**。

MyBatis是持久层的框架，用于简化JDBC开发。

> 持久层：负责将数据保存到数据库的那一层代码。
>
> JavaEE三层架构：表现层(页面展示)、业务层(逻辑处理)、持久层(对数据进行持久化)。
>
> 框架：是一个半成品软件，是一套可重用的、通用的、软件基础代码模型
>
> **在框架的基础之上构建如那件编写更加高效、规范、通用、可扩展**。

## JDBC的缺点与MyBatis改进

存在硬编码：--==配置文件==

- 注册驱动、获取连接(url,username,password)
- SQL语句自行拼接

操作繁琐：--==自动完成==

- 手动设置参数
- 手动封装结果集

**MyBatis免除了几乎所有的JDBC代码以及设置参数和获取结果集的工作。**

## 快速入门

对于查询flights表中的所有数据：

1. 创建DB中table，添加数据
2. 创建模块，导入坐标**--什么是坐标？**
3. 编写MyBatis核心配置文件--替换连接信息
4. 编写SQL映射文件--统一管理SQL语句
5. 编码
   1. 定义POJO类
   2. 加载核心配置文件，获取SqlSessionFactory对象
   3. 获取SqlSession对象，执行SQL语句
   4. 释放资源

```java
public class Demo01 {
    public static void main(String[] args) throws IOException {
        //加载mybatis的核心配置文件，获取SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //获取SqlSession对象，用它来执行SQL
        SqlSession ss=sqlSessionFactory.openSession();
        //执行SQL
        List<Flight> fls=ss.selectList("Schwert.selectAll");
        System.out.println(fls);
        //释放资源
        ss.close();
    }
}
```

这是测试程序，当然，它无可指摘地运行成功了。需要注意的是，在这个程序运行之前，你需要配置两个文件，分别是`mybatis-config.xml`和`FlightsMapper.xml`。

**很容易出错的地方是：**在`maven`的`module`的`pom.xml`下，需要添加两个依赖：

```xml
<dependencies>
        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.11</version>
        </dependency>
        <!--MySQL驱动程序依赖-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.30</version>
        </dependency>
</dependencies>
```

同时，不造是不是版本高了的原因，导致`mybatis-config.xml`中的url要想连接数据库的话，对于后面的限制条件不能使用`;`或是`&`，而是`&amp;`，如下：

```xml
<property name="url" value="jdbc:mysql://127.0.0.1:3306/travelsystem?serverTimezone=GMT%2B8&amp;useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=false "/>
```



## Mapper代理

在上述的操作中，对于查询语句的话是：

```java
List<Flight> fls=ss.selectList("Schwert.selectAll");
```

`"Schwert.selectAll"`是写死在了FlightsMapper的文件中，存在硬编码的问题。使用Mapper代理对象的话：

- 可以使用该类的类方法来操作SQL语句
- 利用idea的代码补全，可以提示在类中有哪些方法，就enter一下补全。

![](https://s1.ax1x.com/2022/10/29/x53z59.png)

但是使用Mapper代理开发需要遵循以下三个规则：

- 定义与**SQL映射文件同名**的Mapper接口，并且将**Mapper接口和SQL映射文件放置在同一目录下**；
- 设置SQL映射文件的namespace属性为Mapper接口全限定命名
- 在Mapper接口中定义方法，方法名就是SQL映射文件中sql语句的id，并保持参数类型和返回值类型一致。

对于`mybatis-config.xml`中加载映射文件，可以采用包扫描的方法：

```xml
<!--【原】加载SQL映射文件-->
<mapper resource="demo/mapper/FlightsMapper.xml"/>

<!--【现】mapper代理开发 包扫描-->
<package name="demo.mapper"/>
```



### 核心配置文件