# JavaWeb

## MySQL

在cmd窗口输入     mysql -uroot -proot

## Junit单元测试

### 测试分类

1. 黑盒测试：不需要写代码。给定输入，看程序能否给出正确输出
2. 白盒测试：需要写代码。关注程序具体的运行流程。**Junit属于白盒测试的一种**

### Junit使用

#### 使用步骤

1. 定义一个测试类（测试用例）

   建议：

   - 类名：待测试的类名+Test          CalculatorTest
   - 包名：xx.xxx.test

2. 定义一个测试方法：可以独立运行

   建议：

   - 方法名：test + 被测试方法名        testAdd()
   - 返回值：void
   - 参数列表：空参

3. 给方法加@Test注解

4. 导入Junit依赖环境

#### 判定结果

1. 红色：失败

2. 绿色：成功

3. 一般使用**断言**操作来处理结果

   Assert.assertEquals(期望的结果，程序实际运行产生的结果)

#### 补充注解

- @Before：
  1. **被@Before修饰的方法**会在所有的测试方法执行前执行
  2. **被@Before修饰的方法**用于资源申请
- @After：
  1. **被@After修饰的方法**会在所有的测试方法执行后自动执行
  2. **被@After修饰的方法**用于资源释放

## 反射

**反射——框架设计的灵魂**

框架：半成品软件。可以在框架的基础上进行软件开发，简化编码

### 定义

将类的各个组成部分封装成其他对象，这就是反射机制

### 好处

1. 可以在程序运行过程中，操作这些对象
2. 可以解耦，提高程序的可扩展性

**Java代码在计算机中经历的过程：**

1. source源代码阶段：放在硬盘中，包括.java文件和.class字节码文件
2. Class对象阶段：通过类加载器，将硬盘中的.class字节码文件加载到内存中
3. Runtime运行时阶段：在内存中创建对象，调用方法运行

### 获取Class对象的方式

1. Class.forName("全类名")：

   - 方式：将.class字节码文件加载进内存，返回Class对象
   - 作用：多用于配置文件，将类名定义在配置文件中

2. 类名.class：

   - 方式：通过类名的属性class获取

   - 作用：多用于参数传递

3. 对象.getClass()

   - 方式：getClass()方法在Object类中定义着
   - 作用：多用于对象的获取字节码

**结论：同一个字节码文件（*.class）在一次程序运行过程中，只会被加载一次，不论通过哪一种方式获取的Class对象都是同一个**

### 使用Class对象

1. 先获取到class对象（有3种方式）

2. 调用class对象的方法来**获取成员变量**：getField()、getFields()、getDeclaredField()、getDeclaredFields()

   - **获取到的成员变量**再调用get方法、set方法可以<u>获得、设置</u>**特定对象的该成员变量的值**

   - getField()、getFields()——**获取public修饰的成员变量**

   - getDeclaredField()、getDeclaredFields()——**获取所有的成员变量**

   - ```
     忽略访问权限控制符的安全检查——成员变量.setAccessible(true)——暴力反射
     ```

3. 调用class对象的方法来**获取构造方法**：getConstructor()、getConstructors()、getDeclaredConstructor()...

   - 使用getConstructor(对应构造方法的参数列表)获得一个**有参(或无参)构造方法对象**
   - **创建对象：**使用获取到的**有参(或无参)构造方法对象**的newInstance(与该构造方法对应的参数列表)方法**创建对象**
   - Declared用于处理当构造方法的访问控制权限为private，此时如果想要利用它创建对象还是要**暴力反射**——**获取到的构造方法.setAccessible(true)**

4. 调用class对象的方法来**获取成员方法**：

   - getMethod()、getMethods()、getDeclaredMethod()、getDeclaredMethods()
   - **获取并执行有参方法**
     - 让class对象调用getMethod(方法名，形参列表)获得一个**成员方法对象**
     - **执行方法：**使用获取到的**成员方法对象**的**invoke(一个对象，实参列表)方法**执行方法
   - **获取并执行无参方法**
     - 让class对象调用getMethod(方法名)获得一个**成员方法对象**
     - **执行方法：**使用获取到的**成员方法对象**的**invoke(一个对象)方法**执行方法
   - **获取方法名**
     - 让已经由class对象获取到的方法对象调用getName方法即可得到该方法的方法名

5. 调用class对象的方法来**获取类名**：getName()

   - 让class对象调用getName()方法获取到**全类名**

```java
//不带Declared方法只能获取到public的成员变量、构造方法、成员方法
//带Declared方法什么访问控制权限的成员都可以获取到，但是要想使用获取到的成员，必须进行暴力反射
//	——成员.setAccessible(true)
```

### 案例

```java
public class ReflectTest {
    //需求：写一个”框架“，在不修改该类任何代码的前提下，创建任意类的对象，执行其中的任意方法
    /*
        1.将需要创建的对象的全类名和需要执行的方法定义在配置文件中
        2.在程序中加载读取配置文件
        3.使用反射技术来加载类文件进内存
        4.创建对象
        5.执行方法
     */
    public static void main(String[] args) throws Exception {
        //1.加载配置文件
            //1.1创建Properties对象
        Properties pro = new Properties();
            //1.2加载配置文件，转换成一个集合
                //1.2.1获取class目录下的配置文件
        ClassLoader classLoader = ReflectTest.class.getClassLoader();
        InputStream is = classLoader.getResourceAsStream("pro.properties");
                //1.2.2加载配置文件
        pro.load(is);

        //2.获取配置文件中定义的数据
        String className = pro.getProperty("className");
        String methodName = pro.getProperty("methodName");

        //3.将类加载进内存
        Class cls = Class.forName(className);

        //4.创建对象——利用构造方法创建一个对象
        Constructor constructor = cls.getConstructor();
        Object obj = constructor.newInstance();

        //5.获取方法
        Method method = cls.getMethod(methodName);

        //6.执行方法
        method.invoke(obj);

    }
}

```

## 注解

### 定义

写给计算机看的、用于说明程序的东西。

### 作用

1. 编写文档：通过注解生成doc文档
2. 编译检查：通过注解让编译器实现基本的编译检查e.g.Override
3. 代码分析：对程序里的代码进行分析（使用反射）

### 内置注解

- @Override：检测被该注解标注地方法**是否继承自父类（接口**）的
- @Deprecated：表示该注解标注地内容**已过时**
- @SuppressWarnings：**压制警告**，一般传递参数all

### 自定义注解

#### 格式

- 元注解
- public @interface 注解名称{}

#### 本质

注解的本质就是一个接口，该接口默认继承自Annotation接口

public interface 注解名称 extends **java.lang.annotation.Annotation**

#### 属性

接口中可以定义的抽象方法

##### 特点

1. 属性的返回值必须是：
   - 基本数据类型
   - String
   - 枚举
   - 注解
   - 以上类型的数组
2. 定义了属性，在使用注解时，需要给属性赋值
   - 定义属性时可以使用default关键字给属性赋默认初值
   - 如果只有一个属性需要赋值，并且该属性的名字是value，那么在使用时可以只传值，不用写属性名
   - 数组赋值时，要使用{}包裹

#### 元注解

**用于描述注解的注解**

- @Target：描述注解**作用的位置**
  - @Target(**value={ElementType.TYPE,ElementType.METHOD,ElementType.FIELD}**)
  - 表示被修饰的注解可以作用在类上、方法上、成员变量上
- @Retention：描述注解**被保留的阶段**
  - 自定义的注解一般都用**@Retention(RetentionPolicy.RUNTIME)**
  - 表示被修饰的注解会保留到class字节码文件中，并被JVM读取到
- @Documented：描述注解**是否被抽取到api文档中**
  - **被@Documented修饰的注解可以被抽取到api文档中**，如果不加这个注解，api文档中就抽取不到该注解
- @Inherited：描述注解**是否被子类继承**
  - **被修饰的注解**如果用来修饰一个父类，则该父类的子类也会自动继承该**被修饰的注解**

### 使用注解

1. 获取被该注解修饰的对象（可以是Class，Method，Field）
2. 获取到该注解对象
   - 用1中获取到的对象调用getAnnotation(注解名称.Class)可以得到该注解对象
   - 其实就是在内存中创建了一个实现了该注解接口的子类实现对象
3. 使用2中获取到的注解对象，调用该注解中的方法，获取相应的属性值

## JDBC

### 概念

Java DataBase Connectivity   Java 数据库连接   Java语言操作数据库

### 本质

其实是官方（Sun公司）定义的一套操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动Jar包。**我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类。**

### 快速入门

1. 导入驱动jar包             mysql-connector-java-5.1.37-bin.jar
   1. 复制mysql-connector-java-5.1.37-bin.jar到项目的libs目录下
   2. 在libs目录上点右键-->Add as Library
2. 注册驱动
3. 获取数据库连接对象  Connection
4. 定义sql语句
5. 获取执行sql语句的对象  Statement
6. 执行sql，接收返回的结果
7. 处理结果
8. 释放资源

### 详解各个对象

#### DriverManager：驱动管理对象

- 功能：

  1. 注册驱动：告诉程序该使用哪一个数据库驱动jar包（mysql5之后的jar包可以省略注册驱动的步骤）

     ```java
     static void registerDriver(Driver driver):注册驱动
     写代码时使用：Class.forName("com.mysql.jdbc.Driver");
     //这句代码含义：将com.mysql.jdbc.Driver这个类的class文件加载进内存，然后这个Driver类中的静态代码块会自动执行，进行注册驱动。该静态代码块如下
     static{
     	try{
     		java.sql.DriverManager.registerDriver(new Driver());
     	}catch(SQLException E){
     		throw new RuntimeException("can't register driver!");
     	}
     }
     ```

  2. 获取数据库连接

     ```java
     方法：static Connection getConnection(String url,String user,String password)
     参数：
     	url:指定连接的路径
     		语法：jdbc:mysql://ip地址(域名):端口号/数据库名称
     		例子：jdbc:mysql://localhost:3306/day3
     		细节：如果连接的是本机的mysql服务器，并且mysql服务器默认端口是3306，则url可以简写为jdbc:mysql:///数据库名称
     	user:要连接的对象的用户名
     	password:要连接的对象的密码
     ```

#### Conncetion：数据库连接对象

- 功能

  1. 获取执行sql的对象

     ```
     Statement createStatement()
     preparedStatement prepareStatement(String sql)
     ```

  2. 管理事务

     ```
     开启事务：setAutoCommit(boolean autoCommit):调用该方法设置参数为false，即开启事务
     提交事务：commit()
     回滚事务：rollback()
     ```

#### Statement：执行sql的对象

1. 执行sql语句

   ```
   1.boolean execute(String sql):
   2.int executeUpdate(String sql):执行DML语句(insert、update、delete)、DDL语句(create、alter、drop)
   	返回值：影响的行数，可以通过影响的行数判断DML语句是否执行成功，返回值>0则执行成功，否则失败
   3.ResultSet executeQuery(String sql):执行DQL(select)语句
   ```

#### ResultSet：结果集对象，封装查询结果

1. boolean next()：游标向下移动一行，判断当前行是否是最后一行末尾（如果是，则返回false，表示没有数据）
2. getXxx(参数)：获取数据
   - Xxx代表数据类型      如：int getInt()   ,    String getString()
   - 参数：
     - int：代表列的编号，从1开始（不是索引）int getInt(1)
     - String：代表列的名称       Double getDouble("balance")

**使用步骤：**

1. 游标向下移动一行
2. 判断是否有数据
3. 获取数据

**练习**

定义一个方法，查询emp表的数据，将其封装成  对象 ，然后装载集合，返回。

1. 定义Emp类
2. 定义方法public List<Emp> findAll(){}
3. 实现方法 select * from emp;

#### PreparedStatement：执行sql的对象

1. SQL注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接，会造成安全性问题

   1. 输入用户名随便，输入密码：a' or 'a' = 'a

   2. sql:

      ```sql
      select * from user where username = 'avkavnln' and password = 'a' or 'a' = 'a'
      ```

2. 解决sql注入问题：使用PreparedStatement对象来解决

3. 预编译的sql：参数使用？作为占位符

4. 步骤：

   1. 导入驱动jar包             mysql-connector-java-5.1.37-bin.jar

      1. 复制mysql-connector-java-5.1.37-bin.jar到项目的libs目录下
      2. 在libs目录上点右键-->Add as Library

   2. 注册驱动

   3. 获取数据库连接对象  Connection

   4. 定义sql语句

      注意：sql的参数使用？作为占位符

   5. 获取执行sql语句的对象  PreparedStatement

      - PreparedStatement Connection.preparedStatement(String sql)

   6. 给？赋值

      - 方法：setXxx(参数1，参数2)
        - 参数1：？的位置编号   从1开始
        - 参数2：？的值

   7. 执行sql，接收返回的结果，不需要传递sql语句

   8. 处理结果

   9. 释放资源

后期都会使用PreparedStatement来完成增删改查的所有操作

1. 可以防止SQL注入
2. 效率更高

### JDBC控制事务

**事务：**一个包含多个步骤的业务操作。如果这个业务操作被事务管理，则这多个步骤要么同时成功，要么同时失败。

**操作：**

1. 开启事务
2. 提交事务
3. 回滚事务

**使用Connection对象来管理事务**

1. 开启事务：setAutoCommit(boolean autoCommit):调用该方法设置参数为false，即开启事务
   - 在执行sql之前开启事务
2. 提交事务：commit()
   - 当所有sql都执行完提交事务
3. 回滚事务：rollback()
   - 在catch中回滚事务

### 抽取JDBC工具类：JDBCUtils

目的：简化书写

分析

1. 注册驱动也抽取
2. 抽取一个方法获取连接对象
   - 需求：不想传递参数（麻烦），还得保证工具类的通用性。
   - 解决方案：配置文件
   - jdbc.properties
     - url = 
     - user = 
     - passward = 
3. 抽取一个方法释放资源

**练习：**

需求：

1. 通过键盘录入用户名和密码
2. 判断用户是否登录成功
   - select * from user where username="" and password="";
   - 如果这个sql有查询结果，则成功，反之则失败

实现步骤：

1. 创建数据库表  user

## 数据库连接池

### 概念

其实就是一个容器，存放数据库连接的容器。

- 当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象。用户访问完之后，会将连接对象归还给容器

### 好处

1. 节约资源
2. 用户访问高效

### 实现

1. 标准接口：DataSource   javax.sql包下的
   1. 方法：
      - 获取连接    getConnection()
      - 归还连接：如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接，而是归还连接
2. 一般我们不去实现它，由数据库厂商来实现
   1. C3P0：数据库连接池实现技术
   2. Druid：数据库连接池实现技术，由阿里巴巴提供的

### C3P0

#### 步骤

1. 导入jar包(2个)       c3p0-0.9.5.2.jar     依赖包：mchange-commons-java-0.2.12.jar
   - 不要忘记导入数据库的驱动jar包   mysql-connector-java-5.1.37-bin.jar
2. 定义配置文件：
   - 名称：c3p0.properties    或者   c3p0-config.xml
   - 路径：直接将文件放在src目录下即可
3. 创建核心对象   数据库连接池对象  ComboPooledDataSource
4. 获取连接：getConnection()

### Druid

#### 步骤

1. 导入jar包    druid-1.0.9.jar
   - 使用Druid也需要导入数据库驱动jar包     mysql-connector-java-5.1.37-bin.jar
2. 定义配置文件：
   - 是properties形式
   - 可以叫任意名称，可以放在任意目录下
3. 加载配置文件。Properties
4. 获取数据库连接池对象：通过工厂类来获取：DruidDataSourceFactory
5. 获取连接   getConnection()

#### 工具类

1. 定义一个类  JDBCUtils
2. 提供静态代码块加载配置文件，初始化连接池对象
3. 提供方法
   1. 获取连接方法：通过数据库连接池获取连接
   2. 释放资源
   3. 获取连接池的方法

## Spring JDBC：JDBC Template

Spring框架对JDBC简单封装。提供了一个JDBCTemplate对象简化JDBC的开发

### 步骤

1. 导入jar包

2. 创建JdbcTemplate对象。依赖于数据源DataSource

   - JdbcTemplate template = new JdbcTemplate(ds)

3. 调用JdbcTemplate的方法来完成CRUD的操作

   1. update():执行DML语句。增删改

   2. queryForMap():查询结果，将结果集封装为map结合

      - 这个方法查询的结果集长度只能是1

      - 将列名作为key，将值作为value，将这条记录封装成一个map集合

   3. queryForList():查询结果，将结果封装为list集合

      - 将每一条记录封装成一个map集合，再将map集合装载到list集合中

   4. query():查询结果，将结果封装为JavaBean对象

      - query的参数：RowMapper

      - 一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean的自动封装

      - ```
        new BeanPropertyRowMapper<Emp>(Emp.class)
        //Emp为最终要封装成的类型名
        ```

   5. queryForObject():查询结果，将结果封装为对象（一般用于查询聚合函数）

4. 练习：

   - 需求：
     1. 修改1号数据的salary为10000
     2. 添加一条记录
     3. 删除刚才添加的记录
     4. 查询id为1的记录，将其封装为map集合
     5. 查询所有记录，将其封装为list
     6. 查询所有记录，将其封装为emp对象的list集合
     7. 查询总记录数



## JavaWeb介绍

### JavaWeb

**用Java语言开发网站**

### 学习内容

1. 数据库：5天
2. 网页前端：5天
3. Web核心技术：15天
4. 项目：5天

## Html

### Web基础概念

#### JavaWeb

用Java语言开发互联网项目

#### 软件架构

1. C/S:     Client/Server      客户端/服务器端
   - 用户本地有一个客户端程序，远程有一个服务器端程序
   - 优点：用户体验好
   - 缺点：开发、安装、部署、维护麻烦
2. B/S:     Browser/Server  浏览器/服务器端
   - 只需要一个浏览器，用户通过输入不同的网址(URL)，就可以访问不同的服务器端程序
   - 优点：开发、安装、部署、维护 简单
   - 缺点：1.如果应用过大，用户体验不好        2.对硬件要求高

#### B/S架构详解

##### 资源分类

1. 静态资源
   - **利用静态网页开发技术发布的资源（静态网页技术：HTML，CSS，JavaScript）**
   - 如：文本、图片、音频、视频，HTML，CSS，JavaScript
   - 特点：所有用户访问到的同一个静态资源都是一样的；
   - 如果浏览器向服务器端请求一个静态资源，服务器端会直接返回该静态资源，浏览器内置的解析引擎可以展示该静态资源
2. 动态资源
   - 利用动态网页技术发布的资源
   - 如：jsp/servlet、php、asp...
   - 特点：不同用户访问到的动态资源可能是不一样的
   - 如果用户向服务器请求的是一个动态资源，服务器会先执行这个动态资源，把它转为静态资源后，再发回给浏览器

静态资源“三剑客”：

- HTML：用于搭建基础网页，展示页面内容
- CSS：用于美化页面、布局页面
- JavaScript：控制页面的元素，让页面有一些动态的效果

## Html基本概念

### Html

<u>超文本</u>标记语言

1. 超文本：超文本是用超链接的方法，将各种不同空间的文字信息组织在一起的网状文本
2. 标记语言：
   - 由标签<标签名称>构成的语言，如html，xml
   - 标记语言不是编程语言

### 快速入门

1. html文档的后缀名是 .html或者.htm
2. 标签分类：
   - 围堵标签：有开始标签和结束标签      如<html></html>
   - **自闭和标签：开始标签和结束标签合二为一**      如<br/>
3. 标签可以嵌套
   - 但是需要正确嵌套，不能你中有我，我中有你
4. **在开始标签中可以定义属性。属性由键值对构成，值需要用引号（单双都行）引起来**
5. html标签不区分大小写，但是建议用小写

### 标签学习

#### 文件标签

构成html最基本的标签

- html5中定义该文档是html文档：<!DOCTYPE html>

- html：html文档的根标签

- head：头标签。用于指定html文档的一些属性。用于引入外部的资源

- title：标题标签

- body：体标签

#### 文本标签

与文本有关的标签

- 注释：<!-- -->
- 标题标签：<h1>
- 段落标签：<p>
- 换行标签：<br>
- 展示一条水平线：<hr>
  - 属性：
    - color：
    - width：宽度
    - size：高度
    - align：对齐方式     center  居中    left  左对齐    right   右对齐
- 字体加粗：<b>
- 字体斜体：<i>
- 字体标签：<font>
- 文本居中：<center>
- 属性定义：
  - color：
    1. 英文单词：
    2. rgb(值1，值2，值3)：值的范围0~255
    3. #值1值2值3：值的范围00~FF之间
  - width：
    1. 数值：width='20'，单位默认为px
    2. 百分比：表示该元素相对于父元素的占比

#### 图片标签

展示图片的标签

```html
<img src="./image/butterfly.jpg" alt="蝴蝶" width="50%" height="50%">
<!--
    相对路径:   以.开头的路径
    ./表示当前目录   如果不写./则默认是从当前目录开始找
    ../表示上一级目录
    从该html文件所在目录出发，一步步找到我们想要的图片
-->
```

#### 列表标签

- 有序列表：

  ol

  li

- 无序列表：

  ul

  li

#### 链接标签

- 定义一个超链接
- 属性：
  - href：指出访问资源的URL(统一资源标识符)
    - 网络URL
    - 本地资源URL
    - mailto:邮箱账号
    - #：代表跳转到本页面
  - target：指出打开资源的方式
    - _self：在当前页面打开
    - _blank：在新窗口打开

#### 块标签

（本身没有任何样式，和CSS结合使用）

- div：每个div默认占满一行      块级标签
- span：文本信息在一行展示     行内标签    内联标签

#### 语义化标签

- html5为了提高程序的可读性，提供了一些标签，这些标签本身没有任何样式
- header
- footer

#### 表格标签

- table：定义表格
  - border：定义表格的边框
  - width：定义表格整体的宽度
  - cellpadding：定义单元格里面的内容和单元格之间的距离
  - cellspacing：定义相邻单元格之间的距离。如果值为0，则单元格之间的线会合并成一条
  - align：对齐方式
- tr：定义行
- td：定义单元格
  - rowspan：用于定义该单元格占几行
  - colspan：用于定义该单元格占几列
  - 合并单元格操作：
    1. 先搞清楚每一行有多少个单元格，先把每一行的单元格删到指定个数
    2. 再在待合并的单元格<td>的属性rowspan、colspan里面给属性值
- th：定义表头单元格
- thead：无任何样式，用来提高程序可读性
- tbody：无任何样式，用来提高程序可读性
- tfoot：无任何样式，用来提高程序可读性

#### 表单标签

- 表单：

  - 定义：用于**采集用户输入的数据、和服务器进行交互**的元素
  - form：**用于定义提交数据的范围**，form标签外的元素不会被提交到服务器
  - 属性：
    - action：指定**数据最终会被提交到哪个URL**
    - method：指定**数据提交的方式**——共7种，常用2种
      - get：
        1. 请求参数会在地址栏中显示
        2. 请求参数的大小有限制
        3. 不太安全
      - post：
        1. 请求参数不会在地址栏中显示，会封装在请求体中(HTTP协议后讲解)
        2. 请求参数的大小没有限制
        3. 较为安全

  **表单项中的数据要想被提交，必须指定它的name属性**

#### 表单项标签

- input：可以通过type属性，改变元素展示的样式
  - **name属性值：代表着提交给服务器的键值对的键**
  - type属性值：
    - text：文本输入框，默认值
    - passward：密码输入框
    - radio：单选框
      1. 要想让多个单选框实现单选的效果，这几个单选框的name属性值必须一样
      2. 要想把指定的值提交给服务器，就要给每个单选框添加一个value属性，属性值代表即将提交的信息
      3. checked属性，可以指定默认选中的值
    - checkbox：复选框
      1. 要想把指定的值提交给服务器，就要给每个复选框添加一个value属性，属性值代表即将提交的信息
      2. checked属性，可以指定默认选中的值
    - file：文件框，用于上传文件
    - hidden：隐藏域，用于提交一些信息
    - **按钮：**
      - type="submit"：提交按钮，用于提交表单
      - type="button"：普通按钮，没啥效果，需要和javascript配合使用
      - type="image"  src=""：图片按钮，用于提交表单
    - 其他type：
      - type="color"：取色器
      - type="date"：日期
      - type="datetime-local"：日期和时间
      - type="number"：输入框内只能填数字，不能填字母或其他
      - type="email"：对输入的数据进行基本的邮箱校验，看是否有@
  - label属性：获取输入项的文字描述信息
    - **label中的 for属性 会和 input中的 id属性对应。如果点击label区域，对应的input区域输入框会获取焦点**
  - select：下拉列表
    - 子元素：option
  - textarea：文本域
    - 属性：cols：指定列数，即每一行可以写多少个字符       rows：指定行数
  - **placeholder属性：用于提示用户输入规则（灰色字体，一点击就消失了），常用于输入框**

## CSS

**用于页面美化和布局控制**

### 概念

层叠样式表：层叠：多个样式可以作用在同一个html元素(即html的标签)上，同时生效

### 好处

1. 功能强大
2. 将内容展示和样式控制分离：降低耦合度，解耦；让分工协作更容易；提高开发效率

### CSS与Html结合方式

1. 内联方式：在html标签内部，使用style属性指定css代码

2. 内部方式：在head标签内，定义style标签，style标签的标签体内容就是css代码

3. 外部方式：

   1.定义css资源文件
   2.在head标签内，定义link标签，引入外部的资源文件

**特点：**三种方式，css作用范围越来越大

### CSS语法格式

**格式：**

选择器 {

​		属性名1：属性值1；

​		属性名2：属性值2；

​		......

​			 }

选择器：筛选具有相似特征的元素

注意：每一对属性都需要使用；隔开，最后一对属性可以不加；

### 基础选择器

1. id选择器：用于选择id为特定值的元素，建议在一个html页面中id值唯一     #id属性值{}
2. 元素选择器：选择具有相同标签名字的元素   标签名字{}
       注意：id选择器的优先级高于元素选择器
3. 类选择器：选择具有相同的class属性值的元素      .class属性值{}
       注意：类选择器的优先级高于id选择器——id选择器>类选择器>元素选择器

### 扩展选择器

1. 选择所有元素：

   - 语法：*{}

2. 并集选择器：

   - 语法：选择器1，选择器2{}

3. 子选择器：筛选选择器1元素下的选择器2元素

   - 语法：选择器1 选择器2

4. 父选择器：筛选选择器2的父元素选择器1

   - 语法：选择器1>选择器2

5. 属性选择器：选择特定元素名称，而且满足特定属性名=特定属性值的元素

   语法：元素名称[属性名=属性值]{}

   ```html
   <input type="text">{
   	border:5px solid;
   }
   ```

6. 伪类选择器：选择一些元素具有的状态

   - 语法：元素：状态{}

   - 如：<a>

     状态：linked：初始化的状态

     ​			hover：鼠标悬浮在链接上的状态

     ​			active：链接正在被访问的状态

     ​			visited：被访问过的状态

### 属性

1. 字体、文本
   - font-size：
   - color：
   - text-align：
   - line-height：
2. 背景
   - background：复合属性
3. 边框
   - border：复合属性
4. 尺寸
   - width：
   - height：
5. 盒子模型：控制布局
   - margin：外边距
   - padding：内边距
     - 默认情况下，修改内边距会影响整个盒子的大小
     - 可以利用box-sizing：border-box;     设置盒子的属性，让width和height就是最终盒子的大小
   - float：浮动
     - left：左浮动
     - right：右浮动

## JavaScript

### 概念

一门客户端脚本语言

- 客户端：运行在客户端浏览器中的。每一个浏览器都有JavaScript的解析引擎
- 脚本语言：不需要编译，直接就可以被浏览器解析执行

### 功能

- 增强用户和html页面的交互过程
- 控制html元素，让页面有一些动态的效果

### 发展

JavaScript = ECMAScript + BOM + DOM

### ECMAScript

客户端脚本语言的标准

#### 基本语法

##### 与html结合的方式

- 内部JS：定义<script>,标签体内容就是JS代码

- 外部JS：定义<script>,通过src属性引入外部的JS文件

- 注意：

  1. <script>可以定义在html页面的任何位置，但是定义的位置会影响执行顺序

  2. <script>可以定义多个

##### 注释

- 单行注释：//注释内容
- 多行注释：/**/

##### 数据类型

1. 原始数据类型(基本数据类型)
   1. number：数字。整数/小数/NaN(not a number 一个不是数字的数字类型)
   2. string：字符串。字符串  “abc”   "a"  'abc'
   3. boolean：true 和 false
   4. null：一个对象为空的占位符
   5. undefined：未定义。如果一个变量没有给初始化值，则会被默认赋值为undefined
2. 引用数据类型：对象

##### 变量

- **变量：一小块存储数据的内存空间**

- Java语言是强类型语言，而JavaScript是弱类型语言。

  - 强类型：在开辟变量存储空间时，定义了空间将来存储的数据的数据类型。只能存储固定类型的数据
  - 弱类型：在开辟变量存储空间时，不定义空间将来存储的数据的数据类型，可以存放任意类型的数据

- 语法：

  var  变量名 = 初始化值；

##### 运算符

- 一元运算符：只有一个运算数的运算符        ++    --     +(正号)

  ```
  +(正号)   -(负号)
  注意：在JS中，如果运算数不是运算符所要求的类型，那么js引擎会自动将运算数进行类型转换
  其他类型转number：
      1.string转number：按照字面值转换。如果字面值不是数字，则转为NaN(不是数字的数字)
      2.boolean转number：true转为1，false转为0
  ```

- 算数运算符：+  -   *   /    %

- 赋值运算符：=    +=    -=

- 比较运算符：<     >    <=     >=   ==    ===(全等于)

  ```
  1.类型相同：直接比较
  2.类型不同：按照字典顺序进行比较。按位逐一比较，直到得出大小为止
      全等于：在进行比较之前，先判断两个变量的类型是否相同，如果不同直接返回false
              全等于既包括两个变量的类型相同，也包括两个变量的值相同
  ```

- 逻辑运算符：&&     ||   !

  ```
  &&：与（短路）
  ||：或（短路）
  ！：非
      其他类型转boolean：
          number转boolean：0和NaN为假，非0为真
          string转boolean：除了空字符串(""),其他都为true
          null&undefined转boolean：都是false
          对象：所有对象都为true
  ```

- 三元运算符：?  :

##### 流程控制语句

- if...else...

- switch:

  在java中，switch语句可以接收的数据类型： byte   short    int    char     枚举    String

  在JS中，switch语句可以接收任意的原始数据类型

- while

- do...while

- for

#### 基本对象

##### function对象

```
function对象：函数(方法)对象
1.创建
    创建方式1：
    function 方法名称(形参列表){
        方法体
    }
    创建方式2：
    var 方法名称 = function(形参列表){
        方法体
    }
2.方法
3.属性
    length属性，代表形参个数
4.特点
    1.形参列表的形式参数不写变量类型,返回值类型也不写
    2.方法是一个对象，对于方法名称相同的方法，后面定义的方法会覆盖前面的，不会像Java一样爆错
    3.在JS中，方法的调用只与方法名称有关，而和它的参数列表无关
    4.在方法声明中，有一个隐藏的内置对象(数组)，arguments，封装所有的实际参数
5.调用
    方法名称(实参列表)
```

##### Array：数组对象

```
Array：数组对象
1.创建：
    1.var arr = new Array(元素列表);
    2.var arr = new Array(size);
    3.var arr = [元素列表];
2.方法：
    join(参数):将数组中的元素按照指定的分隔符拼接成一个字符串
    push():向数组的尾部添加一个或多个元素，并返回新的数组长度
3.属性：
    length:数组的长度
4.特点：
    1.JS中，数组中的元素类型是可变的
    2.JS中，数组长度可变
```

##### Date对象

```
Date：日期对象
1.创建
    var date = new Date();
2.方法
    toLocaleString():返回当前date对象对应的本地字符串格式
    getTime():获取从1970年1月1日0点到当前日期对象描述时间的毫秒值
```

##### Math对象

```
Math：数学对象
1.创建：
    特点：Math对象不用创建，直接使用  Math.方法名();
2.方法：
    random():返回0~1之间的随机数。包含0不包含1
    ceil(x):对数x进行向上舍入
    floor(x):对数x进行向下舍入
    round(x):对数x进行四舍五入
3.属性：
    PI
```

##### RegExp对象

**正则表达式对象**

###### 正则表达式

1. **单个字符：[]**

   如：[a]   [ab]   [a-zA-Z0-9_]

   ​	特殊符号代表特殊含义的单个字符：

   ​		\d：单个数字字符   [0-9]

   ​		\w：单个单词字符   [a-zA-Z0-9_]

2. **量词符号**

   ?：表示出现过0次或1次

   *：表示出现过0次或多次

   +：表示出现过1次或多次

   {m,n}：表示  m<= 数量  <=n
   			m如果缺省，{,n}:表示最多n次

   ​			n如果缺省，{m,}:表示最少m次

3. 标志

   - 开始：^
   - 结束：$

###### 正则对象

1. 创建

   ```
   1.var reg = new RegExp("正则表达式");
   2.var reg2 = /正则表达式/;
   ```

2. 方法

   test(参数)：验证指定的字符串是否符合正则定义的规范

##### Global对象

```
Global对象：
1.特点：
    全局对象，这个Global中封装的方法不需要对象就可以调用。方法名();
2.方法：
    encodeURI():url编码
    decodeURI():url解码

    encodeURIComponent():url编码，编码的字符更多
    decodeURIComponent():url解码

    parseInt():将字符串转为数字
        逐一判断每一个字符是否是数字，直到不是数字为止，将前边数字部分转为number

    isNaN():判断一个值是否是NaN
        NaN六亲不认，连自己都不认。NaN参与的==比较全部为false

    eval():将JavaScript字符串转为它的脚本代码来执行
3.URL编码：
    传智播客 = %E4%BC%A0%E6%99%BA%E6%92%AD%E5%AE%A2
    GBK编码：一个汉字占2个字节
    UTF-8编码：一个汉字占3个字节
```

##### Number对象

##### String对象

##### Boolean对象

#### DOM简单介绍

```
DOM简单学习：为了满足案例需求
功能：控制html文档的内容
代码：获取页面标签(元素)对象  Element
    document.getElementById(id值):通过元素的id值获取元素对象
操作Element对象：
    1.修改属性值：
        1.明确获取的对象到底是哪种标签？是<img>?<h1>?
        2.查看API文档，找出其中有哪些属性可以设置
    2.修改标签体内容
        属性：innerHTML
```

#### 事件简单介绍

```
事件
    功能：某些组件被执行了某些操作之后，触发某些代码的执行
    如何绑定事件：
        1.直接在html标签上，指定事件的属性(操作)，属性值就是JS代码
            事件：onclick---单击事件
        2.通过js获取一个元素对象，指定事件属性，设置一个函数
```

#### BOM

##### 概念

Browser   Object   Model      浏览器对象模型

**将浏览器的各个组成部分封装成对象**

##### 组成

Window：窗口对象

History：历史记录对象

Location：地址栏对象

Navigator：浏览器对象

Screen：显示屏幕对象

##### Window

```
Window窗口对象
    1.创建
    2.方法
        1.与弹出框有关的方法
            alert()---警告框
            confirm()---确认取消框
                如果用户点击确认，则返回true
                如果用户点击取消，则返回false
            prompt(参数)---输入框，参数为输入提示
                返回值为用户输入的值
        2.与打开关闭有关的方法
            open()---打开一个新窗口
                返回一个新的window对象
            close()---关闭浏览器窗口
                谁调用我，我就关谁
        3.与定时器有关的方法
            一次性定时器：指定毫秒后执行一次指定操作
            setTimeout()
                参数：
                    1.js代码或者方法对象
                    2.毫秒值
                返回值：
                    唯一标识，用于取消定时器
            clearTimeout()---取消由setTimeout设置的定时器

            周期定时器：每隔指定毫秒，执行一次指定操作
            setInterval()
            clearInterval()---取消由setInterval()设置的定时器
    3.属性
    	1.获取其他BOM对象
    		history
    		location
    		Navigator
    		Screen
    	2.获取DOM对象
    		document
    4.特点
        1.Window对象不需要创建直接 window使用    window.方法名();
        2.window引用可以省略    方法名();
```

##### Location

```
Location对象
1.创建
	window.location.方法名();
	location.方法名();
2.方法
	reload();
3.属性
	href——可以用于获取和修改网址
```

##### History

```
History：历史记录对象
1.创建
    1.window.history
    2.history
2.方法
    back()
    forward()
    go(参数)
        参数：正数：前进几个历史记录
             负数：后退几个历史记录
3.属性
    length  返回当前窗口历史列表中的URL数量
```

#### DOM

##### 概念

Document Object Model		文档对象模型

将标记语言文档的各个组成部分，封装为对象。可以使用这些对象，对标记语言文档进行CRUD的动态操作

##### 组成

- 核心DOM——针对任何结构化文档的标准模型
  - Document：文档对象
  - Element：元素对象
  - Attribute：属性对象
  - Text：文本对象
  - Comment：注释对象
  - Node：节点对象，其他5个对象的父对象

- XML DOM——针对XML文档的标准模型

- HTML DOM——针对HTML文档的标准模型

##### 核心DOM模型

###### Document

```
1.创建(获取)：在html dom模型中可以使用window对象来获取
        window.document
        document
2.方法
    1.获取Element对象
        1.getElementById():通过id属性值获取元素对象，id属性值一般唯一
        2.getElementsByTagName():根据标签名称获取元素对象们，返回值是一个数组
        3.getElementsByClassName():根据Class属性值获取元素对象们，返回值是一个数组
        4.getElementsByName():根据Name属性值获取元素对象们，返回值是一个数组
    2.创建其他DOM对象
        1.createAttribute(属性名)
        2.createComment()
        3.createElement(标签名)
        4.createTextNode()
3.属性
```

###### Element

```
Element:元素对象
	1.获取/创建：通过document来获取和创建
	2.方法：
		1.removeAttribute():删除属性
		2.setAttribute():设置属性
```

###### Node

```
Node:节点对象
特点：所有dom对象都可以被认为是一个节点
方法：
    CRUD dom树：
        appendChild():向节点的子节点列表的结尾添加新的子节点
        removeChild():删除（并返回）当前节点的指定子节点
        replaceChild():用新节点替换一个子节点
属性：
    parentNode  返回该节点的父节点
```

#### HTML DOM

##### 标签体的设置和获取

**innerHTML**

##### 使用html元素对象的属性

##### 控制元素样式

1. 使用元素的style属性来设置
2. 提前定义好类选择器的样式，然后通过元素的className属性设置其class属性值

## BootStrapt

### 概念

一个前端开发的框架，来自Twitter，基于HTML,CSS,JavaScript，简洁灵活，使web开发更便捷

**框架：**一个半成品软件，开发人员可以在框架基础上，再进行开发，简化编码

**好处：**1.定义了很多的css样式和js插件。我们开发人员可以直接使用这些样式和插件得到丰富的页面效果

​			2.响应式布局。同一套页面可以兼容不同分辨率的设备。

### 快速入门

1. 下载Bootstrap
2. 在项目中将这三个文件夹复制
3. 创建html页面，引入必要的资源文件

### 响应式布局

#### 概念

同一套页面可以兼容不同分辨率的设备

#### 实现

依赖于栅格系统：将一行平均分为12个格子，可以指定元素占几个格子

#### 步骤

1. 定义容器。相当于之前的table
   - 容器分类：
   - container：两边留白
   - container-fluid：100%
2. 定义行。相当于之前的tr       样式：row
3. 定义元素。相当于之前的td。指定该元素在不同的设备上，所占的格子数目，样式col-设备代号-格子数目
   - 设备代号：
     1. xs：超小屏幕   手机(<768px)     col-xs-12
     2. sm：小屏幕    平板(>=768px)
     3. md：中等屏幕    桌面显示器(>=992px)
     4. lg：大屏幕     大桌面显示器(>=1200px)

**注意：**

1. 一行中，如果格子数目超过12，则超出部分自动换行
2. 栅格类属性可以向上兼容。栅格类适用于与屏幕宽度大于或等于分界点大小的设备
3. 如果真实设备宽度小于了设置栅格类属性的设备代码的最小值，会一个元素占满一整行

### CSS样式和js插件

#### CSS全局样式

- 按钮：

  - ```
    class="btn btn-default"
    ```

- 图片：

  - ```
    class="img-responsive":图片随着设备屏幕大小变化
    ```

  - ```
    class="img-rounded"：图片是方形
    class="img-circle"：图片是圆形
    class="img-thumbnail"：图片是相框样式
    ```

- 表格

  - ```
    class="table"   表格效果
    class="table-striped"  条纹状表格效果
    class="table-hover"   鼠标悬停背景色改变效果
    ```

- 表单

  - ```
    <form class="form-horizontal">
    <div class="form-group">
    <label class="col-sm-2 control-label">
    <input type="..." class="form-control">
    ```

    

#### 组件

- 导航条
- 分页条

#### js插件

- 轮播图

### 事件监听机制

#### 概念

**某些组件被执行了某些操作后，触发某些代码的执行。**

- 事件：某些操作。如：单击，双击，键盘按下了，鼠标移动了
- 事件源：组件。如：按钮   文本输入框...
- 监听器：代码。
- 注册监听：**将事件、事件源、监听器结合在一起。**当事件源发生了某个事件，则触发执行某个监听器代码。

#### 常见的事件

##### 点击事件

- onclick：单击事件
- ondblclick：双击事件

##### 焦点事件

- onblur：元素失去焦点——一般用于表单验证
- onfocus：元素获得焦点

##### 加载事件

- onload：一张页面或一幅图像完成加载

##### 鼠标事件

- onmousedown：鼠标按键被按下
  - 定义方法时，定义一个形参，接收event对象
  - 通过event对象的button属性，可以获取到鼠标的哪个按键被点击了
- onmouseup：鼠标按键被松开
- onmousemove：鼠标被移动
- onmouseover：鼠标移到某元素之上
- onmouseout：鼠标从某元素上离开

##### 键盘事件

- onkeydown：某个键盘按键被按下
- onkeyup：某个键盘按键被松开
- onkeypress：某个键盘按键被按下并被松开

##### 选择和改变

- onchange：域的内容被改变
- onselect：文本被选中

##### 表单事件

- onsubmit：确认按钮被点击
  - 可以阻止表单提交——方法返回false，表单就不会被提交
- onreset：重置按钮被点击

## XML

#### 概念

Extensible Markup Language     可扩展标记语言

**可扩展：标签都是自定义的**   e.g.  <user>          <student>

#### 功能

**存储数据**

- 配置文件
- 在网络中传输

#### xml与html的区别

1. xml标签都是自定义的，html标签都是预定义的。
2. xml的语法严格，html的语法松散
3. xml用于存储数据，html用于展示数据

#### 语法

##### 基本语法

1. xml文档的后缀名   .xml
2. xml第一行必须定义为文档声明         <? xml version='1.0' ?>
3. xml文档中有且仅有一个根标签
4. 属性值必须使用引号（单双都可）引起来
5. 标签必须正确关闭
6. xml标签名称区分大小写

##### 快速入门

```xml
<?xml version='1.0' ?>

<users>

	<user id='1'>
		<name>zhangsan</name>
		<age>23</age>
		<gender>male</gender>
	
	</user>

	<user id='2'>
		<name>lisi</name>
		<age>24</age>
		<gender>female</gender>
	
	</user>

</users>
```

##### 组成部分

1. 文档声明

   - 格式：<? xml 属性列表 ?>
   - **属性列表：**
     - version：版本号，必须的属性
     - encoding：编码方式。告知解析引擎当前文档使用的字符集，默认值：ISO-8859-1
     - standalone：是否独立        取值：yes：不依赖其他文件    no：依赖其他文件

2. 指令

3. 标签

4. 属性

   - id属性值唯一

5. 文本

   - CDATA区：在该区域中的数据将被原样展示

   - 格式：

     ```
     <![CDATA[ 数据 ]]>
     ```

#### 约束

##### 概念

**规定xml文档的书写规则**

作为框架的使用者（程序员）：

- 能够在xml中引入约束文档
- 能够简单地读懂约束文档

##### 分类

1. DTD：一种简单的约束技术
2. Schema：一种复杂的约束技术

##### DTD

**引入dtd文档到xml文档中**

- 内部文档：将约束规则定义在xml文档中

  格式:<!DOCTYPE 根标签名[dtd文件内容]>

- 外部文档：将约束规则定义在外部的dtd文件中

  - 本地：<!DOCTYPE 根标签名 SYSTEM "dtd文件名">
  - 网络：<!DOCTYPE 根标签名 PUBLIC "dtd文件名" "dtd文件位置的URL">

##### Schema

除了能对标签名称\顺序进行约束,**还能对标签体中内容的类型等进行约束**

#### 解析

##### 操作xml文档

- **解析(读取):将xml文档中的内容读取到内存中**
- 写入:将内存中的数据写入到xml文档中,做持久化的存储

##### 解析xml的方式

1. DOM:将标记语言文档一次性加载进内存,在内存中形成一棵dom树
   - 优点:操作方便,可以对文档进行增删改查
   - 缺点:占内存
2. SAX:逐行读取,基于事件驱动的
   - 优点:不占内存(因为SAX读一行释放一行,内存中永远只有一行)
   - 缺点:只能读取,不能增删改(因为之前的资源都释放掉了)

##### xml常见的解析器

1. JAXP:sun公司提供,支持dom和sax两种思想(烂)
2. DOM4J:一款非常优秀的解析器
3. Jsoup:一款Java的HTML解析器,提供了一套非常省力的API,可通过DOM,CSS及类似于jQuery的操作方法来取出和操作数据
4. PULL:Android操作系统内置的解析器,sax方式的

##### Jsoup

###### 快速入门

1. 导入jar包
2. 获取Document对象
3. 获取对应标签的Element对象
4. 获取数据

```java
//2.获取Document对象，根据xml文档获取
//2.1获取xml文档的文件路径path
String path =JSoupDemo1.class.getClassLoader().getResource("student.xml").getPath();
//2.2根据path新建一个file对象
File file = new File(path);
//2.3作为参数传入JSoup.parse()中，得到Document对象(dom树)
Document dom = Jsoup.parse(file, "utf-8");
//3.获取元素对象 Element
Elements elements = dom.getElementsByTag("name");
Element element = elements.get(0);
//4.获取数据
String name = element.text();
System.out.println(name);
```

###### 对象的使用

1. Jsoup:工具类,可以解析html或xml文档,返回Document

   parse:解析html或xml文档,返回Document

   - parse(File in, String charsetName):解析xml或html文件
   - parse(String html):解析xml或html字符串
   - parse(URL url,  int timeoutMillis):通过网络路径获取指定的html或xml的文档对象

2. Document:文档对象,代表内存中的dom树

   获取Element对象

   - getElementByTag(String tagName):根据标签明朝获取元素对象的集合
   - getElementByAttribute(String key):根据属性名称获取元素对象的集合
   - getElementByAttributeValue(String key,String value):根据对应的属性名和属性值获取元素对象的集合
   - getElementById(String id):根据id属性值获取唯一的element对象

3. Elements:元素Element对象的结合.可以当作  ArrayList<Element> 来使用

4. Element:元素对象

   获取子元素对象

   - getElementsByTag(String tagName):根据标签名称获取元素对象的集合
   - getElementsByAttribute(String key):根据属性名获取元素对象的集合
   - getElementsByAttributeValue(String key,String value):根据对应的属性名和属性值获取元素对象的集合
   - getElementById(String id):根据id属性值获取唯一的element对象

   获取属性值

   - String attr(String key):根据属性名获取属性值

   获取文本内容

   - String text():获取所有子标签的文本内容
   - String html():获取标签体的所有内容(包括子标签的标签和文本)

5. Node:节点对象

   是Document对象和Element对象的父类

###### 快速查询方式

1. selector:选择器
   - 使用方法：Elements select(String cssQuery)
   - 语法：参考Selector类中定义的语法
2. XPath：XPath即为XML路径语言，它是一种用来确定XML(标准通用标记语言的子集)文档中某部分位置的语言
   - 使用Jsoup的XPath需要额外导入jar包
   - 查询w3cschool参考手册，使用xpath的语法完成查询

## web概念回顾

### 软件架构

1. C/S：客户端/服务器端
2. B/S：浏览器/服务器端

### 资源分类

1. 静态资源：
   - 所有用户访问后，得到的结果都是一样的，称为静态资源。
   - 静态资源可以直接被浏览器解析
   - 如html，css，Javascript
2. 动态资源
   - 每个用户访问相同资源后，得到的结果可能不一样，称为动态资源。
   - 动态资源被访问后，需要先转换为静态资源，再返回给浏览器

### 网络通信三要素

- IP：电子设备（计算机）在网络中的唯一标识。
- 端口号：应用程序在计算机中的唯一标识。0~65536
- 传输协议：
  - 基础协议：
    - tcp：安全协议，三次握手。速度稍慢
    - udp：不安全协议。速度快

## web服务器软件

**服务器：** 安装了服务器软件的计算机

**服务器软件：** 接收用户请求，处理请求，做出响应

**web服务器软件：** 接收用户请求，处理请求，做出响应

- 在web服务器软件中，可以部署web项目，让用户通过浏览器来访问这些项目
- web容器

**常见的java相关的web服务器软件：**

- webLogic：oracle公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
- webSphere：IBM公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
- JBOSS：JBOSS公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
- Tomcat：Apache基金组织，中小型的JavaEE服务器，仅仅支持少量的JavaEE规范，开源的，免费的。

JavaEE：Java语言在企业级开发中使用的技术规范的总和，一共规定了13项大的规范。

## Tomcat

### Tomcat目录认识

![image-20210607144919989](https://github.com/ValoraY/NotePictures/blob/main/5.png)

### 基础操作

1. 下载：官网下载

2. 安装：解压压缩包即可

   - 注意：安装目录不要有中文和空格

3. 卸载：删除掉解压后的文件夹即可（重新解压即可重新安装）

4. 启动

   bin/startup.bat，双击运行该文件即可

   访问：浏览器输入：http://localhost:8080     回车访问自己

   ​                                   http://别人的ip:8080      回车访问别人

   **可能遇到的问题：**

   1. 黑窗口一闪而过：

      - 原因：没有正确配置JAVA_HOME环境变量
      - 解决方案：正确配置JAVA_HOME环境变量

   2. 启动爆错：

      - 原因：端口号被占用

      - 解决方案

        - 暴力：找到占用该端口号的进程，杀死该进程

          - 在cmd窗口输入  netstat -ano
          - 在出现的结果中找到你想要的端口号的PID（即为进程号）
          - 打开”任务管理器“，找到对应的进程，杀死该进程

        - 温柔：修改自身的端口号

          - conf/server.xml

          - //修改默认端口号8080
            <Connector port="8080" protocol="HTTP/1.1"
                       connectionTimeout="20000"
                       redirectPort="8443" />

          - 一般会将tomcat的默认端口号8080改为80，因为80端口号是http协议的默认端口号

            - 好处：在访问时，就不用输入端口号

5. 关闭

   - 正常关闭：
     - 点击bin/shutup.bat
     - 在窗口按快捷键ctrl+c
   - 强制关闭：直接点窗口的x

6. 配置

   **部署项目的方式：**

   1. 直接将项目webapps目录下即可。
      - /hello：项目的访问路径--->虚拟目录
      - /hello.html：资源路径
      - 简化部署：将项目打成一个war包，再将war包放置到webapps目录下（war包会自动解压缩）
   2. 配置conf/server.xml文件
      - 在<Host>标签体中配置
      - <Context docBase="项目的实际访问路径" path="虚拟目录" />
      - docBase:项目的实际存放路径
      - path:实际目录
   3. 在conf\Catalina\localhost目录下创建任意名称的xml文件。在文件中编写<Context docBase="项目的实际存放位置" />             虚拟目录：xml文件的名称

### 静态项目和动态项目

**目录结构：**

**Java动态项目的目录结构：**

WEB-INF目录：

- web.xml：web项目的核心配置文件
- classes目录：放置字节码文件的目录
- lib目录：放置依赖的jar包

## Servlet

### 概念

**Server applet:**运行在服务器端的小程序

- **Servlet就是一个接口，定义了Java类被浏览器访问到（被tomcat识别到）的规则**
- 将来我们自定义一个类，实现Servlet接口，复写方法

### 快速入门

1. 创建JavaEE项目

2. 定义一个类，实现Servlet接口

   ```
   public class ServletDemo1 implements Servlet
   ```

3. 实现接口中的抽象方法

4. 配置Servlet

   ```xml
   <!--在web.xml中配置：-->
   <!--配置Servlet-->
       <servlet>
           <servlet-name>demo1</servlet-name>
           <servlet-class>cn.jingyu.web.servlet.ServletDemo1</servlet-class>
       </servlet>
       <servlet-mapping>
           <servlet-name>demo1</servlet-name>
           <url-pattern>/demo1</url-pattern>
       </servlet-mapping>
   ```

   ### 执行原理

   1. 当服务器接收到客户端浏览器的请求后，会解析请求的URL路径，获取到客户端想访问的Servlet的资源路径
   2. 在web.xml文件中查找，看是否有与1获取到的资源路径对应的<url-pattern>标签体内容
   3. 如果有，则再找到对应的<servlet-class>全类名
   4. 得到该全类名之后，tomcat会获取到该类的字节码文件，把它加载进内存，并创建一个该类的对象
   5. 调用该类的方法

### 生命周期

1. 被创建：执行init方法，只执行一次
   - 默认情况下，第一次被访问时，Servlet被创建
   - 可以配置，指定Servlet的创建时机（在web.xml的<servlet>中配置）
     - 当第一次被访问时，创建Servlet——<load-on-startup>的标签体为负数
     - 当服务器启动时，创建Servlet——<load-on-startup>的标签体为0或正整数
   - Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的
     - 多个用户同时访问时，可能存在线程安全问题
     - 解决：尽量不要在Servlet中定义成员变量。即使定义了成员变量，也不要对其进行修改值的操作
2. 提供服务：执行service方法，执行多次
   - 每次访问Servlet时，Service方法都会被调用一次
3. 被销毁：执行destroy方法，只执行一次
   - Servlet被销毁时执行。服务器关闭时，Servlet被销毁。
   - 只有当服务器正常关闭时，destroy方法才被执行。
   - destroy方法在Servlet被销毁之前执行，一般用于释放资源

### Servlet3.0

### 好处

- 支持注解配置，可以不需要web.xml了

### 创建步骤

1. 创建Java项目Project，右键-Add FrameWork Support-Web application-OK
2. 向项目导入Jar包 import javax.servlet.*;(跟着提示Add JavaEE 6 JARS to Project...)
3. 定义一个类，实现Servlet接口
4. 复写Servlet中的方法
5. 在该类上使用@WebServlet注解进行配置
   - @WebServlet("资源路径")

**右键NEW-出现Servlet方法**

File-Project Structure-

![image-20210731203402416](https://github.com/ValoraY/NotePictures/blob/main/6.png)

然后在day14_test模块中，右键NEW即可出现Servlet

### IDEA与tomcat的相关配置

- IDEA会为每一个tomcat部署的项目单独建立一份配置文件

  查看控制台的log：

- 工作空间项目          和      tomcat部署的web项目

  - tomcat真正访问的是“tomcat部署的web项目”，“tomcat部署的web项目”对应着“工作空间项目”的web目录下的所有资源
  - WEB-INF目录下的资源不能被浏览器直接访问
  - 断点调试：使用“小虫子”

### Servlet体系结构

爷爷：Servlet  --   接口

儿子：GenericServlet   --   抽象类

孙子：HttpServlet   --    抽象类

#### GenericServlet

- 将Servlet中的其他抽象方法做了默认空实现，只将service()方法作为抽象

- 将来定义Servlet类时，可以继承(extends)GenericServlet，实现service()方法即可。

#### HttpServlet

- 定义类继承HttpServlet
- 复写doGet()/doPost()方法

### Servlet相关配置

1. urlpattern：Servlet访问路径

   1. 一个Servlet可以定义多个访问路径（用来实现--多个访问路径都能访问到同一个Servlet资源）

      ```
      @WebServlet({"/d4","/dd4","/ddd4"})
      ```

   2. 路径定义规则：

      1. /xxx：路径匹配
      2. /xxx/xxx：多层路径，目录结构
      3. *.do：扩展名匹配

## JavaEE项目创建

IDEA新建JavaEE项目

1. 新建普通的Java项目：File-New-Project-Next-OK
2. 在1建好的Project上增加框架：选中1建好的项目-右键-Add Framework support-Web application-OK
3. 配置tomcat：IDEA右上方-Add Configuration-“+”-Tomcat-Local-配置一些参数-OK

## Http

### 概念

Hyper Text Transfer Protocol      超文本传输协议

- 传输协议：定义了，客户端和服务器端通信时，发送数据的格式
- 特点：
  1. 基于TCP/IP的高级协议
  2. 默认端口号：80
  3. 基于请求/响应模型的：一次请求对应一次响应
  4. 无状态的：每次请求之间相互独立，不能交互数据
- 历史版本
  - 1.0：每一次请求响应都会建立新的连接
  - 1.1：复用连接

### 请求消息数据格式

1. 请求行

   - 请求方式    请求url    请求协议/版本

   - ```
     GET /demo3?username=aaa HTTP/1.1
     ```

   - 请求方式：HTTP有7种请求方式，常用的有2种

     - GET：
       1. 请求参数在请求行中，在url后
       2. 请求的url长度有限制
       3. 不太安全
     - POST：
       1. 请求参数在请求体中
       2. 请求的url长度没有限制
       3. 相对安全

2. 请求头：客户端浏览器要告诉服务器一些信息

   - 请求头名称：请求头值

   - 常见的请求头：

     - User-Agent：浏览器告诉服务器，我访问你使用的浏览器版本信息
       - 可以在服务器端获取到该头的信息，解决浏览器的兼容性问题
     - Referer：http://localhost/login.html
       - 告诉服务器，我（当前请求）从哪里来？
       - 作用：
         1. 防止盗链
         2. 统计流量来源

   - ```
     Host: localhost
     User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:88.0) Gecko/20100101 Firefox/88.0
     Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
     Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
     Accept-Encoding: gzip, deflate
     Connection: keep-alive
     Cookie: Idea-e756d7a8=700da7b9-7c2b-4c40-b911-754b91096779
     Upgrade-Insecure-Requests: 1
     If-Modified-Since: Sat, 12 Jun 2021 07:06:16 GMT
     If-None-Match: W/"268-1623481576901"
     Cache-Control: max-age=0
     ```

3. 请求空行

   - 就是一个空行，用于分隔  POST请求的请求头，和请求体

4. 请求体(正文)

   - 封装POST请求消息的请求参数

   **请求消息的字符串格式**

   ```
   GET /demo3?username=aaa HTTP/1.1
   Host: localhost
   User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:88.0) Gecko/20100101 Firefox/88.0
   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
   Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
   Accept-Encoding: gzip, deflate
   Connection: keep-alive
   Cookie: Idea-e756d7a8=700da7b9-7c2b-4c40-b911-754b91096779
   Upgrade-Insecure-Requests: 1
   If-Modified-Since: Sat, 12 Jun 2021 07:06:16 GMT
   If-None-Match: W/"268-1623481576901"
   Cache-Control: max-age=0
   ```

   ```
   POST /demo3 HTTP/1.1
   Host: localhost
   User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:88.0) Gecko/20100101 Firefox/88.0
   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
   Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
   Accept-Encoding: gzip, deflate
   Connection: keep-alive
   Cookie: Idea-e756d7a8=700da7b9-7c2b-4c40-b911-754b91096779
   Upgrade-Insecure-Requests: 1
   If-Modified-Since: Sat, 12 Jun 2021 07:06:16 GMT
   If-None-Match: W/"268-1623481576901"
   Cache-Control: max-age=0
   
   username=aaa
   ```

## Request

### Request对象和Response对象的原理

1. tomcat服务器会根据请求消息中的资源路径，创建对应的ServletDemo1对象。
2. tomcat服务器会创建request和response对象，request对象中封装请求消息数据。
3. tomcat将request和response两个对象传递给service方法，并且调用service方法。
4. **<u>程序员（我们），可以通过request对象获取请求消息数据，通过response对象设置响应消息数据。</u>**
5. 服务器在给浏览器做出响应之前，会从response对象中拿到程序员设置的响应消息数据。

**注意：**

- request和response对象是由服务器创建的。我们只是来使用它们。
- request对象用来获取请求消息，response对象用来设置响应消息

### Request对象继承体系结构

```
ServletRequest  --  接口
	|  继承
HttpServletRequest  --  接口
	|  实现
org.apache.catalina.connector.RequestFacade  --  类(tomcat实现的)
```

### Request功能

#### 获取请求消息数据

#### 获取请求行数据

```
GET /day14/demo1?name=zhangsan HTTP/1.1
```

方法：

1. 获取请求方式：GET
   - String getMethod()
2. 获取虚拟目录：/day14
   - String getContextPath()
3. 获取Servlet路径：/demo1
   - String getServletPath()
4. 获取get方式请求参数：name=zhangsan
   - String getQueryString()
5. 获取请求URI：/day14/demo1
   - String getRequestURI()       /day14/demo1
   - StringBuffer getRequestURL()    http://localhost/day14/demo1
   - URI：统一资源标识符         /day14/requestDemo1                              共和国
   - URL：统一资源定位符        http://localhost/day14/requestDemo1   中华人民共和国
6. 获取协议及版本：HTTP/1.1
   - String getProtocol()
7. 获取客户机的IP地址：
   - String getRemoteAddr()

#### 获取请求头数据

**方法：**

- **String getHeader(String name)：通过请求头的名称获取请求头的值**
- Enumeration<String> getHeaderNames()：获取所有的请求头名称

#### 获取请求体数据

**请求体：**只有post请求方式，才有请求体，在请求体中封装了post请求的请求参数

步骤：

1. 获取流对象

   ```
   BufferedReader getReader():获取字符流对象
   ServletInputStream getInputStream():获取字节流对象
   ```

2. 再从流对象中拿数据

#### 其他功能

##### 获取请求参数通用方式

1. String getParameter(String name):根据参数名称获取参数值    username=zs&password=123

2. String[] getParameterValues(String name):根据参数名称获取参数值的数组   hobby=xx&hobby=game

3. Enumeration<String> getParameterNames():获取所有请求的参数名称

4. Map<String,String[]> gteParameterMap():获取所有参数的map集合

   **中文乱码问题**

   get方式：tomcat 8 已经将get方式乱码解决了

   post方式：会乱码

   解决：**在获取参数前，设置请求request的编码           request.setCharacterEncoding("utf-8");**

##### 请求转发

###### 定义

一种在**服务器内部**的资源跳转方式

###### 步骤

1. 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispacher(String path)
2. 使用RequestDispatcher对象进行转发：forward(ServletRequest request, ServletReaponse response)

###### 特点

1. 浏览器地址栏路径不发生变化
2. 只能转发到当前服务器内部资源中
3. 转发是一次请求

##### 共享数据

域对象：一个有作用范围的对象，可以在范围内共享数据

**request域：代表一次请求的范围，一般用于请求转发的多个资源中去共享数据**

###### 方法

1. void setAttribute(String name,Object obj):存储数据
2. Object getAttribute(String name):通过键来获取值
3. void removeAttribute(String name):通过键来移除值

##### 获取ServletContext

ServletContext getServletContext()

### 新建Servlet文件配置

**如何让“右键-NEW-Servlet"出现？**

File-Project Structure-WEB-Source Roots-勾选

## 案例-用户登录

### 案例需求

​	1.编写login.html登录页面
​		username & password 两个输入框
​	2.使用Druid数据库连接池技术,操作mysql，day14数据库中user表
​	3.使用JdbcTemplate技术封装JDBC
​	4.登录成功跳转到SuccessServlet展示：登录成功！用户名,欢迎您
​	5.登录失败跳转到FailServlet展示：登录失败，用户名或密码错误

![img](https://github.com/ValoraY/NotePictures/blob/main/7.png)

### 开发步骤

1. 创建项目，导入html页面，配置文件，jar包

2. 创建数据库环境

   ```mysql
   CREATE DATABASE day14;
   
   USE day14;
   
   CREATE TABLE USER(
   id INT PRIMARY KEY AUTO_INCREMENT,
   username VARCHAR(32) UNIQUE NOT NULL,
   passward VARCHAR(32) NOT NULL
   );
   ```

3. 创建包cn.itcast.domain，创建类User，对应着数据库中的表USER

   ```java
   package cn.itcast.domain;
   
   //用户的实体类
   public class User {
       private int id;
       private String username;
       private String password;
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public String getUsername() {
           return username;
       }
   
       public void setUsername(String username) {
           this.username = username;
       }
   
       public String getPassword() {
           return password;
       }
   
       public void setPassword(String password) {
           this.password = password;
       }
   
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", username='" + username + '\'' +
                   ", password='" + password + '\'' +
                   '}';
       }
   }
   ```

4. 创建包cn.itcast.dao，创建类UserDao，提供login方法

5. 编写cn.iitcast.web.servlet.LoginServlet类

6. login.html中form表单的action路径的写法

   - 虚拟目录+Servlet的资源路径

### BeanUtils工具类

#### 用途

用于封装JavaBean类

#### JavaBean

标准的Java类

要求：

1. 类必须被public修饰
2. 必须提供空参的构造器
3. 成员变量必须使用private修饰
4. 提供public修饰的setter和getter方法

功能：封装数据

概念：

1. 成员变量
2. 属性：setter和getter方法截取后的产物
   - 例如：getUsername  -->   Username   -->  username

方法：

1. setProperty():设置对象的属性值（操作的是属性的值！不是成员变量的值！）

   ```
   //给user对象的username的属性名赋一个属性值zhangsan
   BeanUtils.setProperty(user,"username","zhangsan");
   ```

2. getProperty()：获取对象的属性值

   ```
   //获取user对象的属性名为“gender”的属性值
   String gender =  BeanUtils.getProperty(user,"gender");
   ```

3. **populate(Object obj,Map map)：将map集合的键值对信息，封装到对应的JavaBean对象中**

## HTTP协议

**请求消息：**客户端发送给服务器端的数据

- 数据格式：
  1. 请求行
  2. 请求头
  3. 请求空行
  4. 请求体

### 响应消息

**响应消息：**服务器端发送给客户端的数据

- 数据格式：

  1. 响应行

  2. 响应头

  3. 响应空行

  4. 响应体

     ```html
     HTTP/1.1 200 OK														<!--响应行-->
     Bdpagetype: 1														<!--响应头-->
     Bdqid: 0xd486bb39000899ea
     Cache-Control: private
     Connection: keep-alive
     Content-Encoding: gzip
     Content-Type: text/html;charset=utf-8
     Date: Tue, 03 Aug 2021 08:38:25 GMT
     Expires: Tue, 03 Aug 2021 08:37:52 GMT
     Server: BWS/1.1
     Set-Cookie: BDSVRTM=0; path=/
     Set-Cookie: BD_HOME=1; path=/
     Set-Cookie: H_PS_PSSID=34298_34336_34369_31660_34374_33848_34283_34094_26350_34242_34367; path=/; domain=.baidu.com
     Strict-Transport-Security: max-age=172800
     Traceid: 1627979905037802215415314133436408044010
     X-Frame-Options: sameorigin
     X-Ua-Compatible: IE=Edge,chrome=1
     Transfer-Encoding: chunked
     																	<!--响应空行-->
     <!DOCTYPE html><!--STATUS OK-->                             	     <!--响应体-->
         <html><head>meta http-equiv="Content-Type" content="text/html;charset=utf-8">
         	<script>
                 _manCard = {
                     asynJs : [],
                     asynLoad : function(id){
                         _manCard.asynJs.push(id);
                     }
                 };
                 window._sp_async = 1;
         	</script>
         </html>
     ```

#### 响应行

1. 组成：协议/版本 响应状态码 状态码描述

2. 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态

   - 状态码都是3位数

   - 分类：
     1. 1xx：服务器接收客户端消息，但没有接收完成，等待一段时间后，发送1xx的状态码
     2. 2xx：成功。代表：200
     3. 3xx：重定向。代表：302(重定向)、304(访问缓存)
     4. 4xx：客户端错误。代表：404（请求路径没有对应的资源）405（请求方式没有对应的doXxx方法）
     5. 5xx：服务器端错误。代表：500（服务器内部出现异常）

#### 响应头

1. 格式：头名称：值

2. 常见的响应头：

   1. Content-Type:服务器告诉客户端浏览器本次**响应体的数据格式**以及**编码格式**

      ```
      Content-Type: text/html;charset=utf-8
      ```

   2. Content-disposition：服务器告诉客户端以什么格式打开响应体数据

      - 值：
        - in-line：默认值，在当前页面打开
        - attachment;filename=xxx:以附件形式打开响应体。文件下载

#### 响应体

传输的数据

### Response对象

#### 功能

**设置响应消息**

1. 设置响应行

   ```
   1.格式：HTTP/1.1 200 OK		
   2.设置状态码：setStatus(int sc)
   ```

2. 设置响应头

   ```
   setHeader(String name, String value)
   ```

3. 设置响应体

   ```
   使用步骤：
   	1.获取输出流
   		字符输出流：PrintWriter getWriter()
   		字节输出流：ServletOutputStream getOutputStream()
   	2.使用输出流，将数据输出到客户端浏览器中
   ```

案例：

1. 完成重定向

   *重定向：资源跳转的方式*

   1. 告诉浏览器重定向：状态码302

   2. 告诉浏览器B资源的路径：响应头location：B资源的路径

      ```java
      //1.设置状态码为302
      response.setStatus(302);
      //2.设置响应头location
      response.setHeader("location","/day15/responseDemo2");
      
      //简单的重定向方法：
      response.sendRedirect("/day15/responseDemo2");
      ```

   *重定向的特点：*

   1. 地址栏发生变化
   2. 重定向可以范围跟其他站点（服务器）的资源
   3. **重定向是两次请求**（就意味着有2个request对象），不能使用request对象来共享数据

   *转发特点：*

   1. 地址栏路径不变
   2. 转发只能访问当前服务器下的资源
   3. **转发是一次请求**，可以使用request对象来共享数据

   **forward 和 redirect 区别**

   ***路径写法：***

   1. 路径分类：

      1. 相对路径：通过相对路径不可以确定唯一资源

         - ./index.html
         - 不以/开头，以.开头
         - 规则：找到**当前资源和目标资源**之间的相对位置关系
         - 当前目录：用“./”或者不写
         - 后退一级目录：../

      2. 绝对路径：通过绝对路径可以确定唯一资源

         - 如：http://localhost:8080/day15/responseDemo2         

         - /day15/responseDemo2

         - 以/开头的路径

         - 规则：判断定义的路径是给谁用的？判断请求是从哪里发出的？（如果是客户端发出的，就是客户端在向服务器端请求资源的话，这个路径就是给客户端浏览器使用的）

           1. 给客户端浏览器使用：需要加虚拟目录（项目的访问路径）**重定向是客户端向服务器请求资源**          超链接、form表单路径......

              **动态获取虚拟目录：**

              ```
              String contextPath = request.getContextPath();
              ```

           2. 给服务器使用：不需要加虚拟目录  **转发是服务器内部在互相请求资源**

2. 服务器输出字符数据到浏览器

   步骤：

   1. 获取字符输出流

   2. 输出数据

      **乱码原因**：编码和解码使用的字符集不一致

      编码：服务器Servlet输出数据是编码，**response获取出来的字符输出流的编码是ISO-8859-1**

      解码：客户端浏览器解析数据是解码，与操作系统有关，**windows中文操作系统默认字符集为GBK（GB2312）**

      解决办法：

      ```java
      //1.获取流对象之前，设置流的默认编码ISO-8859-1  设置为：utf-8
      response.setCharacterEncoding("utf-8");
      
      //2.告诉浏览器，服务器发送的消息体采用的编码。建议浏览器使用该编码解码
      response.setHeader("content-type","text/html;charset=utf-8");
      
      //也可以只有第2步！！！既设置了流的编码，又告诉了客户端浏览器该用什么编码
      
      //简单形式来设置编码,既设置了流的编码，又告诉了客户端浏览器该用什么编码
      response.setContentTyoe("text/html;charset=utf-8")
      ```

3. 服务器输出字节数据到浏览器

   步骤：

   1. 获取字符输出流
   2. 输出数据

4. 验证码

   本质：图片

   目的：防止恶意表单注册

### ServletContext对象

#### 概念

代表整个web应用，可以和程序的容器（服务器）来通信

#### 功能

1. 获取MIME类型：

   - MIME类型：在互联网通信过程中定义的一种文件数据类型

     - 格式：大类型/小类型       image/jpeg

     - 获取：

       ```
       String getMimeType(String file)
       ```

2. 域对象：共享数据

   1. setAttribute(String name,Object value):

   2. getAttribute(String name):

   3. removeAttribute(String name):

      ServletContext对象的范围：所有用户所有请求的数据

3. 获取文件的真实路径（服务器路径）

   - 方法：String   getRealPath(String Path)

     ```java
     //获取文件的服务器路径
     //web目录下的资源访问         "/b.txt"
     //WEB-INF目录下的资源访问     "/WEB-INF/c.txt"
     //src目录下的资源访问         "/WEB-INF/classes/a.txt"
     
     String b = context.getRealPath("/b.txt");//web目录下的资源访问
     //D:\IDEAWorkSpace\day15_response\out\artifacts\day15_response_war_exploded\b.txt
     String c = context.getRealPath("/WEB-INF/c.txt");//WEB-INF目录下的资源访问
     //D:\IDEAWorkSpace\day15_response\out\artifacts\day15_response_war_exploded\WEB-INF\c.txt
     String a = context.getRealPath("/WEB-INF/classes/a.txt");//src目录下的资源访问
     //D:\IDEAWorkSpace\day15_response\out\artifacts\day15_response_war_exploded\WEB-INF\classes\a.txt
     ```

#### 获取

1. 通过request对象获取

   ```
   ServletContext context1 = request.getServletContext();
   ```

2. 通过HttpServlet获取

   ```
   ServletContext context2 = this.getServletContext();
   ```

### 案例-文件下载

文件下载需求：

​	1.页面显示超链接

2. 点击超链接后弹出下载提示框
3. 完成图片文件下载

分析：

1. 超链接指向的资源如果能够被浏览器解析，则在浏览器中展示，如果不能解析，则弹出下载提示框。不满足需求

2. 任何资源都必须弹出下载提示框

3. 使用响应头，设置资源的打开方式：

   Content-disposition：attachment;filename=xxx以附件形式弹出

步骤：

1. 定义页面，编辑超链接的href属性，让它指向Servlet，传递资源名称·filename
2. 定义Servlet：
   1. 获取文件名称
   2. 加载文件进内存（使用字节输入流）
   3. 指定response的响应头：Content-disposition：attachment;filename=xxx以附件形式弹出
   4. 将数据写出到response输出流

问题：中文文件名的问题

解决思路：

1. 获取客户端浏览器使用的浏览器版本信息

2. 根据不同的版本信息，响应不同的数据（设置filename的编码方式不同）

   ```java
   //1.获取user-agent请求头
   String agent = request.getHeader("user-agent");
   //2.使用工具类方法编码文件名即可
   filename = DownloadUtils.getFilename(agent,filename);
   ```

## 会话技术

### 会话

一次会话中包含多次请求和响应

1. 一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止
2. 功能：在一次会话的范围内的多次请求间，共享数据
3. 方式：
   1. 客户端会话技术（将数据存到客户端）：Cookie
   2. 服务器端会话技术（将数据存到服务器端）：Session

### Cookie

#### 概念

客户端会话技术，将数据保存到会话技术

#### 快速入门

**步骤：**

1. 创建Cookie对象，绑定数据

   ```
   new Cookie(String name,String value)
   ```

2. 发送Cookie

   ```
   response.addCookie(Cookie cookie)
   ```

3. 获取Cookie，拿到数据

   ```
   Cookie[]  request.getCookies()
   ```

#### 实现原理

基于响应头set-cookie和请求头cookie实现

![image-20210805105235149](https://github.com/ValoraY/NotePictures/blob/main/8.png)

#### 细节处理

1. 一次可不可以发送多个cookie？
   - 可以
   - 可以创建多个cookie对象，使用response调用多次addCookie方法发送cookie即可
2. cookie在浏览器中保存多长时间？
   - 默认情况下，当浏览器关闭后，Cookie数据被销毁（Cookie存在浏览器的内存中）
   - 持久化存储：
     - setMaxAge(int seconds):
       - 正数：将Cookie数据写到硬盘的文件中。持久化存储。参数代表cookie存活时间。（时间到了，硬盘中存储cookie数据的文件会自动删除）
       - 负数：默认值。当浏览器关闭后，Cookie数据被销毁（Cookie存在浏览器的内存中）
       - 零：删除cookie信息
3. cookie能不能存中文？
   - 在tomcat 8之前，cookie中不能直接存储中文数据
     - 需要将中文数据转码---一般采用URL编码（%E3）
   - 在tomcat 8之后，cookie中可以直接存储中文数据。特殊字符还是不支持，建议使用URL编码，URL解码来解析
4. cookie共享问题？
   - 假设在一个tomcat服务器中，部署了多个web项目，那么在这些web项目中cookie能不能共享？
     - 默认情况下cookie不能共享
   - setPath(String path)：设置cookie的获取范围。默认情况下，path设置为当前项目的虚拟目录
     - path = "/" ：当前服务器下的所有项目都可以共享这个cookie
   - 不同的tomcat服务器间cookie共享问题？
     - setDomain(String path)：如果设置path的一级域名相同，哪儿买多个服务器之间可以共享cookie
       - setDomain(".baidu.com")，那么tieba.baidu.com和news.baidu.com中的cookie可以共享

#### 特点

1. cookie存储数据在客户端浏览器
2. 浏览器对于单个cookie(4kb)的大小有限制  以及  对同一个域名下的总cookie数量(20个)也有限制

#### 作用

1. cookie一般用于存储少量的不太敏感的数据
2. **在不登录的情况下，完成服务器对客户端的身份识别**

#### 案例

**记住上一次访问时间**

案例需求：
 	1. 访问一个Servlet，如果是第一次访问，则提示：您好，欢迎您首次访问。
 	2. 如果不是第一次访问，则提示：欢迎回来，您上次访问时间为:显示时间字符串

分析：

1. 可以采用cookie来完成
2. 在服务器中的Servlet来判断是否有一个名为lastTime的cookie
   1. 有：不是第一次访问
      1. 响应数据：欢迎回来，您上次访问时间为:2021年8月5日11:50:01
      2. 写回cookie：lastTime=2021年8月5日11:52:01
   2. 没有：是第一次访问
      1. 响应数据：您好，欢迎您首次访问。
      2. 写回Cookie：lastTime=2021年8月5日11:50:01

### Session

#### 概念

服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中。

#### 快速入门

HttpSession对象：

1. Object getAttribute(String name)：
2. void setAttribute(String name,Object value)：
3. void removeAttribute(String name)：

#### 步骤

1. 获取HttpSession对象

   ```java
   HttpSession session = request.getSession();
   ```

2. 使用HttpSession对象

   ```
   1. Object getAttribute(String name)：
   2. void setAttribute(String name,Object value)：
   3. void removeAttribute(String name)：
   ```

#### 原理

**服务器如何确保在一次会话范围内，多次获取的Session对象是同一个？**

**Session的实现是依赖于Cookie来实现的**

![img](https://github.com/ValoraY/NotePictures/blob/main/111.png)

#### 细节

1. 当客户端关闭后，服务器不关闭，两次获取session是否为同一个？

   - 默认情况下，不是。

   - 如果需要相同，则可以创建Cookie，键为JSESSIONID，值为session.getId()，设置最大存活时间，让cookie持久化保存

     ```java
     Cookie c = new Cookie("JSESSIONID",session.getId());
     c.setMaxAge(60*60);
     response.addCookie(c);
     ```

2. 客户端不关闭，服务器关闭后，两次获取的session是否为同一个？

   不是同一个，但是要确保数据不丢失

   session的钝化：

   - 在服务器正常关闭之前，将session对象**序列化**到硬盘上

   session的活化

   - 在服务器启动后，将session文件转化为内存中的session对象即可（**反序列化**）

3. session什么时候被销毁？

   1. 服务器关闭
   2. session对象调用invalidate()
   3. session默认失效时间是30分钟
      - 可以选择性配置修改：在web.xml里的session-config

#### 特点

1. session用于存储一次会话的多次请求的数据，存在服务器端
2. session可以存储任意类型、任意大小的数据

**session与cookie的区别**

1. session存储数据在服务器端，cookie在客户端
2. session没有数据大小限制，cookie有
3. session数据安全，cookie相对不安全

#### 案例：验证码

案例需求：
 	1. 访问带有验证码的登录页面login.jsp
 	2. 用户输入用户名，密码以及验证码。
     * 如果用户名和密码输入有误，跳转登录页面，提示:用户名或密码错误
     * 如果验证码输入有误，跳转登录页面，提示：验证码错误
     * 如果全部输入正确，则跳转到主页success.jsp，显示：用户名,欢迎您

分析：

![img](https://github.com/ValoraY/NotePictures/blob/main/9.png)

## JSP入门

### 概念

Java Server Pages：java服务器端页面

- 可以理解为：一个特殊的页面，其中既可以直接定义html标签，又可以定义java代码
- 用于简化书写！！！

### 原理

**JSP本质上就是一个Servlet**

![img](https://github.com/ValoraY/NotePictures/blob/main/10.png)

### 脚本

JSP定义Java代码的方式

1. <%        代码          %>：定义的Java代码在service方法中。service方法中可以定义什么，该脚本中就可以定义什么
2. <%!        代码          %>：定义的Java代码，在jsp转换后的java类的成员位置。可以定义成员变量、成员方法、静态代码块（用得很少）
3. <%=        代码          %>：定义的Java代码，会输出到页面上，定义在service方法里。输出语句中可以定义什么，这个就可以定义什么。

### 内置对象

在jsp页面中不需要获取和创建，可以直接使用的对象

jsp一共有9个内置对象

1. request
2. response
3. out：字符输出流对象。可以将数据输出到页面上。和response.getWriter()类似
   - response.getWriter()和out的区别：
   - 在tomcat服务器给客户端做出响应之前，会先找response缓冲区的数据，再找out缓冲区的数据。
   - response.getWriter()的数据输出永远在out.write()之前

## JSP

### 指令

#### 作用

用于配置JSP页面，导入资源文件

#### 格式

<%@ 指令名称 属性名1=属性值1 属性名2=属性值2%>

#### 分类

1. page：用于配置JSP页面

   1. contentType：等同于response.setContentType()，设置响应体的MIME类型和字符集
      1. 设置响应体的MIME类型和字符集
      2. 设置当前jsp页面的编码（也可以通过设置pageEncoding属性来设置当前页面的字符集）
   2. import：用于导包
   3. erroePage：当前页面发生异常后，会自动跳转到错误页面
   4. isErrorPage：标识当前页面是否是错误页面
      1. 当isErrorPage="true"之后，在JSP中就可以使用内置对象exception
      2. 当isErrorPage="false"之后，默认值，不可以使用内置对象exception

2. include：完成页面包含。导入页面的资源文件

   ```jsp
   <%@ include file="top.jsp"%>
   ```

3. taglib：用于导入资源

   ```jsp
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   ```

### 注释

1. html注释

   <!--      -->：只能注释html代码片段

2. jsp注释：推荐使用

   <%--                 --%>：可以注释所有

### 内置对象

在JSP页面中不需要创建和获取，直接使用的对象

一共有9个

|             |      真实类型       |                    作用                     |
| :---------: | :-----------------: | :-----------------------------------------: |
| PageContext |     PageContext     | 当前页面共享数据，还可以获取其他8个内置对象 |
|   request   | HttpServletRequest  |  一次请求访问多个资源之间共享数据（转发）   |
|   session   |     HttpSession     |       一次会话的多个请求之间共享数据        |
| application |   ServletContext    |             所有用户间共享数据              |
|  response   | HttpServletResponse |                  响应对象                   |
|    page     |       Object        |       当前页面(Servlet)的对象    this       |
|     out     |      JspWriter      |        输出对象，把数据输出到页面上         |
|   config    |    ServletConfig    |              Servlet的配置对象              |
|  exception  |      Throwable      |                  异常对象                   |

## MVC开发模式

1. M：Model：模型。**JavaBean**
   - 完成具体的**业务操作**，如：查询数据库，封装对象
2. V：View：视图。**JSP**
   - **展示数据**
3. C：Controller：控制器。**Servlet**
   - 获取用户的输入
   - 调用模型
   - 将模型返回的数据交给视图进行展示

![img](https://github.com/ValoraY/NotePictures/blob/main/11.png)

优点：

1. 耦合性低，方便维护，利于分工协作
2. 重用性高

缺点：

- 使得项目架构变得复杂，对开发人员要求高

## EL表达式

#### 概念

Expression Language    表达式语言

#### 作用

替换和简化JSP页面中Java代码的编写

#### 语法

${表达式}

#### 注意

1. jsp默认支持el表达式
   1. 如果要忽略EL表达式，设置jsp的page指令中的isELIgnored属性值为true，表示忽略当前jsp页面中所有的EL表达式
   2. "\\${表达式}"：忽略当前这个el表达式

#### 使用

1. 运算
   1. 运算符
      1. 算数运算符： +   -   *    /(div)       %(mod)
      2. 比较运算符：< >  >= !=  == <=
      3. 逻辑运算符：&& (and)           ||(or)                 !(not)
      4. 空运算符：empty
         - 功能：用于判断字符串、集合、数组对象是否为null  或者   长度是否为0
         - ${empty list}：用于判断字符串、集合、数组对象是否为null     或者    长度是否为0
         - ${not empty list}：表示判断字符串、几何、数组是否不为null   并且    长度>0
2. 获取值
   1. el表达式只能从域对象中获取值
   2. 语法：
      1. ${域名称.键名}：从指定域中获取指定键的值
         - 域名称：
           1. PageScope        ----->        PageContext
           2. requestScope   ------>       request
           3. sessionScope   ------->       session
           4. applicationScope  ----->     application(ServletContext)
         - 举例：在request域中存储了name=张三
         - 获取：${requestScope.name}
      2. ${键名}：表示依次从最小的域中去查找是否有该键对应的值，直到找到为止
3. 获取对象、List集合、Map集合的值
   1. 对象：
      - 通过${域名称.键名.属性名}获取对象某属性的值
      - 举例：${requestScope.u.name}：通过requestScope.u获取到键“u”对应的值——对象user，**调用对象user的getName方法获取到name属性的值**（本质）
   2. List集合：${域名称.键名[索引]}
   3. Map集合：
      1. ${域名称.键名.key名称}
      2. ${域名称.键名["key名称"]}

#### 隐式对象

el表达式中不用创建直接拿来用的对象，有11个

PageContext：

1. 获取jsp其他8个内置对象

   ```
   ${PageContext.request.ContextPath}:用于动态获取虚拟目录
   ```

## JSTL标签

#### 概念

JavaServer    Pages     Tag      Library         JSP标准标签库

由Apache组织提供的开源免费的jsp标签

#### 作用

**用于简化和替换jsp页面上的java代码**

#### 使用步骤

1. 导入jstl相关jar包

2. 引入标签库：tablig指令：《%@ taglib %》

3. 使用标签

   1. if

      ```
      c:if标签：
          1.属性
              test必须属性：接收boolean表达式
              如果表达式为true，则显示if标签体内容；
              如果为false，则不显示标签体内容
              一般情况下，test属性值会结合el表达式一起使用
          2.注意：c:if标签没有else情况，想要else情况，则可以再定义一个c:if标签
      ```

   2. choose(相当于java代码的switch)

      ```jsp
      <c:choose>
              <c:when test="${number == 1}">星期一</c:when>
              <c:when test="${number == 2}">星期二</c:when>
              <c:when test="${number == 3}">星期三</c:when>
              <c:when test="${number == 4}">星期四</c:when>
              <c:when test="${number == 5}">星期五</c:when>
              <c:when test="${number == 6}">星期六</c:when>
              <c:when test="${number == 7}">星期日</c:when>
      
              <c:otherwise>数字输入有误</c:otherwise>
      </c:choose>
      ```

      

   3. foreach

      ```
      foreach：
      1.完成重复操作
          for(int i = 0;i < 10;i++){
      
          }
          属性：
          begin:开始值
          end：结束值
          var：临时变量
          step：步长
          varStatus:循环状态对象
              index:容器中元素的索引，从0开始
              count:循环次数从1开始
      2.遍历容器
          List<User> list;
          for(User user : list){
      
          }
          属性：
          items:容器对象（相当于list）
          var:容器中元素的临时变量（相当于user）
          varStatus:循环状态对象
              index:容器中元素的索引，从0开始
              count:循环次数从1开始
      ```

#### 案例

**需求：**

在request域中有一个存有User对象的List集合。需要使用jstl+el将list集合数据展示到jsp页面的表格table中

```jsp
    <%
        List list = new ArrayList();
        list.add(new User("张三",23,new Date()));
        list.add(new User("李四",24,new Date()));
        list.add(new User("王五",25,new Date()));
        request.setAttribute("list",list);

    %>
<table border="1" width="500" align="center">
    <tr>
        <th>编号</th>
        <th>姓名</th>
        <th>年龄</th>
        <th>生日</th>
    </tr>
    <c:forEach items="${list}" var="user" varStatus="s">
        <c:if test="${s.count % 2 == 0}">
            <tr bgcolor="red">
                <td>${s.count}</td>
                <td>${user.name}</td>
                <td>${user.age}</td>
                <td>${user.birStr}</td>
            </tr>
        </c:if>

        <c:if test="${s.count % 2 != 0}">
            <tr bgcolor="green">
                <td>${s.count}</td>
                <td>${user.name}</td>
                <td>${user.age}</td>
                <td>${user.birStr}</td>
            </tr>
        </c:if>
        
    </c:forEach>
    
</table>
```

## 三层架构

#### 软件设计架构

1. 界面层（表示层）：用户看到的界面。用户可以通过界面上的组件和服务器进行交互。
2. 业务逻辑层：处理业务逻辑。
3. 数据访问层：操作数据存储文件。

![img](https://github.com/ValoraY/NotePictures/blob/main/12.png)

#### 案例：用户信息列表展示

**需求**：用户信息的增删改查操作

**设计**：

1. 技术选型：Servlet+JSP+MySQL+JDBCTemplate+Duird+BeanUtils+tomcat

2. 数据库设计：

   ```sql
   create database day17;					--创建数据库
   use day17;								--使用数据库
   create table user(						--创建表
   	id int primary key auto_increment,
   	name varchar(20) not null,
   	gender varchar(20),
   	age int,
   	address varchar(32),
   	qq varchar(20),
   	email varchar(50)
   );
   ```

3. 开发：

   1. 环境搭建

      1. 创建数据库环境
      2. 创建项目，导入需要的jar包

   2. 编码

      ![image-20210806163813099](https://github.com/ValoraY/NotePictures/blob/main/13.png)

   #### 综合练习

   1. 简单功能

      1. 列表查询

      2. 登录

         1. 调整页面（为.jsp），加入验证码功能

      3. 添加

         ![img](https://github.com/ValoraY/NotePictures/blob/main/14.png)

      4. 删除

         ![img](https://github.com/ValoraY/NotePictures/blob/main/15.png)

      5. 修改

         ![img](https://github.com/ValoraY/NotePictures/blob/main/16.png)

   2. 复杂功能

      1. 删除选中

         ![img](https://github.com/ValoraY/NotePictures/blob/main/17.png)

      2. 分页查询

         1. 好处：

            1. 减轻服务器内存的开销
            2. 提升用户体验

            ![img](https://github.com/ValoraY/NotePictures/blob/main/18.png)

         ![img](https://github.com/ValoraY/NotePictures/blob/main/19.png)

      3. 复杂条件查询![img](https://github.com/ValoraY/NotePictures/blob/main/20.png)

4. 测试

5. 部署运维

## Filter：过滤器

### 概念

web中的过滤器：当访问服务器的资源时，过滤器可以将请求拦截下来，完成一些特殊的功能。

过滤器的作用：

- 一般用于完成一些通用的操作，比如登录验证、统一编码处理、敏感字符过滤......

### 快速入门

#### 步骤

1. 定义一个类，让其实现接口Filter

2. 复写方法

3. 配置拦截路径

   1. web.xml

   2. 注解

      ```
      @WebFilter("/*")//拦截路径
      ```

### 细节

#### web.xml配置

```
<filter>
    <filter-name>demo1</filter-name>
    <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
</filter>

<filter-mapping>
    <filter-name>demo1</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

#### 过滤器执行流程

filter被执行--->放行--->访问资源--->返回继续执行filter剩余部分

1. 执行过滤器
2. 执行放行后的资源
3. 回来执行过滤器放行代码下边的代码

#### 过滤器生命周期方法

1. init：在服务器启动后，会创建Filter对象，调用init方法。执行一次。用于加载资源
2. doFilter：每一次请求被拦截资源时，会执行。执行多次
3. destroy：在服务器关闭后，Filter对象被销毁。如果服务器是正常关闭，则会执行destroy方法。执行一次。用于释放资源

#### 过滤器配置详解

##### 拦截路径配置

1. 具体资源路径：/index.jsp      只有访问index.jsp资源时，过滤器才会被执行
2. 目录拦截：/user/*                  访问/user下的所有资源时，过滤器都会被执行
3. 后缀名拦截：*.jsp                   访问所有后缀名为jsp的资源时，过滤器都会被执行
4. 拦截所有资源：/*                     访问所有资源时，过滤器都会被执行

##### 拦截方式配置

资源被访问的方式

1. 注解配置

   - 设置dispatcherTypes属性
     - REQUEST：默认值。浏览器直接请求资源
     - FORWARD：转发访问资源
     - INCLUDE：包含访问资源
     - ERROR：错误跳转资源
     - ASYNC：异步访问资源

2. web.xml配置

   ```
   配置<dispatcher></dispatcher>标签
   ```

#### 过滤器链（配置多个过滤器）

**执行顺序**

如果有2个过滤器：过滤器1、过滤器2

1. 过滤器1
2. 过滤器2
3. 资源执行
4. 过滤器2
5. 过滤器1

**过滤器先后顺序问题**

1. 注解配置：按照类名的字符串比较规则比较，值小的先执行
2. web.xml配置：<filter-mapping>谁定义在上面，谁就先执行

#### 案例1_登录验证

需求：

 1. 访问day17_case案例的资源。验证其是否登录

 2. 如果登录了，则直接放行。

 3. 如果没有登录，则跳转到登录页面，提示"您尚未登录，请先登录"。

    ![img](https://github.com/ValoraY/NotePictures/blob/main/21.png)

#### 案例2_敏感词汇过滤

需求：
 	1. 对day17_case案例录入的数据进行敏感词汇过滤
 	2. 敏感词汇参考《敏感词汇.txt》
 	3. 如果是敏感词汇，替换为 *** 

![img](https://github.com/ValoraY/NotePictures/blob/main/22.png)

##### 分析

1. 对request对象进行增强。增强获取参数相关方法
2. 放行。传递代理对象

增强对象的功能：

- 设计模式：一些通用的解决固定问题的方式

  1. 装饰模式

  2. 代理模式

     - 概念：
       1. 真实对象：被代理的对象
       2. 代理对象
       3. 代理模式：代理对象代理真实对象，达到增强真实对象功能的目的

     - 实现方式：
       1. 静态代理：有一个类文件描述代理模式
       2. 动态代理：在内存中形成代理类
          - 实现步骤：
            1. 代理对象和真实对象实现相同的接口
            2. 代理对象 = Proxy.newProxyInstance();
            3. 使用代理对象来调用方法
            4. 增强方法
          - 增强方式：
            1. 增强参数列表
            2. 增强返回值类型
            3. 增强方法体执行逻辑



## Listener：监听器

概念：web的三大组件之一

### 事件监听机制

- 事件：一件事情
- 事件源：事件发生的地方
- 监听器：一个对象
- 注册监听：将事件、事件源、监听器绑定在一起。当事件源上发生某个事件后，执行监听器代码。

### ServletContextListener

监听ServletContext对象的创建和销毁

- void contextDestroyed(ServletContextEvent   sce)：ServletContext对象被销毁之前会调用该方法
- void contextInitialized(ServletContetxEvent  sce)：ServletContext对象创建后会调用该方法

#### 步骤

1. 定义一个类，实现ServletContextListener接口

2. 复写方法

3. 配置

   1. web.xml配置

      ```xml
      <listener>
          <listener-class>cn.itcast.web.listener.ContextLoaderListener</listener-class>
      </listener>
      <!--指定初始化参数-->
      <context-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>/WEB-INF/classes/applicationContext.xml</param-value>
      </context-param>
      ```

   2. 注解配置@WebListener

## JQuery基础

### 概念

一个JavaScript框架。简化JS开发。

*jQuery是一个快速、简洁的[JavaScript]框架，是继[Prototype]之后又一个优秀的JavaScript代码库（框架）于2006年1月由[John Resig]发布。jQuery设计的宗旨是“write Less，Do More”，即倡导写更少的代码，做更多的事情。它封装JavaScript常用的功能代码，提供一种简便的JavaScript[设计模式]，优化[HTML]文档操作、事件处理、动画设计和[Ajax]交互。*

JavaScript框架：本质上就是一些js文件，封装了js的原生代码而已

### 快速入门

1. 下载JQuery

   目前jQuery有三个大版本：
   	*1*.x：兼容ie678,使用最为广泛的，官方只做BUG维护，*
   		 *功能不再新增。因此一般项目来说，使用1.x版本就可以了，*
   		 *最终版本：1.12.4 (2016年5月20日)*
   	*2.x：不兼容ie678，很少有人使用，官方只做BUG维护，*
   		 *功能不再新增。如果不考虑兼容低版本的浏览器可以使用2.x，*
   		 *最终版本：2.2.4 (2016年5月20日)*
   	*3.x：不兼容ie678，只支持最新的浏览器。除非特殊要求，*
   		 *一般不会使用3.x版本的，很多老的jQuery插件不支持这个版本。*
   		 目前该版本是官方主要更新维护的版本。最新版本：3.2.1（2017年3月20日）*

   jquery-xxx.js  与  jquery-xxx.min.js区别：

   - jquery-xxx.js：开发版本。给程序员看的，有良好的缩进和注释
   - jquery-xxx.min.js：生产版本。没有缩进，体积小一些。程序加载更快。

2. 导入JQuery的js文件：jquery-3.3.1.min.js

3. 使用

### JQuery对象与JS对象的区别与转换

```
1.JQuery对象在操作时，更加方便
2.JQuery对象和js对象方法不相通
3.两者相互转换
    //jq-->js:jq对象[索引]  或者   jq对象.get(索引)
    //js-->jq:$(js对象)
```

### 选择器

筛选具有相似特征的元素（标签）

#### 基本语法学习

1. 事件绑定

   ```javascript
   $("#b1").click(function () {
       alert("abc");
   })
   ```

2. 入口函数

   ```javascript
   $(function () {
   	alert("abc");
   })
   ```

3. 样式控制

   ```javascript
   $(function () {
       // $("#div1").css("background-color","red");
       $("#div1").css("backgroundColor","pink")
   })
   ```

#### 分类

##### 基本选择器

1. 标签选择器（元素选择器）

   语法：$("html标签名")    获得所有匹配标签名称的元素

2. id选择器

   语法：$("#id的属性值")    获得与指定id的属性值匹配的元素

3. 类选择器

   语法：$(".class的属性值")     获得与指定的class属性值匹配的元素

4. 并集选择器

   语法：$("选择器1，选择器2......")      获得多个选择器选中的所有元素

##### 层级选择器

1. 后代选择器

   语法：$("A  B")    选择A元素内部的所有B元素（包括儿子B，孙子B）

2. 子选择器

   语法：$("A > B")   选择A元素内部的所有B子元素（只有儿子B）

##### 属性选择器

1. 属性名称选择器

   语法：$("A[属性名]")     包含指定属性的选择器

2. 属性选择器

   语法：$("A[属性名='值']")     包含指定属性等于指定值的选择器

3. 复合属性选择器

   语法：$("A[属性名='值'] [] ...")     包含多个属性条件的选择器

##### 过滤选择器

1. 首元素选择器

   语法：:first    获得选择的元素中的第一个元素

2. 尾元素选择器

   语法：:last    获得选择的元素中的最后一个元素

3. 非元素选择器

   语法：:not(selector)    不包括指定内容的元素

4. 偶数选择器

   语法：:even  偶数，从0开始计数

5. 奇数选择器

   语法：:odd   奇数，从0开始计数

6. 等于索引选择器

   语法：:eq(index)   指定索引元素

7. 大于索引选择器

   语法：:gt(index)     大于指定索引元素

8. 小于索引选择器

   语法：:lt(index)      小于指定索引元素

9. 标题选择器

   语法：:header   获得标题（h1~h6）元素，固定写法

##### 表单过滤选择器

1. 可用元素选择器

   语法：:enabled  获得可用元素

2. 不可用元素选择器

   语法：:disabled  获得不可用元素

3. 选中选择器

   语法：:checked    获得单选/复选框选中的元素

4. 选中选择器

   语法：:selected    获得下拉框选中的元素

### DOM操作

#### 内容操作

1. html()：获取/设置元素的**标签体内容**
2. text()：获取/设置元素的**标签体纯文本内容**
3. val()：获取/设置元素的**value属性值**

#### 属性操作

1. 通用属性操作

   1. attr()：获取/设置元素的属性值
   2. removeAttr()：删除属性
   3. prop()：获取/设置元素的属性值
   4. removeProp()：删除属性

   attr  和  prop区别？

   1. 如果操作的是元素的固有属性，建议使用prop
   2. 如果操作的是元素自定义的属性，建议使用attr

2. 对class属性操作

   1. addClass()：添加class属性值
   2. removeClass()：删除class属性值
   3. toggleClass()：切换class属性值
      - toggleClass("one")：如果元素对象上存在class="one"，则将属性值one删除。如果元素对象上不存在class="one"，则添加。
   4. css():
      - 只写一个参数，可以获取到该属性对应的属性值
      - 写两个参数，可以设置该属性的属性值

#### CRUD操作

1. append()：父元素将子元素追加到末尾
   - 对象1.append(对象2)：将对象2添加到对象1元素内部，并且在末尾
2. prepend()：父元素将子元素追加到开头
   - 对象1.prepend(对象2)：将对象2添加到对象1元素内部，并且在开头
3. appendTo()：
   - 对象1.appendTo(对象2)：将对象1添加到对象2元素内部，并且在末尾
4. prependTo():
   - 对象1.prependTo(对象2)：将对象1添加到对象2元素内部，并且在开头
5. after():添加元素到元素后边
   - 对象1.after(对象2)：将对象2添加到对象1后边。对象1和对象2是兄弟关系
6. before():添加元素到元素前边
   - 对象1.before(对象2)：将对象2添加到对象1前边。对象1和对象2是兄弟关系
7. insertAfter():
   - 对象1.insertAfter(对象2)：将对象1添加到对象2后边。对象1和对象2是兄弟关系
8. insertBefore():
   - 对象1.insertBefore(对象2)：将对象1添加到对象2前边。对象1和对象2是兄弟关系
9. remove()：移除元素
   - 对象.removw()：将对象删除掉
10. empty()：清空元素的所有后代元素
    - 对象.empty()：将对象的后代元素全部清空，但是保留当前对象以及其属性节点

## JQuery高级

### 动画

三种方式显示和隐藏元素

1. 默认显示和隐藏方式
   1. show([speed],[easing],[fn])
      - speed:动画的速度。三个预定义的值（"slow","normal","fast"）或者表示动画执行的毫秒值
      - easing:用来指定切换效果，默认是"swing"，可用参数"linear"
        - swing：动画执行时，效果是先慢，中间快，最后又慢
        - linear：动画执行时，速度是匀速的
      - fn:在动画完成时执行的函数，每个元素执行一次
   2. hide([speed],[easing],[fn])
   3. toggle([speed],[easing],[fn])
2. 滑动显示和隐藏方式
   1. slideDown([speed],[easing],[fn])
   2. slideUp([speed],[easing],[fn])
   3. slideToggle([speed],[easing],[fn])
3. 淡入淡出显示和隐藏方式
   1. fadeIn([speed],[easing],[fn])
   2. fadeOut([speed],[easing],[fn])
   3. fadeToggle([speed],[easing],[fn])

### 遍历

1. js的遍历方式
   - for(初始化值；循环结束条件；步长)
2. jq遍历方式
   1. jq对象.each(callback)
   2. $.each(object,[callback])
   3. for..of：

### 事件绑定

1. jquery标准的绑定方式

   - jq对象.事件方法(回调函数)
   - 注：如果调用事件方法，不传递回调函数，则会触发浏览器默认行为
   - 表单对象.submit()；//让表单提交

2. on绑定事件/off解除绑定

   - jq对象.on("事件名称"，回调函数)；
   - jq对象.off("事件名称")；
   - 如果off方法不传递任何参数，则将jq对象对应的组件上绑定的所有事件全部解绑

3. 事件切换：toggle

   - jq对象.toggle(fn1,fn2...)

   - 当单击jq对象对应组件后，会执行fn1，第二次点击会执行fn2...

   - 注：1.9版本 .toggle()方法删除,jQuery Migrate(迁移)插件可以恢复此功能

     ```javascript
     <script src="../js/jquery-migrate-1.0.0.js" type="text/javascript" charset="utf-8"></script>
     ```

### 案例

### 插件

增强JQuery的功能

#### 实现方式

1. $.fn.extend(object)
   - 增强通过Jquery获取的对象的功能     $("#id")
2. $.extend(object)
   - 增强JQuery对象自身的功能       $/jQuery

## AJAX

### 概念

ASynchronous   JavaScript   And   Xml      异步的   JavaScript  和   XML

**异步和同步：**客户端和服务器端相互通信的基础上

同步：客户端必须等待服务器端的响应。在等待期间，客户端不能做其他操作

异步：客户端不需要等待服务器端的响应。在服务器处理请求的过程中，服务器可以做其他操作

*在不需要刷新页面的情况下，就可以产生局部刷新的效果*

*Ajax 在浏览器与 Web 服务器之间使用异步数据传输（HTTP 请求），这样就可使网页从服务器请求少量的信息，而不是整个页面。*
**提升用户体验**

### 实现方式

1. 原生的JS实现方式(了解)

2. JQuery实现方式

   1. $.ajax()：$.ajax({键值对});

      ```javascript
      //使用$.ajax()发送异步请求
      $.ajax({
          url:"ajaxServlet",//请求路径
          type:"POST",//请求方式
          // data:"username=jack&age=23",//请求参数
          data:{"username":"jack","age":23},
          success:function (data) {
              //响应成功后的回调函数
              alert(data);
          },
          error:function () {
              //如果请求响应出现错误，会执行的回调函数
              alert("出错啦！")
          },
          dataType:"text"//设置接收到的响应数据格式
      
      });
      ```

   2. $.get()：发送get请求

      语法：$.get(url,[data],[callback],[type])

      参数：url：请求路径

      ​			data：请求参数

      ​			callback：回调函数

      ​			type：响应结果的类型

   3. $.post()：发送post请求

## JSON

### 概念

JavaScript     Object     Notation      JavaScript对象表示法

```javascript
var p = {"name":"张三","age":23,"gender":"男"};
```

JSON是存储和交换文本信息的语法

进行数据的传输，JSON比XML更小、更快、更易于解析

## 语法

### 基本规则

1. json数据是由键值对构成的
   1. 键用引号（单双都行）引起来，也可以不使用引号
   2. 值可以是：数字、字符串（双引号引起来）、逻辑值、数组（在方括号中）、json对象（在花括号中）、null
2. 多个键值对由逗号分隔
3. 使用{}定义json格式
4. []保存数组

### 获取数据

1. json对象.键名
2. json对象["键名"]
3. 数组对象[索引]

### JSON数据和Java对象的相互转换

**JSON解析器：**

常见的解析器：Jsonlib，Gson，fastjson，jackson

#### JSON-->Java对象

##### 使用步骤

1. 导入jackson的相关jar包
2. 创建Jackson核心对象   ObjectMapper
3. 调用ObjectMapper的相关方法进行转换
   1. readValue(json数据，Class类型)：
   2. 

#### Java对象-->JSON

##### 使用步骤

1. 导入jackson的相关jar包
2. 创建Jackson核心对象   ObjectMapper
3. 调用ObjectMapper的相关方法进行转换

##### 注解

1. @JsonIgnore：排除属性。在成员变量上方或者该成员变量的get方法上方加注解
2. @JsonFormat(pattren = "yyyy-MM-dd")：属性值格式化。

##### 复杂的java对象转换

List：数组

Map：和对象的格式一致

### 案例：校验用户名是否存在

1. 服务器响应的数据在客户端使用时，要想当作json数据格式使用

   1. $.get(type)：将最后一个参数type指定为"json"               在客户端html页面指定

   2. ```
      //在服务器端Servlet中设置MIME类型   设置响应的数据格式为json
      response.setContentType("application/json;charset=utf-8");
      ```

## redis

### 概念

redis是一款高性能的NOSQL系列的非关系型数据库

![img](https://github.com/ValoraY/NotePictures/blob/main/23.png)

一般会将数据存储在关系型数据库中，在nosql数据库中**备份存储**关系型数据库的数据

### 下载安装

### 命令操作

#### redis的数据结构

redis存储的是：key,value格式的数据，其中key都是字符串，value有5种不同的数据结构

- value的数据结构：
  1. 字符串类型  string
  2. 哈希类型   hash：map格式
  3. 列表类型   list：linkedlist（允许重复）
  4. 集合类型    set（不允许重复）
  5. 有序集合类型     sortedset
- 字符串类型  string
  1. 存储：set   key   value
  2. 获取：get   key
  3. 删除：del   key
- 哈希类型   hash：map格式
  1. 存储：hset   key    field    value
  2. 获取：hget   key    field      获取指定的field对应的值
     - hgetall   key      获取所有的field和value
  3. 删除：hdel   key   field
- 列表类型   list：linkedlist（允许重复）
  1. 添加：
     1. lpush key value：将元素加入列表左边
     2. rpush key value：将元素加入列表右边
  2. 获取：
     1. lrange  key  start  end：范围获取
  3. 删除
     1. lpop  key：删除列表最左边的元素，并将元素返回
     2. rpop  key：删除列表最右边的元素，并将元素返回
- 集合类型    set（不允许重复）
  1. 存储：sadd  key  value
  2. 获取：smembers  key：获取setu集合中的所有元素
  3. 删除：srem  key  value：删除set集合中的某个元素
- 有序集合类型     sortedset（不允许重复元素，且元素有序）
  1. 存储：zadd  key  score  value：
  2. 获取：zrange   key   start  end：
  3. 删除：zrem   key   value：

#### 通用命令

1. keys  *：查询所有的键
2. type  key：获取键对应的value的类型
3. del   key：删除指定的key和value

### 持久化操作

1. redis是一个内存数据库，当redis服务器重启或者电脑重启，数据会丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中。

2. redis持久化机制：

   1. RDB：默认方式，不需要进行配置，默认使用这种机制。

      - 在一定的间隔时间中，检测key的变换情况，然后持久化数据

        1. 编辑redis.windows.conf文件

           - save 900 1             在15min之后，只要有大于等于1个key发生改变，就持久化一次
             save 300 10           在5min之后，只要有大于等于10个key发生改变，就持久化一次
             save 60 10000        在1min之后，只要有大于等于10000个key发生改变，就持久化一次

        2. 重新启动redis服务器，并指定配置文件名称

           ```
           //在cmd窗口重启redis服务器
           D:\develop\redis-2.8.9>redis-server.exe redis.windows.conf
           ```

   2. AOF：日志记录的方式，可以记录每一条命令的操作。可以每一次命令操作后来持久化数据。

      1. 编辑redis.windows.conf文件

         - appendonly no（关闭AOF）   -->  appendonly yes（开启AOF持久化机制）

         - ```
           # appendfsync always   每一次操作都进行持久化
           appendfsync everysec   每隔一秒操作一次
           # appendfsync no       不进行持久化
           ```

### Jedis

#### Jedis

一款java操作redis数据库的工具。

#### 使用步骤

1. 下载jedis的jar包       commons-pool2-2.3.jar        jedis-2.7.0.jar

2. 使用

   ```java
   //1.获取连接
   Jedis jedis = new Jedis("localhost",6379);
   //2.操作
   jedis.set("username","zhangsan");
   //3.关闭连接
   jedis.close();
   ```

**Jedis操作各种redis中的数据结构**

#### Jedis连接池

**JedisPool**

##### 使用

1. 创建JedisPool连接池对象
2. 调用   getResource()方法   获取Jedis连接

#### 案例

案例需求：
	1. 提供index.html页面，页面中有一个省份 下拉列表
	2. 当 页面加载完成后 发送ajax请求，加载所有省份
	![img](https://github.com/ValoraY/NotePictures/blob/main/24.png)

1. 准备工作——环境搭建

   1. 创建数据库，并插入数据
   2. 在java项目中导入相关jar包
   3. 导入js文件
   4. 创建项目的各种包domain,service,web.servlet,util......注意分层(service和servlet的impl子包)
   5. 在util包中导入已经写好的JDBDUtils模板（其实就是利用Druid连接池写好的一个JDBC工具类  JDBCUtils.java）

2. 实现_查询数据库——后端

   1. 在domain包**创建**对应于数据库province表的**实体类Province**

   2. 在dao包**创建接口ProvinceDao**，提供查询所有省份的方法find All()方法声明

   3. 在dao.impl包**创建实现类ProvinceDaoImpl**，重写findAll()方法——**直接操作数据库**

      ```java
      public class ProvinceDaoImpl implements ProvinceDao {
          //1.声明成员变量  jdbctemplate
          private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
      
      
          @Override
          public List<Province> findAll() {
              //1.定义sql
              String sql = "select * from province";
              //2.执行sql
              //template.query(sql,BeanPropertyRowMapper<实体类名>(实体类名.class))的返回值是一个List<实体类>
              List<Province> list = template.query(sql, 
                                  new BeanPropertyRowMapper<Province>(Province.class));
              return list;
          }
      }
      ```

      

   4. 在service包**创建接口ProvinceService**，提供查询所有省份的方法findAll()方法声明

   5. 在service.impl包**创建实现类ProvinceServiceImpl**，重写findAll()方法——**创建dao对象，调用dao的findAll()方法**

      ```java
      public class ProvinceServiceImpl implements ProvinceService {
          //声明dao
          private ProvinceDao dao = new ProvinceDaoImpl();
          
          @Override
          public List<Province> findAll() {
              return dao.findAll();
          }
      }
      ```

   6. 在web.servlet包下**创建**名为FindServlet的**Servlet**——完成1.调用service中的findAll()方法查询  2.将service返回的结果序列化为json    3.响应数据（响应前注意设置响应编码）

      ```java
          @Override
          protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
              //1.调用service查询
              ProvinceService service = new ProvinceServiceImpl();
              List<Province> list = service.findAll();
              //2.序列化list为json——这是导入了jackson的包才能直接创建ObjectMapper对象
              ObjectMapper mapper = new ObjectMapper();
              String json = mapper.writeValueAsString(list);
              //3.响应结果
              response.setContentType("application/json;charset=utf-8");
              response.getWriter().write(json);
          }
      ```

3. 前端

   1. 在web目录下创建index.html页面，创建<select>标签

   2. 导入js包下的jquery.js文件

      ```javascript
      <script src="js/jquery-3.3.1.min.js"></script>
      ```

   3. 在<script>标签中编写代码，实现——页面一加载完成，就发送ajax请求，加载所有省份数据，再获取<select>元素对象，将从数据库获取到的省份追加到<option>中

      ```javascript
      <script>
      $(function () {
          //发送ajax请求，加载所有省份数据
          $.get("provinceServlet",{},function (data) {
              //[{"id":1,"name":"北京"},{"id":2,"name":"上海"},{"id":3,"name":"广州"},{"id":4,"name":"陕西"}]
              //1.获取select
              var province = $("#province");
              //2.遍历json数组
              $(data).each(function () {
                  //3.创建option标签
                  var option = "<option name='"+this.id+"'>"+this.name+"</option>";
                  //4.调用select的append方法追加option
                  province.append(option);
              })
      
      
          })
      })
      </script>
      ```

#### 案例优化——redis缓存

![img](https://github.com/ValoraY/NotePictures/blob/main/25.png)

**修改ProvinceServiceImpl类**

```java
    /**
     * 使用redis缓存
     * @return
     */
    @Override
    public String findAllJson() {
        //1.先从redis中查询数据
        //1.1 获取redis的客户端连接
        Jedis jedis = JedisPoolUtils.getJedis();
        String province_json = jedis.get("province");
        //2.判断 province_json 数据是否为null
        if(province_json == null || province_json.length() == 0){
            //redis缓存中没有数据
            System.out.println("redis中没数据，查询数据库...");
            //2.1从数据库中查询
            List<Province> ps = dao.findAll();
            //2.2将list序列化为json
            ObjectMapper mapper = new ObjectMapper();

            try {
                province_json = mapper.writeValueAsString(ps);
            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
            //2.3 将json数据存入redis中
            jedis.set("province",province_json);
            //归还连接
            jedis.close();
        }else{
            System.out.println("redis中有数据，查询缓存...");
        }
        return province_json;
    }
```

**注意：**

1. 使用redis缓存一些不经常发生变化的数据
   1. 数据库中的数据一旦发生改变，则需要更新缓存
      1. 数据库的表执行 增删改的相关操作，需要将redis缓存数据清空，再次存入
      2. 在service对应的 增删改方法中，将redis数据删除

## Maven

### 概念

Maven是一个项目管理工具，包含

1. 一个项目对象模型（POM：Project  Object  Model）
2. 一组标准集合
3. 一个项目生命周期
4. 一个依赖管理系统
5. 插件

### Maven仓库

#### 分类

1. 本地仓库：当前项目所在计算机的文件夹
2. 远程仓库
3. 中央仓库：全球唯一，Maven团队自己维护，拥有大部分仓库

### Maven目录结构

**项目的根目录：**

​		**--src：源码**

​				**--main：主工程代码**

​							**--java：主工程代码**

​							**--resources：主工程代码需要使用的配置文件**

​							**--webapp：web项目的资源目录(jsp/html/WEB-INF......)**

​				**--test：测试代码**

​							**--java：测试代码**

​							**--resources：测试代码需要使用的配置文件**

​		**--pom.xml：项目的核心配置文件**

### Maven常用命令

compile

clean

package

install

### Maven生命周期

有三套生命周期

- Clean   LifeCycle

- Default   LifeCycle

- Site   LifeCycle

**在同一套生命周期中，执行后边的操作，会自动执行前面的所有操作**

**Default   LifeCycle：编译------>测试------>打包------>安装**

Clean   LifeCycle：清理

Site   LifeCycle：站点发布

### IDEA配置Maven环境

![image-20210812162656617](https://github.com/ValoraY/NotePictures/blob/main/26.png)

### Maven坐标

#### 概念

被Maven管理的**资源**（jar包、插件）的唯一标识

#### 构成

groupId：组织名称   cn.itcast

atifactId：模块名称

version：版本号

### IDEA创建Java项目

创建普通的Java Project  -->  在项目名上右键  -->  NEW  Module  -->  选择Maven 、相应的SDK -->  NEXT -->按要求填写即可  -->Finish

### 使用依赖导入jar包

在pom.xml文件中<dependencies>标签中写<dependency>,每一个<dependency>都是一个坐标

### 设置Maven编译插件

alt+insert  -->  Plugin  template -->选择本地仓库里面你需要的插件即可自动补全

### IDEA创建WEB项目

**不使用骨架**

1. 创建普通的Java Project  -->  在项目名上右键  -->  NEW  Module  -->  选择Maven 、相应的SDK -->  NEXT -->按要求填写即可  -->Finish
2. 在pom.xml文件中配置<packaging>war</packaging>
3. 补全目录结构
   1. **在main目录下-->新建WEB-INF目录-->在WEB-INF目录下-->新建web.xml文件**（在Settings->Editor->File and Code Template -> Other -> Web ->Deployment descriptors ->任选一个xml文件->复制并粘贴到web.xml文件中）

### 依赖范围

![img](https://github.com/ValoraY/NotePictures/blob/main/27.png)

## 《黑马旅游网》综合案例

### 一、项目导入

**问：**解决导入初始项目后  dependency  not  found  问题

**答：**在IDEA中配置Maven环境，修改Maven查找资源的路径

![image-20210813095647848](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20210813095647848.png)

**问：**解决依赖冲突  ommitd with confilct 2.3.3

**答：**下载Maven Helper插件   https://blog.csdn.net/dhfzhishi/article/details/81952760

### 二、项目启动

![image-20210813102433332](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20210813102433332.png)

![image-20210813102455280](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20210813102455280.png)

### 三、技术选型

#### web层

1. Servlet：前端控制器
2. Html：视图
   - 视图  用  JSP  还是  HTML？
   - HTML：供普通用户访问、访问速度更快、前后端分离
   - JSP：供内部人员使用    如：办公自动化系统、财务系统
3. Filter：过滤器
4. BeanUtils：完成数据封装
   - 将前端页面传递给Servlet的数据进行封装
5. Jackson：json序列化工具
   - 服务器向客户端响应的数据需要序列化为json返回

#### service层

1. Javamail：Java  发送邮件的工具
2. redis：nosql内存数据库
3. Jedis：Java的redis客户端

#### dao层

1. Mysql：数据库
2. Druid：数据库连接池
3. JdbcTemplate：jdbc工具

### 四、创建数据库

--创建数据库

CREATE DATABASE travel;

--使用数据库

USE travel;

--创建表

复制提供好的sql

### 五、功能实现

#### 注册

##### 功能分析

![img](file:///C:\Users\鲸鱼\Documents\Tencent Files\1432843916\Image\C2C\7C0468CA35208F99BEA2FE05E551A9F2.png)

##### 代码实现

###### 前台效果

1. 表单校验           在register.html页面

   ```javascript
   <!--导入jquery-->
   		<script src="js/jquery-3.3.1.js"></script>
   
   		<script>
   			/*
   				表单校验
   					1.用户名：单词字符，长度8-20位
   					2.密码：单词字符，长度8-20位
   					3.email：邮箱格式
   					4.姓名：非空
   					5.手机号：手机号格式
   					6.出生日期：非空
   					7.验证码：非空
   			 */
   			//校验用户名:用户名：单词字符，长度8-20位
   			function checkUsername() {
   				//1.获取用户名的值
   				var username = $("#username").val();
   				//2.定义正则
   				var reg_username = /^\w{8,20}$/;
   				//3.判断，给出提示信息
   				var flag = reg_username.test(username);
   				if (flag){
   					//flag位true，用户名合法，校验通过——获取用户名文本输入框，将其边框恢复为最初颜色
   					$("#username").css("border","");
   
   				}else{
   					//用户名非法——获取用户名文本输入框，将其边框设置为红色
   					$("#username").css("border","1px solid red");
   				}
   				return flag;
   			}
   
   			//校验密码
   			function checkPassword() {
   				//1.获取密码的值
   				var password = $("#password").val();
   				//2.定义正则
   				var reg_password = /^\w{8,20}$/;
   				//3.判断，给出提示信息
   				var flag = reg_password.test(password);
   				if (flag){
   					//flag位true，用户名合法，校验通过——获取用户名文本输入框，将其边框恢复为最初颜色
   					$("#password").css("border","");
   
   				}else{
   					//用户名非法——获取用户名文本输入框，将其边框设置为红色
   					$("#password").css("border","1px solid red");
   				}
   				return flag;
   			}
   
   			//校验邮箱
   			function checkEmail() {
   				//1.获取邮箱
   				var email = $("#email").val();
   				//2.定义正则
   				var reg_email = /^\w+@\w+\.\w+$/;
   				//3.判断
   				var flag = reg_email.test(email);
   				if (flag){
   					$("#email").css("border","");
   				}else{
   					$("#email").css("border","1px solid red");
   				}
   				return flag;
   			}
   
   
   			$(function () {
   				//当表单提交时，调用所有的校验方法
   				$("#registerForm").submit(function () {
   					//如果这个匿名function函数返回true或者没有返回值，就会提交表单；返回false，就不会提交表单
   					return checkUsername() && checkPassword() && checkEmail();
   				})
   
   
   				//当某一个组件失去焦点时，调用对应的校验方法
   				$("#username").blur(checkUsername);//参数是一个function对象，只传函数名即可，不用加()
   				$("#password").blur(checkPassword);
   				$("#email").blur(checkEmail);
   
   
   			})
   
   
   		</script>
   ```

2. 异步ajax提交表单

   - 在此使用**异步提交表单**是为了**获取服务器响应的数据**，因为我们前台使用的是html作为视图层，不能够直接从servlet相关的域对象（request域、session域）来获取值，只能通过ajax获取响应数据。

   - **表单提交都是一些同步的方式——因此这里我们不能提交表单，只能通过发送ajax请求，来提交表单的数据**

   - **注意：提交表单≠提交表单的数据**

     ```javascript
     $(function () {
         //当表单提交时，调用所有的校验方法
         $("#registerForm").submit(function () {
             //1.发送数据到服务器
             if (checkUsername() && checkPassword() && checkEmail()){
                 //校验通过，发送ajax请求，提交表单的数据  username=zhangsan&password=123
                 //$(this).serialize():表单对象调用serialize方法可以将表单数据字符串化——username=zhangsan&password=123
                 $.post("registerUserServlet",$(this).serialize(),function (data) {
                     //处理服务器响应的数据data
                 })
             }
             //2.跳转页面
             return false;//就不会去跳转页面，表单就不能提交了
             //如果这个匿名function函数返回true或者没有返回值，就会提交表单；返回false，就不会提交表单
     
     })
     ```

###### 后台代码

**编写CharacterFilter过滤器**——解决html通过**post方式请求数据时的中文乱码问题**

```java
@WebFilter("/*")
public class CharacterFilter implements Filter {

    @Override
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        //将父接口转换为子接口
        HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) resp;
        //获取请求方法
        String method = request.getMethod();
        //解决post请求中文数据乱码问题
        if(method.equalsIgnoreCase("post")){
            request.setCharacterEncoding("utf-8");
        }
        //处理响应乱码
        response.setContentType("text/html;charset=utf-8");
        //放行
        chain.doFilter(request, response);
    }

    public void init(FilterConfig config) throws ServletException {
    }

    public void destroy() {
    }
    
}
```

**编写RegisterUserServlet**

```java
 @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //首先校验验证码
        //1.获取验证码的文本输入框——check 代表 用户输入的验证码
        String check = request.getParameter("check");
        //2.从session中获取验证码——程序（checkCodeServlet）随机生成的验证码
        HttpSession session = request.getSession();
        String checkcode_server = (String) session.getAttribute("CHECKCODE_SERVER");
        //防止验证码复用——获取到程序生成的验证码后，立刻将session域中的那个验证码删除
        session.removeAttribute("CHECKCODE_SERVER");
        //3.比较

        if(checkcode_server == null || !checkcode_server.equalsIgnoreCase(check)){
            //验证码错误，不做后面的操作
            //给出提示信息
            ResultInfo info = new ResultInfo();
            info.setFlag(false);
            info.setErrorMsg("验证码错误！");
            //将info对象序列化为json
            ObjectMapper mapper = new ObjectMapper();
            String json = mapper.writeValueAsString(info);

            //将json数据写回客户端
            //先设置content-type
            response.setContentType("application/json;charset=utf-8");
            response.getWriter().write(json);

            return;
        }


        //1.获取数据
        Map<String, String[]> map = request.getParameterMap();
        //2.封装对象
        User user = new User();
        //将map集合中对应的数据封装到user对象对应的属性当中去
        try {
            BeanUtils.populate(user,map);
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
        //3.调用service完成注册
        UserService service = new UserServiceImpl();
        boolean flag = service.regist(user);
        ResultInfo info = new ResultInfo();
        //4.响应结果
        if(flag){
            //注册成功
            info.setFlag(true);
        }else{
            //注册失败
            info.setFlag(false);
            info.setErrorMsg("注册失败！");
        }
        //将info对象序列化为json
        ObjectMapper mapper = new ObjectMapper();
        String json = mapper.writeValueAsString(info);

        //将json数据写回客户端
        //先设置content-type
        response.setContentType("application/json;charset=utf-8");
        response.getWriter().write(json);

    }
```

**编写UserServive以及UserServiceImpl**

```java
public class UserServiceImpl implements UserService {

    //声明成员变量  UserDao
    private UserDao dao = new UserDaoImpl();

    /**
     * 注册用户
     * @param user
     * @return
     */
    @Override
    public Boolean regist(User user) {
        //1.根据用户名查询用户对象
        User u = dao.findByUsername(user.getUsername());
        //2.保存用户信息
        //判断u是否为null
        if(u!=null){
            //用户名存在，注册失败
            return false;
        }
        dao.save(user);
        return true;
    }
}
```

**编写UserDao以及UserDaoImpl**

```java
public class UserDaoImpl implements UserDao {

    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());


    @Override
    public User findByUsername(String username) {
        User user = null;
        try {
            String sql = "select * from tab_user where username = ?";
            //用户名查询只能查询出一个对象——queryForObject
            //参数：sql语句，你要封装的对象类型，参数
            //问题：在spring提供的JdbcTemplate里面，如果我们提供的用户名在数据库中没有查询到响应的用户数据，这块封装的user对象并不是null，而是直接爆错
            //即：如果BeanPropertyRowMapper封装失败，不会返回null，而是直接爆异常——所以try-catch
            user = template.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), username);
            
        } catch (Exception e) {
            
        }
        return user;
    }

    @Override
    public void save(User user) {
        //1.定义sql
        String sql = "insert into tab_user(username,password,name,birthday,sex,telephone,email) values(?,?,?,?,?,?,?)";
        //2.执行sql
   
```

**问题解决:**

> Maven： java.lang.NullPointerException: inStream parameter is null
>
> 错误原因：无法读取properties配置文件（在maven项目中，所有的配置文件都存放在-src-main-resources目录下）
>
> 解决办法：在JDBCUtils.java工具类中，读取配置文件的默认写法是  
>
> ```java
> InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("/druid.properties");
> ```
>
> 应修改为：
>
> ###### InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");

###### 邮件激活

**目的：**保证用户填写的邮箱是正确的，将来可以推广一些宣传信息到用户邮箱中

**发送邮件**：**在UserServiceImpl中完成，当保存完用户信息后，可以发送邮件**

**用户点击邮件激活**

![img](file:///C:\Users\鲸鱼\Documents\Tencent Files\1432843916\Image\C2C\17ED452FE8FFDB9EAED5E5580C583EBD.png)

**用户激活其实就是修改用户表中的 status 为Y**

**一、发送邮件、跳转链接到activeUserServlet**

```java
public class UserServiceImpl implements UserService {

    //声明成员变量  UserDao
    private UserDao dao = new UserDaoImpl();

    /**
     * 注册用户
     * @param user
     * @return
     */
    @Override
    public Boolean regist(User user) {
        //1.根据用户名查询用户对象
        User u = dao.findByUsername(user.getUsername());
        //判断u是否为null
        if(u!=null){
            //用户名存在，注册失败
            return false;
        }
        //2.保存用户信息
        //2.1设置激活码（唯一）
        user.setCode(UuidUtil.getUuid());
        //2.2设置激活状态
        user.setStatus("N");
        dao.save(user);

        //3.发送激活邮件
        String content = "<a href='http://localhost/travel/activeUserServlet?code="+user.getCode()+"'>点击激活[黑马旅游网]</a>";
        MailUtils.sendMail(user.getEmail(),content,"激活邮件");
        return true;
    }
}
```

**注意修改dao中的save方法——加上存储status、code的代码**

![image-20210815161812394](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20210815161812394.png)

**二、编写activeUserServlet**

```java
@WebServlet("/activeUserServlet")
public class ActiveUserServlet extends HttpServlet {
	public void doGet(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
		//1.获取激活码
		String code = request.getParameter("code");
		if(code!=null){
			//2.调用service完成激活
			UserService service = new UserServiceImpl();
			boolean flag = service.active(code);
			
			//3.判断标记
			String msg = null;
			if(flag){
				//激活成功
				msg = "激活成功，请<a href='login.html'>登录</a>";
			}else{
				//激活失败
				msg = "激活失败，请联系管理员";
			}
			response.setContentType("text/html;charset=utf-8");
			response.getWriter().write(msg);
		}
	}
}
```

**三、编写UserServiceImpl，提供active方法**

```java
public class UserServiceImpl implements UserService {

    @Override
    public boolean active(String code) {
        //1.根据激活码查询用户对象
        User user = dao.findByCode(code);
        if(user!=null){
            //2.调用dao修改激活状态的方法
            dao.updateStatus(user);
            return true;
        }else{
            return false;

        }
    }

}
```

**四、编写UserDaoImpl，提供findByCode方法、updateStatus方法**

```java
public class UserDaoImpl implements UserDao {

    /**
     * 根据激活码查询用户对象
     * @param code
     * @return
     */
    @Override
    public User findByCode(String code) {
        User user = null;
        try {
            String sql = "select * form tab_user where code = ?";
            user = template.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), code);
        } catch (DataAccessException e) {
            e.printStackTrace();
        }
        return user;
    }

    /**
     * 修改指定用户激活状态
     * @param user
     */
    @Override
    public void updateStatus(User user) {
        String sql = "update tab_user set status = 'Y' where id = ?";
        template.update(sql,user.getUid());

    }
}
```

#### 登录

##### 功能分析

![img](file:///C:\Users\鲸鱼\Documents\Tencent Files\1432843916\Image\C2C\1CF2A870F8AFBA040CD469F68C1F2CB3.png)

##### 代码实现

###### 前台代码

**编写login.html的<script>**

```javascript
<script>
   $(function () {
      //1.给登录按钮绑定单击事件
      $("#btn_sub").click(function () {
         //2.发送ajax请求，提交表单数据
         $.post("loginServlet",$("#loginForm").serialize(),function (data) {
            //3.处理响应结果  {flag:true,errorMsg:""}
            if (data.flag){
               //登录成功
               location.href = "index.html";
            }else{
               //登录失败
               $("#errorMsg").html(data.errorMsg);
            }
            
         })
      })
   })
</script>
```

###### 后台代码

**一、编写LoginServlet**

```java
/**
 * 登录
 */
@WebServlet("/loginServlet")
public class LoginServlet extends HttpServlet {
	public void doGet(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
		//1.获取用户名和密码
		Map<String, String[]> map = request.getParameterMap();
		//2.封装对象
		User user = new User();
		try {
			BeanUtils.populate(user,map);
		} catch (IllegalAccessException e) {
			e.printStackTrace();
		} catch (InvocationTargetException e) {
			e.printStackTrace();
		}
		//3.调用service完成查询用户
		UserService service = new UserServiceImpl();
		User u = service.login(user);
		//4.判断用户对象是否为null
		ResultInfo info = new ResultInfo();
		if(u==null){
			//为null：用户名和密码 填错了
			info.setFlag(false);
			info.setErrorMsg("用户名或密码错误！");
		}else{
			//不为null：数据库中存在 这样的用户名和密码  的用户
			//判断用户是否激活
			if(!"Y".equals(u.getStatus())){
				//用户存在，但是没有激活
				info.setFlag(false);
				info.setErrorMsg("您尚未激活，请激活!");
			}else{
				//用户存在 且 已经激活   登录成功
				info.setFlag(true);
			}
		}
		
		//响应数据
		ObjectMapper mapper = new ObjectMapper();
		response.setContentType("application/json;charset=utf-8");
		mapper.writeValue(response.getOutputStream(),info);
		
	}
	public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request,response);
	}
}
```

**二、编写UserService**

```java
/**
* 登录方法
* @param user
* @return
*/
@Override
public User login(User user) {
	return dao.findByUsernameAndPassword(user.getUsername(),user.getPassword());
}
```

**三、编写UserDao**

```java
/**
     * 根据用户名和密码查询用户
     * @param username
     * @param password
     * @return
*/
@Override
public User findByUsernameAndPassword(String username, String password) {
    User user = null;
    try {
        String sql = "select * from tab_user where username = ? and password = ?";
        //用户名查询只能查询出一个对象——queryForObject
        //参数：sql语句，你要封装的对象类型，参数
        //问题：在spring提供的JdbcTemplate里面，如果我们提供的用户名在数据库中没有查询到响应的用户数据，这块封装的user对象并不是null，而是直接爆错
        //即：如果BeanPropertyRowMapper封装失败，不会返回null，而是直接爆异常
        user = template.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), username,password);

    } catch (Exception e) {

    }
    return user;
}
```

**index.html页面中用户姓名的提示信息功能**

一、index.html -->  header.html页面

```javascript
<script>
    $(function () {
        $.get("findUserServlet",{},function (data) {
            //{uid:1,name:'李四'}
            var msg = "欢迎回来，" + data.name;
            $("#span_username").html(msg);
        })
    })
</script>
```

二、编写findUserServlet

```java
/**
 * 查找用户的名字
 */
@WebServlet("/findUserServlet")
public class FindUserServlet extends HttpServlet {
	public void doGet(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
		//从session中获取登录用户
		Object user = request.getSession().getAttribute("user");
		//将user写回客户端
		ObjectMapper mapper = new ObjectMapper();
		response.setContentType("application/json;cha");
		mapper.writeValue(response.getOutputStream(),user);
	}
	public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request,response);
	}
}
```

#### 退出

什么叫做登录呢？session中有user对象

步骤：

1. 访问servlet，将session销毁
2. 跳转到登录页面

###### 代码实现

**一、给     <u>退出</u>    超链接  绑定    点击就访问ExitServlet   事件**

```javascript
<a href="javascript:location.href='exitServlet';">退出</a>
```

**二、编写ExitServlet**

```java
/**
 * 退出
 */
@WebServlet("/exitServlet")
public class ExitServlet extends HttpServlet {
	public void doGet(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
		//1.销毁session
		request.getSession().invalidate();
		//2.跳转到登录页面
		response.sendRedirect(request.getContextPath()+"/login.html");

	}
	public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request,response);
	}
}
```

#### 优化Servlet

减少Servlet的数量，现在是一个功能一个Servlet，将其优化为**一个模块一个Servlet**，<u>**相当于在数据库中，一张表对应一个Servlet，在这个Servlet中提供不同的方法，完成用户的请求**</u>。

![img](file:///C:\Users\鲸鱼\Documents\Tencent Files\1432843916\Image\C2C\BDFA461041D6E601941D42919EEAB006.png)

> 设置控制台输出的中文字符不乱码
>
> ![image-20210816104329615](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20210816104329615.png)

##### BaseServlet抽取

```java
/**
 * BaseServlet——不需要被访问到——不需要写注释
 */
public class BaseServlet extends HttpServlet {
   @Override
   protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      //System.out.println("baseServlet的service方法被执行了");
      //完成方法分发
      //1.获取请求路径
      String uri = req.getRequestURI();//    /travel/user/add
      System.out.println("请求uri："+uri);//       /travel/user/add
      //2.获取方法名称
      String methodName = uri.substring(uri.lastIndexOf("/") + 1);
      System.out.println("方法名称：" + methodName);
      //3.获取方法对象Method
      System.out.println(this);//this:谁调用我？我代表谁——UserServlet的对象
      try {
 //忽略访问权限修饰符获取方法
//Method method = this.getClass().getDeclaredMethod(methodName, HttpServletRequest.class, HttpServletResponse.class);
         Method method = this.getClass().getDeclaredMethod(methodName, HttpServletRequest.class, HttpServletResponse.class);
         //4.执行方法
         //暴力反射
//       method.setAccessible(true);
         method.invoke(this,req,resp);
      } catch (NoSuchMethodException e) {
         e.printStackTrace();
      } catch (IllegalAccessException e) {
         e.printStackTrace();
      } catch (InvocationTargetException e) {
         e.printStackTrace();
      }

      //4.执行方法
   }
}
```

```java
/**
 * 用户模块对应的Servlet
 */
@WebServlet("/user/*")
public class UserServlet extends BaseServlet {
	public void add(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
		System.out.println("userServlet的add方法...");
	}
	public void find(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("userServlet的find方法...");

	}
}
```

```java
/**
 * 种类模块对应的Servlet
 */
@WebServlet("/category/*")
public class CategoryServlet extends BaseServlet {
   public void add(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
      System.out.println("categoryServlet的add方法...");
   }
   public void find(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      System.out.println("categoryServlet的find方法...");

   }
}
```

### 分类数据展示

**效果：**

<img src="C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20210816115131262.png" alt="image-20210816115131262" style="zoom:67%;" />

**分析：**

![img](file:///C:\Users\鲸鱼\Documents\Tencent Files\1432843916\Image\C2C\CA3FF19EE8623D5BA1B3700D50B8A4F8.png)

### 旅游线路分页展示

#### 前台代码

在route_list.html页面

```javascript
<script>
    $(function () {
        var search = location.search;
        // alert(search);
        //切割字符串，拿到第二个值
        var cid = search.split("=")[1];
        load(cid);
    })

    function load(cid,currentPage) {
        //发送ajax请求，请求route/pageQuery，传递cid
        $.get("route/pageQuery",{cid:cid,currentPage:currentPage},function (pb) {
            /*    pb   {"totalCount": 513,
                         "totalPage": 103,
                         "currentPage": 23,
                         "pageSize": 5,
                         "list": [{},{},{}]
                         }
             */
            //解析PageBean数据，展示到页面上

            //1.分页工具条的数据展示
            //1.1展示总页码和总记录数
            $("#totalPage").html(pb.totalPage);
            $("#totalCount").html(pb.totalCount);
            /*
                <li><a href="">首页</a></li>
                <li class="threeword"><a href="#">上一页</a></li>
                <li><a href="#">1</a></li>
                <li><a href="#">2</a></li>
                <li><a href="#">3</a></li>
                <li><a href="#">4</a></li>
                <li><a href="#">5</a></li>
                <li><a href="#">6</a></li>
                <li><a href="#">7</a></li>
                <li><a href="#">8</a></li>
                <li><a href="#">9</a></li>
                <li><a href="#">10</a></li>
                <li class="threeword"><a href="javascript:;">下一页</a></li>
                <li class="threeword"><a href="javascript:;">末页</a></li>
             */
            var lis = "";

            var firstPage = '<li onclick="javascript:load('+cid+',1)"><a href="javascript:void(0)">首页</a></li>';
            //计算上一页页码
            var beforeNum = pb.currentPage - 1;
            if (beforeNum <= 0){
                beforeNum = 1;
            }
            var beforePage = '<li onclick="javascript:load('+cid+','+beforeNum+')" class="threeword"><a href="javascript:void(0)">上一页</a></li>';

            lis += firstPage;
            lis +=beforePage;
            //1.2展示分页页码
            var li;
            for (var i = 1; i <= pb.totalPage; i++) {
                //当前页码是否等于i
                if (pb.currentPage == i){
                    //创建一个页码的li
                    li = '<li class="curPage" onclick="javascript:load('+cid+','+i+')"><a href="javascript:void(0)">'+i+'</a></li>';
                }else{
                    li = '<li onclick="javascript:load('+cid+','+i+')"><a href="javascript:void(0)">'+i+'</a></li>';
                }
                //拼接字符串
                lis += li;
            }
            var nextPage = '<li class="threeword"><a href="javascript:;">下一页</a></li>';
            var lastPage = '<li class="threeword"><a href="javascript:;">末页</a></li>';

            lis += nextPage;
            lis +=lastPage;

            //将lis的内容设置到ul中
            $("#pageNum").html(lis);

            //2.列表数据展示
            /*
            <li>
                <div class="img"><img src="images/04-search_03.jpg" alt=""></div>
                <div class="text1">
                    <p>【减100元 含除夕/春节出发】广州增城三英温泉度假酒店/自由行套票</p>
                    <br/>
                    <p>1-2月出发，网付立享￥1099/2人起！爆款位置有限，抢完即止！</p>
                </div>
                <div class="price">
                    <p class="price_num">
                        <span>&yen;</span>
                        <span>299</span>
                        <span>起</span>
                    </p>
                    <p><a href="route_detail.html">查看详情</a></p>
                </div>
            </li>
             */
            var route_lis = "";

            for (var i = 0; i < pb.list.length; i++) {
                //获取{rid:1,rname:"xxx"}
                var route = pb.list[i];

                var route_li = '<li>\n' +
                    '                            <div class="img"><img src="'+route.rimage+'" style="width:299px;"></div>\n' +
                    '                            <div class="text1">\n' +
                    '                                <p>'+route.rname+'</p>\n' +
                    '                                <br/>\n' +
                    '                                <p>'+route.routeIntroduce+'</p>\n' +
                    '                            </div>\n' +
                    '                            <div class="price">\n' +
                    '                                <p class="price_num">\n' +
                    '                                    <span>&yen;</span>\n' +
                    '                                    <span>'+route.price+'</span>\n' +
                    '                                    <span>起</span>\n' +
                    '                                </p>\n' +
                    '                                <p><a href="route_detail.html">查看详情</a></p>\n' +
                    '                            </div>\n' +
                    '           </li>';

                route_lis += route_li;
            }

            $("#route").html(route_lis);

        })


    }
</script>
```

#### 展示分页页码

```javascript
/*
展示分页页码：
    1.一共展示10个页码，能够达到前5后4的效果
    2.如果前面不够5个，后面补齐，整体一共10个
    3.如果后边不足4个，前面补齐，整体一共10个
*/

//定义开始位置 begin，结束位置 end
var begin;//开始位置
var end;//结束位置

//1.显示10个页码
if (pb.totalPage < 10){
    //总结果页码数不够10页
    begin = 1;
    end = pb.totalPage;
}else{
    //总页码数超过10页
    begin = pb.currentPage - 5;
    end = pb.currentPage + 4;

    //2.如果前面不够5个，后面补齐，整体一共10个
    if (begin < 1){
        begin = 1;
        end = begin + 9;
    }
    //3.如果后边不足4个，前面补齐，整体一共10个
    if (end > pb.totalPage){
     end = pb.totalPage;
     begin = end - 9;
    }

}
```

### 旅游线路名称查询

#### 前台代码

##### 查询参数传递

在header.html中

```javascript
//给搜索按钮绑定单击事件，获取搜索输入框的内容
$("#search-button").click(function () {
    //线路名称
    var rname = $("#search_input").val();
    var cid = getParameter("cid");
    //alert(cid);
    //跳转路径   http://localhost/travel/route_list.html?cid=5,拼接上rname=xxx
    location.href = "http://localhost/travel/route_list.html?cid="+cid+"&rname="+rname;
})
```

在route_list.html中

```javascript
<script src="js/getParameter.js"></script>
<script>
    $(function () {

        //获取cid的参数值
        var cid = getParameter("cid");
        //获取rname的参数值
        var rname = getParameter("rname");
        if (rname){
            //如果rname不为null或者“”
            //url解码
            rname = window.decodeURIComponent(rname);
        }
        load(cid);
    })
```

在route.html中继续修改

```javascript
<script>

var firstPage = '<li onclick="javascript:load('+cid+',1,\''+rname+'\')"><a href="javascript:void(0)">首页</a></li>';

var beforePage = '<li onclick="javascript:load('+cid+','+beforeNum+',\''+rname+'\')" class="threeword"><a href="javascript:void(0)">上一页</a></li>';

var li;
for (var i = begin; i <= end ; i++) {
    //当前页码是否等于i
    if (pb.currentPage == i){
    //创建一个页码的li
        li = '<li class="curPage" onclick="javascript:load('+cid+','+i+',\''+rname+'\')"><a href="javascript:void(0)">'+i+'</a></li>';
    }else{
        li = '<li onclick="javascript:load('+cid+','+i+',\''+rname+'\')"><a href="javascript:void(0)">'+i+'</a></li>';
    }
    //拼接字符串
    lis += li;
}

</script>
```

#### 后台代码

##### RouteServlet

```java
@WebServlet("/route/*")
public class RouteServlet extends BaseServlet {
	private RouteService routeService = new RouteServiceImpl();
    
    public void pageQuery(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //3.调用service查询PageBean对象
        PageBean<Route> pb = routeService.pageQuery(cid, currentPage, pageSize,rname);

    }
}
```

**解决rname乱码问题**

![image-20210818174623475](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20210818174623475.png)

##### RouteService

```java
public PageBean<Route> pageQuery(int cid, int currentPage, int pageSize, String rname){
    //设置总记录数
    int totalCount = routeDao.findTotalCount(cid,rname);
    pb.setTotalCount(totalCount);
    //设置当前页显示的数据集合
    int start = (currentPage - 1) * pageSize;//开始的记录数
    List<Route> list = routeDao.findByPage(cid,start,pageSize,rname);
    pb.setList(list);
}
```

##### RouteDao

```java
public class RouteDaoImpl implements RouteDao {
    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());

    @Override
    public int findTotalCount(int cid,String rname) {
//        String sql = "select count(*) from tab_route where cid = ?";
        //1.定义sql模板
        String sql = "select count(*) from tab_route where 1 = 1 ";
        StringBuilder sb = new StringBuilder(sql);
        
        List params = new ArrayList();//条件们
        //2.判断参数中是否有值
        if(cid != 0){
            sb.append("and cid = ? ");
            params.add(cid);//添加？对应的值
        }
        
        if(rname!=null && rname.length()>0){
            sb.append("and rname like ? ");
            params.add("%"+rname+"%");
        }
        
        sql = sb.toString();
        
        return template.queryForObject(sql,Integer.class,params.toArray());
    }

    @Override
    public List<Route> findByPage(int cid, int start, int pageSize,String rname) {
//        String sql = "select * from tab_route where cid = ? limit ? , ?";
        String sql = "select * from tab_route where 1 = 1 ";

        StringBuilder sb = new StringBuilder(sql);

        List params = new ArrayList();//条件们
        //2.判断参数中是否有值
        if(cid != 0){
            sb.append("and cid = ? ");
            params.add(cid);//添加？对应的值
        }

        if(rname!=null && rname.length()>0){
            sb.append("and rname like ? ");
            params.add("%"+rname+"%");
        }

        sb.append(" limit ? , ? ");//分页条件
        params.add(start);
        params.add(pageSize);
        sql = sb.toString();


        return template.query(sql,new BeanPropertyRowMapper<Route>(Route.class),params.toArray());
    }
}
```

### 旅游线路详情

#### 分析

![img](file:///C:\Users\鲸鱼\Documents\Tencent Files\1432843916\Image\C2C\F9542B27296961D7C4AD1F73CEFAB5A6.png)

#### 后台代码

##### RouteServlet

**在RouteServlet中编写findOne()方法**

```java
/**
 * 根据rid查询一个旅游线路的详细信息
 * @param request
 * @param response
 * @throws ServletException
 * @throws IOException
 */
public void findOne(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   //1.接收参数rid
   String rid = request.getParameter("rid");
   //2.调用service查询route对象
   Route route = routeService.findOne(rid);
   //3.转为json，写回客户端
   writeValue(route,response);
   
}
```

##### RouteService

**在RouteService中编写findOne(int rid)方法**

```java
public class RouteServiceImpl implements RouteService {

    private RouteDao routeDao = new RouteDaoImpl();
    private RouteImgDao routeImgDao = new RouteImgDaoImpl();
    private SellerDao sellerDao = new SellerDaoImpl();

    @Override
    public Route findOne(String rid) {
        //1.根据rid去tab_route表中查询route对象
        Route route = routeDao.findOne(Integer.parseInt(rid));
        //2.根据rid去tab_route_img表中查询图片集合信息
        List<RouteImg> list = routeImgDao.findByRid(route.getRid());
        //2.2将集合设置到route对象中
        route.setRouteImgList(list);
        //3.根据rid去tab_route表先查询到sid，再根据sid去tab_seller表查询买家信息
        Seller seller = sellerDao.findById(route.getSid());
        route.setSeller(seller);
        return route;
    }
}
```

##### RouteDao

**在RouteDao中编写操作表tab_route的方法**

```java
public class RouteDaoImpl implements RouteDao {
    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());

	/**
     * 根据rid查询一条线路
     * @param rid
     * @return
     */
    @Override
    public Route findOne(int rid) {
        String sql = "select * from tab_route where rid = ?";
        return template.queryForObject(sql,new BeanPropertyRowMapper<Route>(Route.class),rid);
    }


}
```

##### RouteImgDao

**在RouteImgDao中编写操作表tab_route_img的方法**

```java
public class RouteImgDaoImpl implements RouteImgDao {

    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());

    @Override
    public List<RouteImg> findByRid(int rid) {
        String sql = "select * from tab_route_img where rid = ?";
        return template.query(sql,new BeanPropertyRowMapper<RouteImg>(RouteImg.class),rid);
    }
}
```

##### SellerDao

**在SellerDao中编写操作表tab_seller的方法**

```java
public class SellerDaoImpl implements SellerDao {

    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());


    @Override
    public Seller findById(int sid) {
        String sql = "select * from tab_seller where sid = ?";

        return template.queryForObject(sql,new BeanPropertyRowMapper<Seller>(Seller.class),sid);

    }
}
```

#### 前台代码

route_detail.html加载后，

1. 获取rid
2. 发送ajax请求，获取route对象
3. 解析服务器返回的route对象的数据，将它填充到页面中

```javascript
<script>
$(function () {
	//1.获取rid
    var rid = getParameter("rid");
    //2.发送ajax请求，请求一个路径   /route/findOne
    $.get("route/findOne",{rid:rid},function (route) {
        //3.解析数据，填充html
        $("#rname").html(route.rname);
        $("#routeIntroduce").html(route.routeIntroduce);
        $("#price").html("￥"+route.price);
        $("#sname").html(route.seller.sname);
        $("#consphone").html(route.seller.consphone);
        $("#address").html(route.seller.address);

        //图片展示
        var ddstr = '<a class="up_img up_img_disable"></a>';
        //遍历routeImgList
        for (var i = 0; i < route.routeImgList.length; i++) {
            var astr;
            if (i >= 4){
                astr = '<a title="" class="little_img" data-bigpic="'+route.routeImgList[i].bigPic+'" style="display: none;">\n' +
                    '            <img src="'+route.routeImgList[i].smallPic+'">\n' +
                    '        </a>';
            }else{
                astr = '<a title="" class="little_img" data-bigpic="'+route.routeImgList[i].bigPic+'">\n' +
                    '            <img src="'+route.routeImgList[i].smallPic+'">\n' +
                    '        </a>';
            }


            ddstr += astr;
        }

        ddstr += '<a class="down_img down_img_disable" style="margin-bottom: 0;"></a>';

        //将ddstr内容设置到dd中
        $("#dd").html(ddstr);
        //图片展示和切换的代码调用
        goImg();
    })
})
</script>
```

### 旅游线路收藏

#### 分析

1. 判断当前登录用户是否收藏过该线路

   - 当页面加载完成后，发送ajax请求，获取用户是否收藏的标记flag
   - 根据标记，展示不同样式的按钮

   ![img](file:///C:\Users\鲸鱼\Documents\Tencent Files\1432843916\Image\C2C\34429C481FC3E96ADEEC2DE5464AAB1A.png)

   ##### 后台代码

   ###### RouteServlet

   ```java
   @WebServlet("/route/*")
   public class RouteServlet extends BaseServlet {
      private FavoriteService favoriteService = new FavoriteServiceImpl();
       
       /**
   	 * 判断当前登录用户是否收藏过该线路
   	 * @param request
   	 * @param response
   	 * @throws ServletException
   	 * @throws IOException
   	 */
   	public void isFavorite(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   		//1.获取线路id
   		String rid = request.getParameter("rid");
   		//2.获取当前登录的用户 user
   		User user = (User) request.getSession().getAttribute("user");
   		int uid;//用户的id
   		if(user == null){
   			//用户尚未登录
   			uid = 0;
   		}else{
   			//用户已经登录
   			uid = user.getUid();
   		}
   
   		//3.调用FavoriteService查询是否收藏
   		boolean flag = favoriteService.isFavorite(rid, uid);
   
   		//4.写回客户端
   		writeValue(flag,response);
   	}
   }
   ```

   ###### FavoriteService

   ```java
   public class FavoriteServiceImpl implements FavoriteService {
   
       private FavoriteDao favoriteDao = new FavoriteDaoImpl();
   
       @Override
       public boolean isFavorite(String rid, int uid) {
           Favorite favorite = favoriteDao.findByRidAndUid(Integer.parseInt(rid), uid);
           return favorite != null;//如果对象有值，表示已经收藏过，返回true
       }
   }
   ```

   ###### FavoriteDao

   ```java
   public class FavoriteDaoImpl implements FavoriteDao {
   
       JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
   
       @Override
       public Favorite findByRidAndUid(int rid, int uid) {
           Favorite favorite = null;
           try {
               String sql = "select * from tab_favorite where rid = ? and uid = ?";
               favorite = template.queryForObject(sql, new BeanPropertyRowMapper<Favorite>(Favorite.class), rid, uid);
           } catch (DataAccessException e) {
   
           }
           return favorite;
       }
   }
   ```

   ##### 前台代码

   route_detail.html

   ```javascript
   $(function () {
       //发送请求，判断用户是否收藏过该线路
       var rid = getParameter("rid");
       $.get("route/isFavorite",{rid:rid},function (flag) {
           if (flag){
               //表示用户已经收藏过
               //设置收藏按钮的样式  <a  class="btn already" disabled="disabled">
               $("#favorite").addClass("already");
               $("#favorite").prop("disabled",disabled);
           }else{
               //用户没有收藏
           }
       })
   })
   ```

2. 点击按钮收藏线路

   #### ![img](file:///C:\Users\鲸鱼\Documents\Tencent Files\1432843916\Image\C2C\4DBADC32B58FA079ADA2E13A9DF5BDDF.png)

   ##### 后台代码

   ###### RouteServlet

   ```java
   /**
    * 添加收藏
    * @param request
    * @param response
    * @throws ServletException
    * @throws IOException
    */
   public void addFavorite(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      //1.获取线路rid
      String rid = request.getParameter("rid");
   
      //2.获取当前登录的用户 user
      User user = (User) request.getSession().getAttribute("user");
      int uid;//用户的id
      if(user == null){
         //用户尚未登录
         return;
      }else{
         //用户已经登录
         uid = user.getUid();
      }
   
      //3.调用service添加
      favoriteService.add(rid,uid);
   
   
   }
   ```

   ###### FavoriteService

   ```java
   @Override
   public void add(String rid, int uid) {
       favoriteDao.add(Integer.parseInt(rid),uid);
   }
   ```

   ###### FavoriteDao

   ```java
   @Override
   public void add(int rid, int uid) {
       String sql = "insert into tab_favorite values(?,?,?)";
       template.update(sql,rid, new Date(),uid);
   }
   ```

   ##### 前台代码

   ```html
   a class="btn" id="favorite" onclick="addFavorite();"><i class="glyphicon glyphicon-heart-empty"></i>点击收藏</a>
   ```

   ```javascript
   <script>
   /**
    * 点击收藏按钮去触发的方法
    */
   function addFavorite() {
       var rid = getParameter("rid");
       //1.判断用户是否登录
       $.get("user/findOne",{},function (user) {
           if (user){
               //用户不为null——用户登录了
               //添加
               $.get("route/addFavorite",{rid:rid},function () {
                   //代码刷新页面
                   location.reload();
               })
           }else{
               //用户没有登录
               alert("您尚未登录，请登录！");
               location.href = "http://localhost/travel/login.html";
           }
   
       })
   }
   </script>
   ```

