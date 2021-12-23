# Java牛客网项目

## springboot核心作用

1. 起步依赖
2. 自动配置
3. 端点监控

## 搭建开发环境

### Maven

1. 安装
2. 修改镜像仓库地址为阿里云；将maven存放路径添加到环境变量path中，以便在任意命令行位置都可以使用maven
3. 测试常用指令：
   1. 创建新的maven项目...太长...文档有
   2. mvn clean
   3. mvn test（test包含了compile）
   4. mvn compile——产生.class文件

### IntelliJ IDEA

1. 安装

2. 为所有新项目配置maven

   https://github.com/ValoraY/NotePictures/blob/main/1.png

3. 创建新项目，注意修改Location到自己的工作空间

   ![image-20211109100037200](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20211109100037200.png)

4. 利用maven编译项目（快捷键：Ctrl+F9）

### Spring Initializr

基于maven，对包按照开发需求做了整合，可以一次性导入一类包

网址：https://start.spring.io/

1. 填信息

2. 点击GENERATE，保存压缩包

3. 解压到工作空间

   ![image-20211109101943153](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20211109101943153.png)

4. 在IDEA中open这个项目

## Spring

### IOC

#### 功能

**管理Bean：创建、初始化、销毁Bean**

实现**相互关联的Bean之间的解耦合。**

#### 原理

<u>**把创建Bean的任务交给Spring容器。**</u>

1. 给Spring容器提供**Bean**
2. 给Spring容器提供**包含不同Bean之间关系的配置文件**

**Spring容器不仅能代替我们创建Bean、还能代替我们完成对Bean的初始化和销毁**

#### 过程

1. 运行SpringApplication.run(CommunityApplication.class, args);主方法，**会自动创建Spring容器**（applicationContext这个接口就是Spring容器）
2. **Spring容器会自动扫描并创建CommunityApplication.class主配置类所在包及其子包的Bean**
3. 只要该类上面有注释@Controller、@Service、@Repository、@Component其中之一，该类对应的Bean就会在2中被创建

#### 测试类

1. 要实现**运行测试代码时，以主配置类为配置类**的功能，要在测试类上加注解

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest
   @ContextConfiguration(classes = CommunityApplication.class)
   ```

2. 要想**在某个类中获取Spring容器对象**，就要**让该类实现SpringContextAware接口、并复写该接口的setSpringContext方法**。

   这样，<u>Spring容器</u>在扫描组件时会检测到这样的Bean，并调用这个set方法<u>把自身传进来</u>。

   因此我们只需要**在该类里提供一个私有属性，并在set方法中把传进来的spring容器保存到该私有属性中**即可。

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest
   @ContextConfiguration(classes = CommunityApplication.class)
   class CommunityApplicationTests implements ApplicationContextAware {
   
   	private ApplicationContext applicationContext;
   	
   	@Override
   	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
   		this.applicationContext = applicationContext;
   	}
   }
   ```

3. 当一个接口有多个实现类时，要在其中一个类上加@Primary注解，Spring容器在装配该接口类型的Bean的时候会优先装配被该注解修饰的实现类。当想要调用非优先装配的Bean时，需要根据名字调用Bean

#### 装配第三方Bean

原始方法

1. 在config包下，**创建配置类**XXXConfig
2. 在该配置类中**创建方法**，方法上加@Bean注解，方法内部实例化该第三方Bean并返回
3. 在测试类（其他类）中即可**通过Spring容器获取到该第三方Bean**

**依赖注入方法**

**需要在哪个类中使用哪个Bean，就在该类中添加对应的私有属性，并在该属性上加@AutoWired注解**

```java
@Autowired
//把AlphaDao类的Bean注入alphaDao这个属性
private AlphaDao alphaDao;
```

可以在该属性上面加注解@Qualifier("Bean的名称")，注入指定实现类的Bean

## Spring MVC

### application.properties

网页：https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#application-properties

学习：

1. 查看网页，找到你配置的属性对应的  properties名称
2. 在IDEA中，Ctrl+N，查找  properties名称，找到对应的  配置类，学习了解

## MyBatis

### Mysql-Server安装&初始化

1. 下载zip，解压到D:\develop

2. 在mysql安装目录（D:\develop\mysql-8.0.15-winx64）下，粘贴my.ini文件，修改其中的Mysql安装路径为D:\develop\mysql-8.0.15-winx64

3. 配置环境变量 在系统变量-path-新建-D:\develop\mysql-8.0.15-winx64

4. 用管理员身份打开命令行，转到mysql安装目录下的/bin目录

   ```
   1.mysqld --initialize --console //console会产生一个随机密码，保存
   2.mysqld install
   3.net start mysql
   ```

   **——mysql后端服务器安装成功**

5. 正常启动命令行，登录mysql

   ```
   -> mysql -uroot -p
   -> 输入4中生成的随机密码
   mysql->alter user root@localhost identified by '新密码';
   
   ```

   **——修改root用户的密码成功**

6. 往后用用户名root，新密码登录mysql

```
用户名：root
密码：203166
```

### 创建数据库和表

```
//在命令行，利用 用户名和密码 成功登录mysql之后：
create database community;
use community;
source D:/develop/workSpace/community-init-sql-1.5/init_schema.sql   //导入“创建表结构”的脚本
source D:/develop/workSpace/community-init-sql-1.5/init_data.sql     //导入“添加示例数据”的脚本
//注意：mysql中，路径斜线要用左斜线！！！
```

**——数据库、表、示例数据创建成功**

### Mysql-Client安装

mysql-workbench

### 整合Spring Boot - Mybatis

1. 在pom.xml中导入相关依赖

   ```xml
   //mysql依赖
   <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.27</version>
   </dependency>
   //spring整合mybatis的依赖
   <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.2.0</version>
   </dependency>
   ```

2. 在application.properties中配置**mysql的数据源和连接池、mybatis**

   ```properties
   # DataSourceProperties  配置mysql数据库和连接池
   spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
   spring.datasource.url=jdbc:mysql://localhost:3306/community?characterEncoding=utf-8&useSSL=false&serverTimezone=Hongkong
   spring.datasource.username=root
   spring.datasource.password=203166
   # 配置连接池
   # 配置连接池的类型为HikariDataSource
   spring.datasource.type=com.zaxxer.hikari.HikariDataSource
   # 配置连接池的最大连接数为15
   spring.datasource.hikari.maximum-pool-size=15
   # 配置最小空闲连接数为5
   spring.datasource.hikari.minimum-idle=5
   # 配置空闲多长时间后释放该连接
   spring.datasource.hikari.idle-timeout=30000
   
   # MybatisProperties  配置mybatis
   # 配置映射文件的位置 classpath代表编译后的classes文件夹 resources/mapper/*.xml编译后放在classes/mapper/*.xml
   mybatis.mapper-locations=classpath:mapper/*.xml
   # 配置与数据库中的表对应的用于封装的实体类所在的包
   mybatis.type-aliases-package=com.nowcoder.community.entity
   # 配置 启用主键自增
   mybatis.configuration.useGeneratedKeys=true
   # 配置 数据库中的表的字段名 与 实体类中的属性名 忽略大小写的自动匹配 如表中的header_ur字段 和 实体类中的headerUrl属性
   mybatis.configuration.mapUnderscoreToCamelCase=true
   ```

3. 在entity包下，创建与数据表对应的**实体类**

4. 在dao包下，声明与数据表对应的**mapper接口**

5. 在resources/mapper目录下，创建为4中的mapper接口服务的**xml映射文件**，为mapper接口中的方法编写相应的sql语句

6. 在application.properties配置文件中，将dao包的日志级别设置为debug，这样可以在控制台显示出实际执行的sql语句

   ```properties
   # 把com.nowcoder.community这个包的日志级别设置为debug，这样dao包的日志级别也是debug，可以显示出实际执行的sql语句
   logging.level.com.nowcoder.community=debug
   ```

## 开发社区首页

### 功能需求

1. 开发社区首页，显示前10条帖子
2. 开发分页组件，分页显示所有帖子

### 开发数据访问层Dao

1. 创建与数据表对应的、用于封装的**实体类**

2. 在dao包下，声明与数据表对应的**mapper接口**

   > 注意：
   >
   > 1.如果方法只有一个参数，且该参数要在sql语句的<if>里使用，则该参数前必须加@Param(“别名”)，给这个参数取别名

3. 在resources/mapper目录下，创建为4中的mapper接口服务的**xml映射文件**，为mapper接口中的方法编写相应的sql语句

   > 注意：
   >
   > 1.区分什么时候用字段、什么时候用属性。e.g. where 字段 = 属性;

### 开发业务层Service

1. 在service包下，创建调用相应的dao层的**service类**

### 开发视图层Controller

1. 在controller包下，创建调用相应的service层的**controller类**

   > 注意：
   >
   > 1.HomeController类可以不写该类的访问路径，直接输入方法的访问路径访问即可

### 开发视图层模板

1. 在resources/template目录下，创建**模板html**

2. 在模板html中，指定所用的模板引擎

   ```html
   <html lang="en" xmlns:th="http://thymeleaf.org">
   //注意：一定不能是<html lang="en" xmlns:th="http://www.thymeleaf.org">  加了www之后就会出问题
   ```

3. 修改模板html中、用相对路径引用的静态资源(css/js/img)的路径

   ```html
   <script th:src="@{/js/global.js}"></script>
   //意思是让thymeleaf去static目录下找相应的静态资源
   ```

4. 利用th将模板html的数据替换为动态值

### 开发分页组件

1. 在entity包下，创建用于封装页面的**Page类**

2. 改造**controller类**，将Model对象和Page对象一起从服务器返回给浏览器

   > 注意：
   >
   > 方法调用前，SpringMVC会自动实例化Model和Page，并将Page注入Model，所以在将Model返回给Thymeleaf的同时也返回了Page，故在Thymeleaf中，可以直接使用Page中的数据

3. 修改thymeleaf中的**模板html**，处理其中的分页逻辑

## 项目调试技巧

### HTTP状态码

1. 200：成功
2. 302：重定向
3. 404：客户端程序有问题。大概率原因：请求路径写错、href链接路径写错......
4. 500：服务器端程序有问题。

### 服务器端程序调试

1. 在服务器端程序**Controller的方法**内部打断点，debug方式启动
2. 在**浏览器访问该方法**，跳到断点卡住
3. 开始调试 
   - F8：向下一行
   - F7：进入到当前行所调用的方法内部
   - F9：程序直接向下执行到下一个断点。用于跳过一段你确认没问题的代码。如果没有下一个断点，程序直接执行到底。
   - 左下角有管理断点的按钮

### 客户端程序调试

**本质是调试js**

1. 在浏览器点击右键-检查-source-选中js文件-在**js文件中的方法**内部打断点
2. 在**浏览器页面访问该js中的方法**，跳到断点卡住
3. 开始调试
   - F10：向下执行一行
   - F11：进入当前行所调用的方法内部。一般会跳到jquery-3.3.1-min.js内部，没法调......一般不进来
   - F8：程序直接执行到下一个断点。如果没有下一个断点，程序直接执行到底。
   - 右下角有管理断点、查看变量值的按钮

### 按照级别记录日志

#### 简单记录日志

> 在application.properties文件中，配置
>
> ```properties
> #把com.nowcoder.community这个包及其子包的日志级别设置为trace/debug/info/warn/error...只有高于该级别的日志信息才会被打印
> logging.level.com.nowcoder.community=error
> 
> # 把日志文件保存到指定位置
> logging.file.name=d:/develop/workSpace/data/test.log
> ```

#### 分类型记录日志

**在src/main/resources文件夹下，放入logbcak-spring.xml文件（文件名必须是这个）**

> 注意：
> 1.修改logback-spring.xml中日志文件的存放位置
>
> ```xml
> <!--
> 需要修改log_path和appdir,最终日志文件存储在“${log_path}/${appdir}/”文件夹下
> -->
>  <property name="LOG_PATH" value="D:/develop/workSpace"/>
>  <property name="APPDIR" value="community"/>
> ```
>
> 2.修改logback-spring.xml中最后项目的包名
>
> ```xml
> <!--
>  需要修改包名为你项目对应的包名
> -->
>  <logger name="com.nowcoder.community" level="debug"/>
> ```

## 版本控制

1. 安装git

2. 打开git cmd，了解git命令。**首先cd到被管理的项目所在目录**

   - git init           ——初始化项目
   - git add *       ——暂存到本地仓库
   - git commit -m "信息"     ——提交到本地仓库

3. 在git cmd中，生成ssh密钥

   ```
   //在git cmd输入
   ssh-keygen -t rsa -C "1432843916@qq.com"
   ```

   根据提示在对应的文件id_rsa.pub中找到生成的密钥并复制

4. 打开github，点击右上方头像的下拉菜单 - settings - SSH and GPG Keys - New SSH Keys - 粘贴3中复制的密钥 - 保存

5. 在github中，**创建远程仓库**。”新项目的网址.git“就是远程仓库地址，复制该地址

6. 在git cmd中，**本地声明远程仓库**，为远程仓库”新项目的网址.git“起别名 origin

   ```
   git remote add origin "htto://github项目路径.git"
   ```

7. 在git cmd中，**向远程仓库推送内容**。输入命令，再输入账号密码后即可推送成功。

   ```
   git push -u origin master
   //向远程仓库origin的master分支推送内容
   ```

8. 在git cmd中，下载远程仓库的项目

   ```
   //先在github选择要克隆的项目，点击code右侧下拉按钮-clone-HTTPS-复制链接，即为“远程仓库地址”
   git clone 远程仓库的地址
   ```

### IDEA中配置Git

1. 让IDEA知道git.exe的安装路径

   ![image-20211115223846017](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20211115223846017.png)

2. 选中项目 - VCS - Import into Version Control - Create Git Repository      ——创建本地仓库

3. 选中项目 - Git - Commit - 选中需要提交的文件 - 填写commit message - commit    ——提交到本地仓库

4. 在github上，创建远程仓库，获得远程仓库地址 “远程仓库地址.git”

5. 在IDEA中，Git - push - 粘贴远程仓库地址 - push - 输入github账号密码

## 发送邮件

1. 开启邮件客户端的SMTP服务。QQ邮箱 - 设置 - 账户 - 开启服务 ：POP3/SMTP服务 - 保存更改

2. 在pom.xml中，导入mail的jar包

3. 在application.properties中，配置发送邮件的参数

   ```properties
   # MailProperties
   # 客户端的SMTP服务器的域名
   spring.mail.host=smtp.qq.com
   # 客户端的SMTP服务器发送邮件的端口号
   spring.mail.port=465
   spring.mail.username=1432843916@qq.com
   spring.mail.password=13593311425xjy!
   spring.mail.protocol=smtps
   # 发送邮件时采用安全连接
   spring.mail.properties.mail.smtp.ssl.enable=true
   ```

4. 在util包下，创建MailClient类

   1. 在类上加@Component，把该类的对象交给Spring容器管理

   2. 类中自动注入JavaMailSender对象，用于发送MimeMessage对象

   3. 创建方法，三个参数（接收芳邮箱、邮件主题、邮件内容）用于发送邮件

   4. 在方法内部，创建MimeMessage对象，用于封装邮件内容

   5. 在方法内部，创建MimeMessageHelper对象，用于构建MimeMessage对象的内容

   6. MimeMessage对象构建好之后，JavaMailSender对象调用sendMail()方法，传入参数MimeMessage对象和true，发送邮件

      ```java
      @Component
      public class MailClient {
      
          private static final Logger logger = LoggerFactory.getLogger(MailClient.class);
      
          @Value("${spring.mail.username}")
          private String from;
      
          @Autowired
          private JavaMailSender mailSender;
      
          public void sendMail(String to,String subject,String content){
              try {
                  MimeMessage message = mailSender.createMimeMessage();
                  MimeMessageHelper helper = new MimeMessageHelper(message);
                  helper.setFrom(from);
                  helper.setTo(to);
                  helper.setSubject(subject);
                  helper.setText(content,true);
                  mailSender.send(helper.getMimeMessage());
              } catch (MessagingException e) {
                  logger.error("发送邮件失败："+e.getMessage());
              }
          }
      
      }
      ```

## 开发注册功能

### 功能1：访问注册页面

**效果：点击顶部区域的链接，打开注册页面**

1. 创建LoginController类，用于存放与注册相关的方法
2. 在LoginController类中，创建getRegister()方法
   1. 方法上加注解，@RequestMapping(path="/register",method=REQUEST.GET)
   2. 在方法内部，返回注册页对应的<u>模板的存放路径</u>
3. 修改注册页对应的模板html，交给thymeleaf管理
   1. 在<html>标签内部添加，xmlns:th="http://thymeleaf.org"
   2. 修改静态资源的相对路径，th:href=@{}
4. 修改首页头部注册按钮对应的链接，让该链接访问LoginController类中的getRegister()方法
   1. 将注册按钮对应的访问链接修改为，th:href=@{/register}
   2. 复用首页的头部，在首页的<header>标签内部添加 th:fragment="header"，为该头部取名称为"header"
   3. 在注册页的头部，<header>标签内部添加 th:replace="index::header"（意为：用index模板页的名称为header的部分代替register模板页的<header>部分）

### 功能2：提交注册数据

**效果：**

**1.通过表单提交数据**

**2.服务端验证账号是否已经存在、邮箱是否已经注册**

**3.服务端发送激活邮件**

1. 在pom.xml中，导入commons-lang依赖，用于处理字符串、集合空值的情况

2. 在application.properties中，配置网站域名，用于邮件的激活链接来指向它

3. 在util包下，创建CommunityUtil类，用于封装一些常用的工具方法

4. 在CommunityUtil类中，编写工具方法——生成随机字符串、对密码进行md5加密

   ```java
   public class CommunityUtil {
       //生成随机字符串
       //生成验证码、salt的时候需要用到
       public static String generateUUID(){
           return UUID.randomUUID().toString().replaceAll("-","");
       }
   
       //md5 对密码进行加密
       //hello -> abc123
       //hello + salt -> 1bc123thu
       //传入的key是 密码和salt的拼接字符串
       public static String md5(String key){
           if(StringUtils.isBlank(key)){
               return null;
           }
           return DigestUtils.md5DigestAsHex(key.getBytes());//把传入的结果加密成16进制的字符串
       }
   }
   ```

5. 正式开始实现注册功能。因为数据访问层在“开发社区首页”部分已经提前写好，所以现在从业务层UserService开始写。

6. **在UserService类中，实现注册逻辑**

   1. 自动注入
      - MailClient对象，用于发送邮件
      - TemplateEngine，用于生成邮件内容
      - 域名值，用于生成激活邮件中的链接
      - 项目路径，用于生成激活邮件中的链接
   2. 判断各种无法注册的情况，将错误信息保存到map对象中，并返回map对象
      - 用户为null
      - 用户名为空
      - 邮箱为空
      - 密码为空
      - 用户名已经存在
      - 邮箱已注册
   3. 当2中判断全都不满足时，该信息才可以注册
      1. 补全用户的信息。包括状态、类型、激活码……
      2. 向数据库插入用户
   4. 发送激活邮件
      1. 利用templateEngine模板引擎生成邮件内容content
         1. 创建Context对象，用于保存<u>动态替换模板html中内容的</u>变量
         2. String content  =  templateEngine.process("模板路径",context)
      2. 利用mailClient对象发送邮件

7. **在LoginController类中**

   1. 自动注入UserService对象，用于调用业务层的方法
   2. 创建注册方法
      1. 调用业务层的注册方法，得到返回值map
      2. 判断返回值map
         - **如果map为空，说明注册成功，跳转到oprate-result模板**
           1. 在model中封装信息，包括”提醒用户前往邮箱验证的信息“、”几秒后跳转到某链接的路径“……
           2. 跳转到oprate-result模板，提示用户前往邮箱进行激活
           3. 修改oprate-result模板头部，用首页的头部代替
         - **如果map不为空，说明注册信息存在错误，跳转到register模板**
           1. 将map中保存的错误信息取出，封装到model对象中
           2. 跳转到register模板，用model对象中封装的错误信息动态替换模板中对应内容
           3. 修改register模板头部，用首页的头部代替

8. **在register.html中，修改form表单的提交信息，**实现点击提交按钮时，会调用LoginController类中的register方法，依次调用UserService类中的register方法……

   1. 在<form>标签中，添加 method="post"    th:action = "@{/register}"
   2. 在<input>标签中，添加对应的name，用于Spring实现同名属性自动注入属性值
   3. 在显示错误信息的<div>标签中，添加  th:text=${usernameMsg} 或 th:text=${passwordMsg} 或 th:text=${emailMsg}
   4. 修改输入框的class样式属性，让它变成动态的。如果usernameMsg不为空，才在该class样式属性上添加is-invalid样式，passwordMsg和emailMsg同理。

> 因为直接点击页面头部的”注册“按钮 和 注册信息有误，这2种情况都会跳转到register模板，所以复用register模板时，需要做一些额外调整。
>
> **默认值的显示**
>
> 当因为注册信息有误而跳转到register模板时，输入框内需要显示刚刚保存的用户名、邮箱、密码等信息；而点击注册按钮跳转到register模板时，则不需要这样。**默认值用value属性实现。**

### 功能3：激活注册账号

**效果：点击邮件中的链接，访问服务端的激活服务**

1. 在UserMapper类中，编写selectById(int userId)、updateStatus(int userId)方法，用于操作数据库查找用户、更新用户的激活状态
2. 在UserService类中，编写activation(int userId,String activationCode)方法。
   - 业务逻辑是：1.先通过userId在数据库中查询到该用户  2.检查该用户的激活状态  3.根据不同的激活状态，返回不同的整数值给Controller层（这些不同的整数值标志着“激活成功”、“重复激活”、“激活失败”这三种状态）
   - 在util包下，创建CommunityConstant接口，定义三个整数常量，分别标志“激活成功”、“重复激活”、“激活失败”这三种状态
   - 让userService类继承CommunityConstant接口，方便在该类中使用这三个整数常量
3. 在LoginController类中，创建activation(Model model,int userId,String activationCode)方法
   1. 调用userService对象的activatioon方法，获得代表“激活成功”、“重复激活”、“激活失败”这三种状态的整数值
   2. 对这些整数值进行判断，在每种情况下，分别封装对应的信息"msg"和目标链接"target"给model
      - "激活成功"，设置目标链接为"/login"。该请求尚未创建方法处理。所以在LoginController中创建方法处理该请求、同时修改对应的模板/site/login.html为tymleaf接管、另外记得把首页上"登录"按钮对应的链接转到访问Controller中的该方法的请求路径。
      - "激活失败"和"重复激活"，设置目标链接为"/index"。该请求已经有方法进行处理了。
   3. 每种情况的信息分别封装后，跳转到operate-result模板。该模板中动态获取model中属性的逻辑已经处理过。

## 会话管理

cookie：用户数据存储在客户端

session：用户数据存储在服务器端

分布式部署存储用户数据方法：

1. 黏性session
2. 同步session
3. 共享session
4. 尽量存到cookie中，不方便存到cookie的敏感数据存入数据库（存入NOSQL数据库Redis中，性能更好）

## 生成验证码

1. 在pom.xml中导入kaptcha包
2. 在config包下，创建KaptchaConfig配置类，提供kaptchaProducer方法，返回Producer对象，把该对象交给spring容器管理
3. 在LoginController类中，创建生成验证码的方法getKaptcha
   1. 从容器中注入Producer对象
   2. 利用Producer对象生成验证码文本、图片
   3. 将验证码文本存入服务器端的session中，用于登录验证
   4. 将验证码图片输出到客户端浏览器
4. 在login.html中，修改验证码src属性为  方法getKaptcha的访问路径，用于实现刷新登录页面自动更新验证码功能
5. 在login.html中，修改文字“刷新验证码”的href属性指向的js方法，用于实现 每次点击“刷新验证码”，自动更新验证码图片的src属性

## 开发登录、退出功能

### 功能1：访问登录页面

**效果：点击顶部区域内的链接，打开登录页面**

### 功能2：登录

#### 效果

1. **验证账号、密码、验证码**
2. **成功时，生成登录凭证，发放给客户端**
3. **失败时，跳转回登录页**

#### 实现

##### 数据库访问层

1. 在entity包下，创建与数据库中login_ticket表对应的实体类LoginTicket（因为服务端程序需要将 登录凭证的信息 存储到数据库的login_ticket表中）
2. 在dao包下，创建操纵数据表login_ticket的接口LoginTicketMapper，创建增改查方法
3. 在接口LoginTicketMapper接口中，利用注解编写sql语句——与在resources/mapper目录下，创建loginticket-mapper.xml编写sql语句等效
4. 在test/java包下的MapperTests测试类中，自动注入loginTicketMapper对象，测试3中的增改查方法能否正常运行

##### 业务层

1. 在UserService类中，自动注入loginTicketMapper对象，用于操纵数据表
2. 在UserService类中，创建login(username,password,expiredSeconds)方法
   1. 对参数进行空值处理
   2. 验证合法性——与数据库比对用户名是否存在、账号是否激活、密码是否正确
   3. 验证通过后，生成登录凭证对象loginTicket，设置相关信息
   4. 利用loginTicketMapper对象，将登录凭证对象loginTicket存入数据库
   5. 将登录凭证对象loginTicket的ticket信息封装到map中，返回给客户端

##### 表现层

1. 在LoginController类中，创建login方法，用于处理浏览器发来的post请求
   1. 验证验证码
   2. 验证账号和密码：利用userService对象的login方法，获得返回值map
      1. 如果map包含键“ticket”，说明登录成功
         1. 将map中的ticket值封装到cookie中，返回给客户端
         2. 返回到首页（首页的thymeleaf模板存放位置）
      2. 否则，登录失败
         1. 将map中的错误信息封装到model中
         2. 返回到登录页面（登录的thymeleaf模板存放位置）
2. 在login.html中，
   1. 处理发送请求
      1. 给表单添加请求方式为post，请求路径为LoginController类中的login方法的请求路径
      2. 给表单中的输入框<input>添加name属性，属性名要与LoginController类中login方法的参数名一致，这样才能提交表单并自动注入参数值
   2. 处理因登录有错而返回login.html的问题
      1. 添加默认值：给表单的<input>输入框添加th:value属性，值要从Controller中动态获取
      2. 对应显示model中封装的错误信息：在相应的<div>中，添加th:text属性，动态获取model中封装的相应错误信息
      3. 修改th:class样式，动态判断是否显示错误信息

### 功能3：退出

#### 效果

1. **将登录凭证修改为失效状态**
2. **跳转至网站首页**

#### 实现

##### 数据访问层

在LoginTicket类中，

1. 创建updateStatus方法，用于修改登录凭证对象的状态（有效->无效）

##### 业务层

在UserService类中，

1. 创建logout(ticket)方法，调用数据访问层的updateStatus方法，修改登录凭证的状态，实现退出

##### 表现层

在UserController类中，

1. 创建logout(ticket)方法，从cookie中获取ticket值传入，调用业务层的logout方法实现退出
2. 重定向去访问Controller类中/login的请求路径

## 显示登录信息

### 拦截器示例

1. 在controller包下，新建interceptor包，在interceptor包下，创建AlphaInterceptor类。
   1. 在AlphaInterceptor类上，加注解@Component，把AlphaInterceptor对象交给Spring容器管理
   2. 在AlphaInterceptor类中，实现HandlerInterceptor接口，重写preHandle、postHandle、afterCompletion方法
2. 在config包下，创建WebMvcConfig类
   1. 在WebMvcConfig类上，加注解@Configration
   2. 在WebMvcConfig类中，自动注入1中创建并交给Spring容器管理的AlphaInterceptor对象
   3. 在WebMvcConfig类中，实现WebMvcConfigurer接口，重写addInterceptors(InterceptorRegistry registry)方法
   4. 在addInterceptors(InterceptorRegistry registry)方法中，配置拦截路径和不拦截路径

### 拦截器实现

1. 在controller包下的interceptor包下，创建LoginInterceptor类
   1. 在LoginInterceptor类上，加注解@Component，把LoginInterceptor对象交给Spring容器管理
   2. 在LoginInterceptor类中，实现HandlerInterceptor接口，重写preHandle、postHandle、afterCompletion方法
   3. 在preHandle方法中，实现
      1. 获取cookie中的ticket
      2. 根据ticket查询获得loginTicket对象
      3. 从loginTicket凭证中获取userId，进一步得到用户对象user
      4. 在本次请求中持有该用户
   4. 在postHandle方法中，实现
      1. 获取到本次请求中的用户user
      2. 将user封装到ModelAndView中
   5. 在afterCompletion中，清空保存的用户
2. 在config包下的WebMvcConfig类中，
   1. 在WebMvcConfig类上，加注解@Configration
   2. 在WebMvcConfig类中，自动注入1中创建并交给Spring容器管理的LoginInterceptor对象
   3. 在WebMvcConfig类中，实现WebMvcConfigurer接口，重写addInterceptors(InterceptorRegistry registry)方法
   4. 在addInterceptors(InterceptorRegistry registry)方法中，配置拦截路径和不拦截路径
3. 修改index.html
   1. 在顶部区域中相应标签中添加th:if条件判断，如：用户登录时，顶部区域才能显示头像部分和”消息“；用户未登录时，顶部区域显示”注册“和”登录“
   2. 修改头像的路径为动态路径，loginUser.headerUrl
   3. 修改点击头像显示的用户名称为动态名称 loginUser.username

## 账号设置

### 功能1：获取账号设置页面

1. 在controller包下，创建UserController类，在该类中创建getSettingPage()方法，返回对应的thymeleaf模板路径
2. 修改1路径中的html页面为thymeleaf模板，<header>复用index的<header>
3. 修改index页面“账号设置”指向的链接，让它指向UserController类中的getSettingPage()方法的请求路径

### 功能2：上传文件

1. 在application.properties中配置文件上传路径
2. 在UserService类中，创建updateHeaderUrl方法，用于更新用户头像
3. 在UserController类中，创建uploadHeader方法
   1. 将头像上传至服务器指定的硬盘位置，
   2. 利用userService对象，更新用户对应的头像的web访问路径

### 功能3：获取文件

在UserController类中，创建getHeader方法

1. 根据web访问路径，确定图片在服务器端硬盘的存储位置
2. 向浏览器响应图片
   1. 设置响应的内容类型（先截取图片的后缀名 - > "image/" + suffix）
   2. 创建输入流、输出流，向浏览器输出二进制数据（图片）

### 修改表单

在setting.html中，

1. 在<form>标签内添加  th:action、method 、 enctype属性
2. 在上传图片的<input>标签内，添加name属性，注意要与UserController中处理该请求的方法的对应参数名相同。这样Spring才会自动匹配
3. 修改<input>标签内的class属性，动态显示错误信息

## 检查登录状态

1. 自定义注解@LoginRequired，并加在Controller中（需要登录才能访问的）方法上，用于标识这些方法
2. 在interceptor包下，创建LoginRequiredInterceptor拦截器，识别有这些注解的方法，进行拦截
   1. 自动注入hostHolder对象，用于判断当前用户是否登录
   2. 判断拦截对象是否为 方法（即HandlerMethod类型），如果是，将 拦截对象 强制转换为 HandlerMethod类型，并得到method对象
   3. 通过method对象，获得LoginRequired类型的注解
   4. 判断该注解是否为空，如果不为空，继续判断用户是否登录，如果没有登录，则重定向到登陆页面，并return false，拦截该请求
3. 在config包下的WebMvcConfig类中，配置LoginRequiredInterceptor，拦截静态资源的请求

## 过滤敏感词

### 敏感词过滤器

1. 创建前缀树
2. 根据敏感词，初始化前缀树
3. 实现查找过滤敏感词的算法

在util包下，创建SensitiveFilter类，在类中：

1. 创建内部类TrieNode，构建前缀树的数据结构
2. 根据敏感词，初始化前缀树
3. 实现查找过滤敏感词的算法

## 发布帖子

1. 在CommunityUtil类中，创建getJSONString方法，用于将服务器返回给浏览器的数据封装称json字符串
   1. 在pom.xml中导包fastjson
   2. 在getJSONString方法中，创建JSON对象，封装服务器返回的数据，将封装结果转为String返回
   3. 重载getJSONString方法，方便用户调用
2. 开发数据访问层
   1. 在DiscussPostMapper类中，增加插入帖子的方法insertDiscussPost(Di是cussPost)
   2. 在discusspost-mapper.xml中编写对应的sql语句
3. 开发业务层
   1. 在DiscussPostService类中，增加插入帖子的方法addDiscussPost(DiscussPost)
   2. 对传入的DiscussPost对象进行判空处理、转义html标签处理、过滤敏感词处理
   3. 调用数据访问层的insertDiscussPost()方法，向数据库插入帖子
4. 开发视图层
   1. 在controller包下，创建DiscussPostController类
   2. 在DiscussPostController类中，增加用于处理  插入帖子请求的  方法addDiscussPost(String title,String content)
   3. 在addDiscussPost方法中，
      1. 先判断当前用户是否登录，如果没有登录，则向浏览器返回“未登录不能发帖”的提示信息（注意是异步请求，返回json格式的字符串）
      2. 如果用户已经登录，根据传入的参数构造DiscussPost对象
      3. 调用业务层的addDiscussPost()方法，完成插入帖子
5. 开发页面
   1. 在index.js里修改publish方法

## 帖子详情

## 事务管理

### 注解式

在方法上加注解@Transactional(isolation = Isolation.READ_COMMITTED,propagation = Propagation.REQUIRED)

隔离级别isolation：

1. READ_COMMITTED
2. REPEATABLE_READ
3. SERIALIZABLE

传播路径propagation：

1. REQUIRED：支持外部事务，如果不存在，创建新事务（按照内部事务执行）
2. REQUIRES_NEW：支持内部事务，同时暂停外部事务
3. NESTED：内部事务嵌套在外部事务中执行

### 编程式

1. 自动注入TransactionalTemplate对象

2. 设置TransactionalTemplate的隔离级别和传播机制

   ```java
   transactionTemplate.setIsolationLevel(TransactionDefinition.ISOLATION_READ_COMMITTED);
   transactionTemplate.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
   ```

3. 执行业务逻辑

   ```
   transactionTemplate.execute(new TransactionCallback<Object>() {
       @Override
       public Object doInTransaction(TransactionStatus status) {
       	//事务的业务逻辑代码
       }
   }
   ```

## 显示评论

## 添加评论

## 私信列表

## 发送私信

## 统一处理异常

在controller.advice包下，创建ExceptionAdvice类

```java
@ControllerAdvice(annotations = Controller.class)
public class ExceptionAdvice {

    private static final Logger logger = LoggerFactory.getLogger(ExceptionAdvice.class);

    @ExceptionHandler({Exception.class})
    public void handleException(Exception e, HttpServletRequest request, HttpServletResponse response) throws IOException {
        logger.error("服务器异常： "+e.getMessage());
        StackTraceElement[] stackTrace = e.getStackTrace();
        for (StackTraceElement element : stackTrace) {
            logger.error(element.toString());
        }

        String XRequestedWith = request.getHeader("x-requested-with");
        if("XMLHttpRequest".equals(XRequestedWith)){
            //异步请求，返回普通字符串
            response.setContentType("application/plain;charset=utf-8");
            PrintWriter writer = response.getWriter();
            writer.write(CommunityUtil.getJSONString(1,"服务器异常"));
        }else{
            response.sendRedirect(request.getContextPath() + "/error");
        }

        //一般请求，重定向到500错误页面

    }
}
```

## 统一记录日志

在aspect包下，创建ServiceLogAspect类，统一记录service层的日志

```java
@Component
@Aspect
public class ServiceLogAspect {

    private static final Logger logger = LoggerFactory.getLogger(ServiceLogAspect.class);

    @Pointcut("execution(* com.nowcoder.community.service.*.*(..))")
    public void pointCut(){

    }

    @Before("pointCut()")
    public void before(JoinPoint joinPoint){
        //用户[.2.3.4]在[xxx]访问了[com.nowcoder.community.service.xxx()]
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        String ip = request.getRemoteHost();
        String date = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date());
        String func = joinPoint.getSignature().getDeclaringTypeName() + "." + joinPoint.getSignature().getName();
        logger.info(String.format("用户[%s]在[%s]访问了[%s]",ip,date,func));

    }
}
```

## Redis

### Spring整合Redis

1. 引入依赖

   在pom.xml导包starter-data-redis

2. 配置Redis

   1. 在application.properties中，配置数据库参数
   2. 在config包下，编写配置类RedisConfig，向容器中装配redisTemplate对象

3. 访问Redis

   1. 向redis中存数据
      1. 在RedisKeyUtil工具类中，创建获取相应的redisKey的方法getXXXKey()
      2. 在service层
         1. 调用getXXXKey()方法，获得redisKey
         2. 利用redisTemplate向redis中存数据。redisTemplate.opsForValue().set(redisKey,Value)
      3. 在service层
         1. 调用getXXXKey()方法，获得redisKey
         2. 利用redisTemplate从redis中取数据。redisTemplate.opsForValue().get(redisKey)
   2. 从redis中取数据

## Kafka

### 启动zookeeper

在控制台执行

```
d:\develop\kafka_2.13-2.8.1>bin\windows\zookeeper-server-start.bat config\zookeeper.properties
```

### 启动kafka服务器

在控制台执行

```
d:\develop\kafka_2.13-2.8.1>bin\windows\kafka-server-start.bat config\server.properties
```

### Spring整合Kafka

1. 引入依赖

   在pom.xml导包spring-kafka

2. 配置kafka

   1. 在application.properties中，配置kafka参数

      ```properties
      # KafkaProperties
      spring.kafka.bootstrap-servers=localhost:9092
      spring.kafka.consumer.group-id=test-consumer-group
      spring.kafka.consumer.enable-auto-commit=true
      spring.kafka.consumer.auto-commit-interval=3000
      ```

3. 使用kafka

   1. 生产者

      ```java
      @Component
      class KafkaProducer{
      
          @Autowired
          private KafkaTemplate kafkaTemplate;
      
          public void sendMessage(String topic,String content){
              kafkaTemplate.send(topic,content);
          }
      }
      ```

   2. 消费者

      ```java
      @Component
      class KafkaConsumer{
      
          @KafkaListener(topics = {"test"})
          public void handleMessage(ConsumerRecord record){
              System.out.println(record.value());
          }
      }
      ```

   3. 测试

      ```java
      public class KafkaTests {
      
          @Autowired
          private KafkaProducer kafkaProducer;
      
          @Test
          public void testKafka(){
              kafkaProducer.sendMessage("test","你好");
              kafkaProducer.sendMessage("test","在吗");
      
              try {
                  Thread.sleep(1000*10);
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
      }
      ```

## 发送系统通知

1. 在entity包下，新建Event类，封装事件信息

   ```java
   public class Event {
   
       private String topic;
       private int userId;
       private int entityType;
       private int entityId;
       private int entityUserId;
       private Map<String,Object> data = new HashMap<>();
       
   }
   ```

2. 在event包下，创建EventProducer类

   ```java
   @Component
   public class EventProducer {
   
       @Autowired
       private KafkaTemplate kafkaTemplate;
   
       //处理事件——本质：发送消息到对应主题
       public void fireEvent(Event event){
           kafkaTemplate.send(event.getTopic(), JSONObject.toJSONString(event));
       }
   }
   ```

3. 在event包下，创建EventConsumer类

   ```java
   @Component
   public class EventConsumer implements CommunityConstant {
   
       private static final Logger logger = LoggerFactory.getLogger(EventConsumer.class);
   
       @Autowired
       private MessageService messageService;
   
       @KafkaListener(topics = {TOPIC_COMMENT,TOPIC_LIKE,TOPIC_FOLLOW})
       public void handleCommentMessage(ConsumerRecord record){
           if(record == null || record.value() == null){
               logger.error("消息的内容为空！");
               return;
           }
   
           Event event = JSONObject.parseObject(record.value().toString(),Event.class);
           if(event == null){
               logger.error("消息的格式不正确！");
               return;
           }
   
           //封装message对象，将它存入数据库
           Message message = new Message();
           message.setFromId(SYSTEM_USER_ID);
           message.setToId(event.getEntityUserId());
           message.setConversationId(event.getTopic());
           message.setCreateTime(new Date());
   
           //封装content数据
           Map<String,Object> content = new HashMap<>();
           content.put("userId",event.getUserId());
           content.put("entityType",event.getEntityType());
           content.put("entityId",event.getEntityId());
   
           if(!event.getData().isEmpty()){
               for (Map.Entry<String, Object> entry : event.getData().entrySet()) {
                   content.put(entry.getKey(), entry.getValue());
               }
           }
           message.setContent(new JSONObject(content).toString());//将map转为json字符串，存入数据库
   
           messageService.addMessage(message);
       }
   }
   ```

4. 在CommentController类中，触发评论事件

   ```java
   @Controller
   @RequestMapping(path = "/comment")
   public class CommentController implements CommunityConstant {
   
       @Autowired
       private CommentService commentService;
   
       @Autowired
       private HostHolder hostHolder;
   
       @Autowired
       private DiscussPostService discussPostService;
   
       @Autowired
       private EventProducer eventProducer;
   
       @RequestMapping(path = "/add/{discussPostId}",method = RequestMethod.POST)
       public String addComment(@PathVariable("discussPostId") int discussPostId, Comment comment){
           comment.setStatus(0);
           comment.setUserId(hostHolder.getUser().getId());
           comment.setCreateTime(new Date());
           commentService.addComment(comment);
   
           //触发评论事件
           Event event = new Event()
                   .setUserId(hostHolder.getUser().getId())
                   .setEntityType(comment.getEntityType())
                   .setEntityId(comment.getEntityId())
                   .setTopic(TOPIC_COMMENT)
                   .setData("postId",discussPostId);
           //根据评论不同的实体类型，设置相应的实体的作者
           if(comment.getEntityType() == ENTITY_TYPE_POST){
               DiscussPost target = discussPostService.findDiscussPostById(comment.getEntityId());
               event.setEntityUserId(target.getUserId());
           }else if(comment.getEntityType() == ENTITY_TYPE_COMMENT){
               Comment target = commentService.findCommentById(comment.getEntityId());
               event.setEntityUserId(target.getUserId());
           }
   
           eventProducer.fireEvent(event);
   
           return "redirect:/discuss/detail/" + discussPostId;
       }
   
   }
   ```

5. 在LikeController类中，触发点赞事件

   ```java
   @Controller
   public class LikeController implements CommunityConstant {
   
       @Autowired
       private LikeService likeService;
   
       @Autowired
       private HostHolder hostHolder;
   
       @Autowired
       private EventProducer eventProducer;
   
       @RequestMapping(path = "/like",method = RequestMethod.POST)
       @ResponseBody
       public String like(int entityType,int entityId,int entityUserId,int postId){
           User user = hostHolder.getUser();
   
           //点赞
           likeService.like(user.getId(),entityType,entityId,entityUserId);
           //获取点赞的数量
           long likeCount = likeService.findEntityLikeCount(entityType, entityId);
           //获取用户对该实体的点赞状态
           int likeStatus = likeService.findEntityLikeStatus(user.getId(), entityType, entityId);
           //封装likeCount、likeStatus到map中，返回给页面
           Map<String,Object> map = new HashMap<>();
           map.put("likeCount",likeCount);
           map.put("likeStatus",likeStatus);
   
           //触发点赞事件
           if(likeStatus == 1){
               Event event = new Event()
                       .setTopic(TOPIC_LIKE)
                       .setUserId(hostHolder.getUser().getId())
                       .setEntityType(entityType)
                       .setEntityId(entityId)
                       .setEntityUserId(entityUserId)
                       .setData("postId",postId);
   
               eventProducer.fireEvent(event);
           }
   
           return CommunityUtil.getJSONString(0,null,map);
   
       }
   }
   ```

6. 在FollowController类中，触发关注事件

   ```java
   @Controller
   public class FollowController implements CommunityConstant{
   
       @Autowired
       private FollowService followService;
   
       @Autowired
       private HostHolder hostHolder;
   
       @Autowired
       private UserService userService;
   
       @Autowired
       private EventProducer eventProducer;
   
   
       @RequestMapping(path = "/follow",method = RequestMethod.POST)
       @ResponseBody
       public String follow(int entityType,int entityId){
           User user = hostHolder.getUser();
           followService.follow(user.getId(), entityType,entityId);
   
           //触发关注事件
           Event event = new Event()
                   .setTopic(TOPIC_FOLLOW)
                   .setUserId(hostHolder.getUser().getId())
                   .setEntityType(entityType)
                   .setEntityId(entityId)
                   .setEntityId(entityId);
   
           eventProducer.fireEvent(event);
           return CommunityUtil.getJSONString(0,"已关注！");
       }
   }
   ```

## 显示系统通知

### 系统通知列表

#### 1.数据访问层Dao

1. 在dao包下的MessageMapper接口中，声明三个方法

   ```java
   //查询某个主题下最新的通知
   Message selectLatestNotice(int userId,String topic);
   
   //查询某个主题所包含的全部通知的数量
   int selectNoticeCount(int userId,String topic);
   
   //查询未读消息的数量
   int selectNoticeUnreadCount(int userId,String topic);
   ```

2. 在resources.mapper包下的message-mapper.xml中，实现1中的三个方法

   ```xml
   <select id="selectLatestNotice" resultType="Message">
       select <include refid="selectFields"></include>
       from message
       where id in (
       select max(id) from message
       where status != 2
       and from_id = 1
       and to_id = #{userId}
       and conversation_id = #{topic}
       )
   </select>
   
   <select id="selectNoticeCount" resultType="int">
       select count(id) from message
       where status != 2
       and from_id = 1
       and to_id = #{userId}
       and conversation_id = #{topic}
   </select>
   
   <select id="selectNoticeUnreadCount" resultType="int">
       select count(id) from message
       where status = 0
       and from_id = 1
       and to_id = #{userId}
       <if test="topic!=null">
           and conversation_id = #{topic}
       </if>
   </select>
   ```

#### 2.业务层Service

在service包下的MessageService类中，创建三个方法，调用数据访问层的对应方法

```JAVA
//查询某个主题下最新的通知
public Message findLatestNotice(int userId,String topic){
    return messageMapper.selectLatestNotice(userId,topic);
}

//查询某个主题所包含的全部通知的数量
public int findNoticeCount(int userId,String topic){
    return messageMapper.selectNoticeCount(userId, topic);
}

//查询未读通知的数量
public int findNoticeUnreadCount(int userId,String topic){
    return messageMapper.selectNoticeUnreadCount(userId,topic);
}
```

#### 3.表现层Controller

1. 在controller包下的MessageController类中，创建方法getNoticeList()，获取系统通知列表

   ```java
   //系统通知列表
   @RequestMapping(path = "/notice/list",method = RequestMethod.GET)
   public String getNoticeList(Model model){
       User user = hostHolder.getUser();
   
       //查询评论类通知
       Map<String,Object> messageVO = new HashMap<>();//存储聚合后的数据
       Message message = messageService.findLatestNotice(user.getId(), TOPIC_COMMENT);//最新一条通知
       if(message != null){
           messageVO.put("message",message);
   
           String content = HtmlUtils.htmlUnescape(message.getContent());//反转义content内容
           Map<String,Object> map = JSONObject.parseObject(content, HashMap.class);//将字符串转成hashmap，方便取值
   
           messageVO.put("user",userService.findUserById((Integer) map.get("userId")));//事件的触发者
           messageVO.put("entityType",map.get("entityType"));
           messageVO.put("entityId",map.get("entityId"));
           messageVO.put("postId",map.get("postId"));
   
           int count = messageService.findNoticeCount(user.getId(), TOPIC_COMMENT);
           messageVO.put("count",count);
   
           int unreadCount = messageService.findNoticeUnreadCount(user.getId(), TOPIC_COMMENT);
           messageVO.put("unreadCount",unreadCount);
       }
       model.addAttribute("commentNotice",messageVO);
   
       //查询点赞类通知
       messageVO = new HashMap<>();//存储聚合后的数据
       message = messageService.findLatestNotice(user.getId(), TOPIC_LIKE);//最新一条通知
       messageVO.put("message",message);
       if(message != null){
           String content = HtmlUtils.htmlUnescape(message.getContent());//反转义content内容
           HashMap map = JSONObject.parseObject(content, HashMap.class);//将字符串转成hashmap，方便取值
           messageVO.put("user",userService.findUserById((Integer) map.get("userId")));//事件的触发者
           messageVO.put("entityType",map.get("entityType"));
           messageVO.put("entityId",map.get("entityId"));
           messageVO.put("postId",map.get("postId"));
   
           int count = messageService.findNoticeCount(user.getId(), TOPIC_LIKE);
           messageVO.put("count",count);
   
           int unreadCount = messageService.findNoticeUnreadCount(user.getId(), TOPIC_LIKE);
           messageVO.put("unreadCount",unreadCount);
       }
       model.addAttribute("likeNotice",messageVO);
   
       //查询关注类通知
       messageVO = new HashMap<>();//存储聚合后的数据
       message = messageService.findLatestNotice(user.getId(), TOPIC_FOLLOW);//最新一条通知
       messageVO.put("message",message);
       if(message != null){
           String content = HtmlUtils.htmlUnescape(message.getContent());//反转义content内容
           HashMap map = JSONObject.parseObject(content, HashMap.class);//将字符串转成hashmap，方便取值
           messageVO.put("user",userService.findUserById((Integer) map.get("userId")));//事件的触发者
           messageVO.put("entityType",map.get("entityType"));
           messageVO.put("entityId",map.get("entityId"));
   
           int count = messageService.findNoticeCount(user.getId(), TOPIC_FOLLOW);
           messageVO.put("count",count);
   
           int unreadCount = messageService.findNoticeUnreadCount(user.getId(), TOPIC_FOLLOW);
           messageVO.put("unreadCount",unreadCount);
       }
       model.addAttribute("followNotice",messageVO);
   
       //查询未读消息数量
       int letterUnreadCount = messageService.findLetterUnreadCount(user.getId(), null);
       model.addAttribute("letterUnreadCount",letterUnreadCount);
       int noticeUnreadCount = messageService.findNoticeUnreadCount(user.getId(),null);
       model.addAttribute("noticeUnreadCount",noticeUnreadCount);
   
       return "/site/notice";
   }
   ```

2. 修改letter.html和notice.html页面中“系统通知”的链接为"@{/notice/list}"

3. 修改notice.html页面，动态生成相应的数据

### 系统通知详情

#### 1.数据访问层

1. 在dao包下的MessageMapper接口中，声明方法

   ```java
   //查询某个主题包含的全部通知
   List<Message> selectNotices(int userId,String topic,int offset,int limit);
   ```

2. 在resources.mapper包下的message-mapper.xml中，实现该方法

   ```xml
   <select id="selectNotices" resultType="Message">
       select <include refid="selectFields"></include>
       from message
       where status != 2
       and from_id = 1
       and to_id = #{userId}
       and conversation_id = #{topic}
       order by create_time desc
       limit #{offset},#{limit}
   </select>
   ```

#### 2.业务层

在service包下的MessageService类中，创建方法findNotices()，调用数据访问层的相应方法

```java
//查询某个主题下的全部通知
public List<Message> findNotices(int userId,String topic,int offset,int limit){
    return messageMapper.selectNotices(userId, topic, offset, limit);
}
```

#### 3.表现层

1. 在controller包下的MessageController类中，创建方法getNoticeDetail()，处理“/notice/detail/{topic}”请求

   ```java
   @RequestMapping(path = "/notice/detail/{topic}",method = RequestMethod.GET)
   public String getNoticeDetail(@PathVariable("topic") String topic,Page page,Model model){
       User user = hostHolder.getUser();
   
       page.setLimit(5);
       page.setPath("/notice/detail/"+topic);
       page.setRows(messageService.findNoticeCount(user.getId(), topic));
   
       List<Message> noticeList = messageService.findNotices(user.getId(), topic, page.getOffset(), page.getLimit());
       List<Map<String,Object>> noticeVoList = new ArrayList<>();
       if(noticeList!=null){
           for (Message notice : noticeList) {
               Map<String,Object> map = new HashMap<>();
               //通知
               map.put("notice",notice);
               //内容
               String content = HtmlUtils.htmlUnescape(notice.getContent());
               Map<String,Object> data = JSONObject.parseObject(content,HashMap.class);
               map.put("user",userService.findUserById((Integer) data.get("userId")));
               map.put("entityType",data.get("entityType"));
               map.put("entityId",data.get("entityId"));
               map.put("postId",data.get("postId"));
   
               map.put("frommUser",userService.findUserById(notice.getFromId()));
   
               noticeVoList.add(map);
           }
       }
       model.addAttribute("notices",noticeVoList);
   
       //设置已读
       List<Integer> ids = getLetterIds(noticeList);
       if(!ids.isEmpty()){
           messageService.readMessage(ids);
       }
   
       return "/site/notice-detail";
   }
   ```

2. 修改notice.html页面中评论、点赞、关注类通知的链接为"@{/notice/detail/comment}"、"@{/notice/detail/like}"、"@{/notice/detail/follow}"

3. 修改notice-detail.html页面

   1. 修改“返回”链接为"/notice/list"
   2. 修改通知列表，动态展示数据

4. 在controller.interceptor包下，创建MessageInterceptor类，实现HandlerInterceptor接口，重写post方法，获得总的未读消息数，并将其装配到modelAndView对象上

   ```java
   @Component
   public class MessageInterceptor implements HandlerInterceptor {
   
       @Autowired
       private HostHolder hostHolder;
   
       @Autowired
       private MessageService messageService;
   
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           User user = hostHolder.getUser();
           if(user != null && modelAndView != null){
               int letterUnreadCount = messageService.findLetterUnreadCount(user.getId(), null);
               int noticeUnreadCount = messageService.findNoticeUnreadCount(user.getId(), null);
               modelAndView.addObject("allUnreadCount",letterUnreadCount + noticeUnreadCount);
           }
       }
   }
   ```

5. 在config包下的WebMvcConfig类中，配置拦截器messageInterceptor

   ```java
   @Configuration
   public class WebMvcConfig implements WebMvcConfigurer {
   
       @Autowired
       private MessageInterceptor messageInterceptor;
   
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
   
           registry.addInterceptor(messageInterceptor)
                   .excludePathPatterns("/**/*.css","/**/*.js","/**/*.png","/**/&.jpg","/**/*.jpeg");
       }
   }
   ```

6. 在index.html模板，动态显示总的未读消息数为modelAndView中携带的allUnreadCount

## ElasticSearch

### 入门

#### 安装

1. 吸杂elasticsearch-6.4.3.zip，解压到项目文件夹elasticsearch-6.4.3

2. 配置elasticsearch-6.4.3/config/elasticsearch.yml

   ```yml
   cluster.name: nowcoder
   path.data: d:\develop\workSpace\elasticsearch-6.4.3\data
   path.logs: d:\develop\workSpace\elasticsearch-6.4.3\logs
   ```

3. 配置环境变量，在path中添加D:\develop\elasticsearch-6.4.3\bin

4. 下载ElasticSearch中文分词插件elasticsearch-analysis-ik-6.4.3.zip，**解压到D:\develop\elasticsearch-6.4.3\plugins\ik文件夹**

5. 安装模拟web客户端的工具postman.exe

#### 使用

1. 启动ElasticSearch服务器：在D:\develop\elasticsearch-6.4.3\bin文件夹下，双击elasticsearch.bat

2. win+R + cmd 打开命令行，作为前端，访问ElasticSearch服务器：

   1. ```
      查看集群健康状况：curl -X GET "localhost:9200/_cat/health?v"
      ```

   2. ```
      查看集群的节点：curl -X GET "localhost:9200/_cat/nodes?v"
      ```

   3. ```
      查看索引：curl -X GET "localhost:9200/_cat/indices?v"
      ```

   4. ```
      创建索引：curl -X PUT "localhost:9200/索引名"
      ```

   5. ```
      删除索引：curl -X DELETE "localhost:9200/索引名"
      ```

3. 用postman作为web客户端，访问ElasticSearch服务器：

   1. GET  "localhost:9200/_cat/indices?v"

   2. PUT  "localhost:9200/索引名"

   3. DELETE "localhost:9200/索引名"

   4. ```
      插入数据：PUT localhost:9200/索引名/_doc/id
      		请求体Body-Row-Json-在输入框中输入json类型数据
      ```

   5. ```
      获取数据：GET localhost:9200/索引名/_doc/id
      ```

   6. ```
      修改数据：PUT localhost:9200/索引名/_doc/id
      		请求体Body-Row-Json-在输入框中输入json类型数据
      ```

   7. ```
      删除数据：DELETE localhost:9200/索引名/_doc/id
      ```

   8. ```
      搜索数据：GET localhost:9200/test/_search?q=title:互联网
      		GET localhost:9200/test/_search?q=content:互联网
      搜索数据（多匹配）：GET localhost:9200/test/_search
      				请求体Body-Row-Json-在输入框中输入json类型数据
                      {
                          "query":{
                              "multi_match":{
                                  "query":"互联网",
                                  "fields":["title","content"]
                              }
                          }
                      }
      ```

## Spring整合ElasticSearch

1. 在pom.xml文件中引入依赖 spring-boot-starter-data-elasticsearch

2. 在config包下创建Esconfig类，构造Bean，设置集群的节点

   ```java
   @Configuration
   public class EsConfig extends AbstractElasticsearchConfiguration {
   
       @Override
       @Bean
       public RestHighLevelClient elasticsearchClient() {
   
           final ClientConfiguration clientConfiguration = ClientConfiguration.builder()
                   .connectedTo("localhost:9200")
                   .build();
   
           return RestClients.create(clientConfiguration).rest();
       }
   }
   ```

3. 解决netty冲突（redis和elasticsearch启动时都会依赖netty，redis先启动netty，elasticsearch后启动会爆错）

   在CommunityApplication主配置类中，增加如下代码，解决netty冲突

   ```java
   //解决netty启动冲突问题
   //see Netty4Utils.setAvailableProcessors(int availableProcessors)
   @PostConstruct
   public void init(){
      System.setProperty("es.set.netty.runtime.available.processors","false");
   }
   ```

4. 让entity包下的Discusspost类与ElasticSearch中的索引对应、Discusspost类中的属性与ElasticSearch中的字段一一对应

   ```java
   @Document(indexName = "discusspost")
   public class DiscussPost {
       @Id
       private int id;
   
       @Field(type = FieldType.Integer)
       private int userId;
   
   	//将数据存入elasticSearch中使用analyzer分词器，尽可能多的分词
       //从elasticSearch中搜索数据时使用searchAnalyzer分词器，尽可能聪明的分词
       @Field(type = FieldType.Text,analyzer = "ik_max_word",searchAnalyzer = "ik_smart")
       private String title;
   
       @Field(type = FieldType.Text,analyzer = "ik_max_word",searchAnalyzer = "ik_smart")
       private String content;
   
       @Field(type = FieldType.Integer)
       private int type;
   
       @Field(type = FieldType.Integer)
       private int status;
   
       @Field(type = FieldType.Date)
       private Date createTime;
   
       @Field(type = FieldType.Integer)
       private int commentCount;
   
       @Field(type = FieldType.Double)
       private double score;
   }
   ```

5. 在dao包下，创建子包elasticsearch，在该子包创建接口DiscussPostRepository，继承ElasticsearchRepository，Spring底层会自动实现DiscussPostRepository的增删改查方法

   ```java
   @Repository
   public interface DiscussPostRepository extends ElasticsearchRepository<DiscussPost,Integer> {
   }
   ```

6. 在test包下创建ElasticsearchTests类，测试从mybatis数据库向Elasticsearch中增删改查数据

   > 进行测试时，记得启动elasticsearch服务器：在D:\develop\elasticsearch-6.4.3\bin文件夹下，双击elasticsearch.bat

## Spring Security

### 入门

1. 在pom.xml中引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
   ```

2. 在entity包下的User类中：让User类实现UserDetails接口，重写UserDetails接口的四个方

   ```java
   public class User implements UserDetails {
   
       private int id;
       private String username;
       private String password;
       private String salt;
       private String email;
       private int type;
       private int status;
       private String activationCode;
       private String headerUrl;
       private Date createTime;
    
       //true:账号未过期
       @Override
       public boolean isAccountNonExpired() {
           return true;
       }
   
       //true:账号未锁定
       @Override
       public boolean isAccountNonLocked() {
           return true;
       }
   
       //true:凭证未过期
       @Override
       public boolean isCredentialsNonExpired() {
           return true;
       }
   
       //true:账号可用
       @Override
       public boolean isEnabled() {
           return true;
       }
   
       @Override
       public Collection<? extends GrantedAuthority> getAuthorities() {
           List<GrantedAuthority> list = new ArrayList<>();
           list.add(new GrantedAuthority() {
               @Override
               public String getAuthority() {
                   switch (type){
                       case 1:
                           return "ADMIN";
                       default:
                           return "USER";
                   }
               }
           });
   
           return list;
       }
   }
   ```

3. 在service包下的UserService类中：让UserService类实现UserDetailsService接口，重写一个方法

   ```java
   @Service
   public class UserService implements UserDetailsService {
   
       @Autowired
       private UserMapper userMapper;
   
       public User findUserByName(String username) {
           return userMapper.selectByName(username);
       }
       
       @Override
       public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
           return this.findUserByName(username);
       }
   }
   ```

4. 在config包下，创建SecurityConfig类，继承WebSecurityConfigurerAdapter，重写configure(WebSecurity web)、configure(AuthenticationManagerBuilder auth)、configure(HttpSecurity http)三个方法，实现认证、授权的业务逻辑

   ```java
   @Configuration
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
   
       @Autowired
       private UserService userService;
   
       @Override
       public void configure(WebSecurity web) throws Exception {
           // 忽略静态资源的访问
           web.ignoring().antMatchers("/resources/**");
       }
   
       // AuthenticationManager: 认证的核心接口.
       // AuthenticationManagerBuilder: 用于构建AuthenticationManager对象的工具.
       // ProviderManager: AuthenticationManager接口的默认实现类.
       @Override
       protected void configure(AuthenticationManagerBuilder auth) throws Exception {
           // 内置的认证规则
           // auth.userDetailsService(userService).passwordEncoder(new Pbkdf2PasswordEncoder("12345"));
   
           // 自定义认证规则
           // AuthenticationProvider: ProviderManager持有一组AuthenticationProvider,每个AuthenticationProvider负责一种认证.
           // 委托模式: ProviderManager将认证委托给AuthenticationProvider.
           auth.authenticationProvider(new AuthenticationProvider() {
               // Authentication: 用于封装认证信息的接口,不同的实现类代表不同类型的认证信息.
               @Override
               public Authentication authenticate(Authentication authentication) throws AuthenticationException {
                   String username = authentication.getName();
                   String password = (String) authentication.getCredentials();
   
                   User user = userService.findUserByName(username);
                   if (user == null) {
                       throw new UsernameNotFoundException("账号不存在!");
                   }
   
                   password = CommunityUtil.md5(password + user.getSalt());
                   if (!user.getPassword().equals(password)) {
                       throw new BadCredentialsException("密码不正确!");
                   }
   
                   // principal: 主要信息; credentials: 证书; authorities: 权限;
                   return new UsernamePasswordAuthenticationToken(user, user.getPassword(), user.getAuthorities());
               }
   
               // 当前的AuthenticationProvider支持哪种类型的认证.
               @Override
               public boolean supports(Class<?> aClass) {
                   // UsernamePasswordAuthenticationToken: Authentication接口的常用的实现类.
                   return UsernamePasswordAuthenticationToken.class.equals(aClass);
               }
           });
       }
   
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           // 登录相关配置
           http.formLogin()
                   .loginPage("/loginpage")
                   .loginProcessingUrl("/login")
                   .successHandler(new AuthenticationSuccessHandler() {
                       @Override
                       public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
                           response.sendRedirect(request.getContextPath() + "/index");
                       }
                   })
                   .failureHandler(new AuthenticationFailureHandler() {
                       @Override
                       public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException e) throws IOException, ServletException {
                           request.setAttribute("error", e.getMessage());
                           request.getRequestDispatcher("/loginpage").forward(request, response);
                       }
                   });
   
           // 退出相关配置
           http.logout()
                   .logoutUrl("/logout")
                   .logoutSuccessHandler(new LogoutSuccessHandler() {
                       @Override
                       public void onLogoutSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
                           response.sendRedirect(request.getContextPath() + "/index");
                       }
                   });
   
           // 授权配置
           http.authorizeRequests()
                   .antMatchers("/letter").hasAnyAuthority("USER", "ADMIN")
                   .antMatchers("/admin").hasAnyAuthority("ADMIN")
                   .and().exceptionHandling().accessDeniedPage("/denied");
   
           // 增加Filter,处理验证码
           http.addFilterBefore(new Filter() {
               @Override
               public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
                   HttpServletRequest request = (HttpServletRequest) servletRequest;
                   HttpServletResponse response = (HttpServletResponse) servletResponse;
                   if (request.getServletPath().equals("/login")) {
                       String verifyCode = request.getParameter("verifyCode");
                       if (verifyCode == null || !verifyCode.equalsIgnoreCase("1234")) {
                           request.setAttribute("error", "验证码错误!");
                           request.getRequestDispatcher("/loginpage").forward(request, response);
                           return;
                       }
                   }
                   // 让请求继续向下执行.
                   filterChain.doFilter(request, response);
               }
           }, UsernamePasswordAuthenticationFilter.class);
   
           // 记住我
           http.rememberMe()
                   .tokenRepository(new InMemoryTokenRepositoryImpl())
                   .tokenValiditySeconds(3600 * 24)
                   .userDetailsService(userService);
   
       }
   }
   ```


5. 在controller包下的HomeController类中，增加getDeniedPage()方法，用于

处理“因权限不符拒绝访问时跳转到404页面”的请求

```java
//拒绝访问时的提示页面
@RequestMapping(path = "/denied", method = RequestMethod.GET)
public String getDeniedPage() {
   return "/error/404";
}
```

6. 在login.html页面中，修改表单提交的内容

   ```html
   <form method="post" th:action="@{/login}">
       <p style="color:red;" th:text="${error}">
           <!--提示信息-->
       </p>
       <p>
           账号：<input type="text" name="username" th:value="${param.username}">
       </p>
       <p>
           密码：<input type="password" name="password" th:value="${param.password}">
       </p>
       <p>
           验证码：<input type="text" >
       </p>
       <p>
           <input type="submit" value="登录">
       </p>
   </form>
   
   ```

7. 在index.html页面中，修改退出的提交方式

   ```html
   <ul>
       <li><a th:href="@{/discuss}">帖子详情</a></li>
       <li><a th:href="@{/letter}">私信列表</a></li>
       <li><a th:href="@{/loginpage}">登录</a></li>
       <!--<li><a th:href="@{/loginpage}">退出</a></li>-->
       <!--Security要求退出必须是post请求方式提交-->
       <li>
           <form method="post" th:action="@{/logout}">
               <!--这个href并不是要直接跳转到某个链接，或者跳转到某个controller方法处理的请求路径
               ，而是要实现对这个表单的提交操作，提交之后action会跳转到处理“/logout”请求路径的controller1方法-->
               <!--document:当前页面;forms:当前页面的所有表单-->
               <a href="javascript:document.forms[0].submit();">退出</a>
           </form>
       </li>
   </ul>
   ```

8. 希望在index.html页面显示针对性的欢迎信息——“欢迎你,XXX”

   1. 在controller包下的HomeController类的getIndexPage()方法中，获取认证成功后的信息，封装到model中，传递给index.html页面

      ```java
      @Controller
      public class HomeController {
      
          @RequestMapping(path = "/index", method = RequestMethod.GET)
          public String getIndexPage(Model model) {
              //认证成功后，结果会通过SecurityContextHolder存入SecurityContext中
              Object obj = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
              if(obj instanceof User){
                  model.addAttribute("loginUser",obj);
              }
              return "/index";
          }
      }
      ```

   2. 在index.html页面显示欢迎信息

      ```html
      <!--欢迎信息-->
      <p th:if="${loginUser!=null}">
          欢迎你， <span th:text="${loginUser.username}"></span>!
      </p>
      ```

9. 处理验证码

   1. 在config包下的SecurityConfig类的configure(HttpSecurity http)方法中增加处理验证码的Filter

      ```java
      //增加Filter，处理验证码
      http.addFilterBefore(new Filter() {
          @Override
          public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
              HttpServletRequest request = (HttpServletRequest) servletRequest;
              HttpServletResponse response = (HttpServletResponse) servletResponse;
              if(request.getServletPath().equals("/login")){
                  String verifyCode = request.getParameter("verifyCode");
                  if(verifyCode == null || !verifyCode.equals("1234")){
                      request.setAttribute("error","验证码错误！");
                      request.getRequestDispatcher("/loginpage").forward(request,response);
                      return;
                  }
              }
              //让请求继续向下执行
              filterChain.doFilter(request,response);
          }
          //处理验证码的Filter要在处理用户名密码的Filter之前
      }, UsernamePasswordAuthenticationFilter.class);
      
      //记住我（顺便配一下）
       http.rememberMe()
                      .tokenRepository(new InMemoryTokenRepositoryImpl())
                      .tokenValiditySeconds(3600 * 24)
                      .userDetailsService(userService);
      ```

   2. 在login.html页面，完善验证码、记住我的相关修改

      ```html
      <form method="post" th:action="@{/login}"> 
          <p>
              验证码：<input type="text" name="verifyCode"> <i>1234</i>
          </p>
          <p>
              <input type="checkbox" name="remember-me">记住我
          </p>
          <p>
              <input type="submit" value="登录">
          </p>
      </form>
      ```

## 权限控制

### 实现功能

![image-20211219185346146](C:\Users\鲸鱼\AppData\Roaming\Typora\typora-user-images\image-20211219185346146.png)

### 实现步骤

1. 在pom.xml中引入依赖

   ```xml
   <dependency> <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
   ```

2. 在config包下的WebMvcConfig类中，注释掉loginRequiredInterceptor相关代码，废弃掉原有的拦截器

3. 在util包下的常量接口CommunityConstant中，定义代表不同权限类型的常量，便于之后引用

   ```java
   public interface CommunityConstant {
   
       /**
        * 权限：普通用户
        */
       String AUTHORITY_USER = "user";
   
       /**
        * 权限：管理员
        */
       String AUTHORITY_ADMIN = "admin";
   
       /**
        * 权限：版主
        */
       String AUTHORITY_MODERATOR = "moderator";
   }
   ```

4. 在config包下，创建配置类SecurityConfig，继承WebSecurityConfigurerAdapter，实现常量接口CommunityConstant，在SecurityConfig类内部重写3个configure方法

   ```java
   @Configuration
   public class SecurityConfig extends WebSecurityConfigurerAdapter implements CommunityConstant {
   
       @Override
       public void configure(WebSecurity web) throws Exception {
           web.ignoring().antMatchers("/resources/**");
       }
   
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           //授权
           http.authorizeRequests()
                   .antMatchers(
                           "/user/setting",
                           "/user/upload",
                           "/discuss/add",
                           "/comment/add/**",
                           "/letter/**",
                           "/notice/**",
                           "/like",
                           "/follow",
                           "/unfollow"
                   )
                   .hasAnyAuthority(
                           AUTHORITY_USER,
                           AUTHORITY_ADMIN,
                           AUTHORITY_MODERATOR
                   )
                   .anyRequest().permitAll();
   
           //权限不够时的处理
           http.exceptionHandling()
                   //因未登录而权限不够时的处理
                   .authenticationEntryPoint(new AuthenticationEntryPoint() {
                       @Override
                       public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException e) throws IOException, ServletException {
                           String xRequestedWith = request.getHeader("x-requested-with");
                           if("XMLHttpRequest".equals(xRequestedWith)){
                               //如果是异步请求，浏览器期望服务器返回json字符串
                               response.setContentType("application/plain;charset=utf-8");
                               PrintWriter writer = response.getWriter();
                               writer.write(CommunityUtil.getJSONString(403,"你还没有登录哦！"));
                           }else {
                               //如果是同步请求，浏览器期望服务器返回html页面
                               response.sendRedirect(request.getContextPath() + "/login");
                           }
                       }
                   })
                   //已登录但权限不够时的处理
                   .accessDeniedHandler(new AccessDeniedHandler() {
                       @Override
                       public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException e) throws IOException, ServletException {
                           String xRequestedWith = request.getHeader("x-requested-with");
                           if("XMLHttpRequest".equals(xRequestedWith)){
                               //如果是异步请求，浏览器期望服务器返回json字符串
                               response.setContentType("application/plain;charset=utf-8");
                               PrintWriter writer = response.getWriter();
                               writer.write(CommunityUtil.getJSONString(403,"你没有访问此功能的权限！"));
                           }else {
                               //如果是同步请求，浏览器期望服务器返回html页面
                               response.sendRedirect(request.getContextPath() + "/denied");
                           }
                       }
                   });
   
           //Security底层默认会拦截/logout请求，进行退出处理
           //覆盖它的逻辑，才能执行我们自己写的退出代码
           http.logout().logoutUrl("/securitylogout");
           //"/securitylogout"这个路径并不存在，是善意的欺骗，意为Security只有执行到这个不存在的请求时才去执行Security底层的默认拦截，所以Security底层的默认拦截就不会被执行了，而是执行我们自己写的处理/logout请求的退出代码
           
       }
   ```

5. 在controller包下的HomeController类中，补充  处理“权限不够时的提示页面”这一请求  的方法

   ```java
   public class HomeController implements CommunityConstant {
       //权限不足时的提示页面
       @RequestMapping(path = "/denied",method = RequestMethod.GET)
       public String getDeniedPage(){
           return "/error/404";
       }
   }
   ```

6. 在4中，只重写了2个configure方法，完成了Security授权的逻辑。没重写configure(AuthenticationManagerBuilder auth)这个方法，意在绕过Security认证的逻辑，采用我们自己写的认证逻辑。但虽然绕过了Security认证的逻辑，仍需要把我们自己代码认证的结果保存到SecurityContext中，这样Security才能获取用户的权限，根据重写的configure(HttpSecurity http)这个方法，帮助我们做授权

   1. 在service包下的UserService类中，增加“查询某个用户权限”的方法getAuthoritues(int userId)

      ```java
      @Service
      public class UserService implements CommunityConstant {
          //查询某个用户权限
          public Collection<? extends GrantedAuthority> getAuthorities(int userId){
              User user = this.findUserById(userId);
              List<GrantedAuthority> list = new ArrayList<>();
              list.add(
                      new GrantedAuthority() {
                          @Override
                          public String getAuthority() {
                              switch (user.getType()){
                                  case 1:
                                      return AUTHORITY_ADMIN;
                                  case 2:
                                      return AUTHORITY_MODERATOR;
                                  default:
                                      return AUTHORITY_USER;
                              }
                          }
                      }
              );
              return list;
          }
      }
      ```

   2. 在controller.interceptor包下的LoginInterceptor类下的preHandle方法中，构建用户认证的结果，并存入SecurityContext中，以便于Security进行授权

      ```java
      @Component
      public class LoginInterceptor implements HandlerInterceptor {
      
          @Autowired
          private UserService userService;
      
          @Autowired
          private HostHolder hostHolder;
      
          @Override
          public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
              //获取cookie中的ticket
              String ticket = CookieUtil.getValue(request, "ticket");
              //根据ticket获取相应的loginTicket凭证
              LoginTicket loginTicket = userService.getLoginTicket(ticket);
              //从loginTicket凭证中获取userId，进一步得到用户对象user
              if(loginTicket != null && loginTicket.getStatus() == 0
                      && loginTicket.getExpired().after(new Date())){
                  int userId = loginTicket.getUserId();
                  User user = userService.findUserById(userId);
                  //在本次请求中持有该用户
                  hostHolder.setUser(user);
                  //构建用户认证的结果，并存入SecurityContext，以便于Security进行授权
                  Authentication authentication = new UsernamePasswordAuthenticationToken(
                          user,user.getPassword(), userService.getAuthorities(user.getId()));
                  SecurityContextHolder.setContext(new SecurityContextImpl(authentication));
              }
      
              return true;
          }
      }
      ```

   3. 在controller.interceptor包下的LoginInterceptor类下的afterHandle方法中，对SecurityContextHolder进行清理

      ```java
      @Component
          public class LoginInterceptor implements HandlerInterceptor {
          @Override
          public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
              hostHolder.clear();
              SecurityContextHolder.clearContext();
      	}
      }
      ```

   4. 在controller包下的LoginController类下的logout方法中，执行退出后也要清空SecurityContextHolder中的Context

      ```java
      @Controller
      public class LoginController implements CommunityConstant {
      	@RequestMapping(path = "/logout",method = RequestMethod.GET)
          public String logout(@CookieValue("ticket") String ticket){
              userService.logout(ticket);
              SecurityContextHolder.clearContext();
              return "redirect:/login";
          }
      }
      ```

7. 防止CSRF攻击：同步请求Security已经自动帮我们完成了，我们只需要定义异步请求如何防止CSRF攻击

   1. 在index.html页面的<head>标签里，生成CSRF令牌

      ```html
      <head>
         <meta charset="utf-8">
         <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
         
         <!--访问该页面时，在此处生成CSRF令牌-->
         <meta name="_csrf" th:content="${_csrf.token}">
         <meta name="_csrf_header" th:content="${_csrf.headerName}">
         
         <link rel="icon" href="https://static.nowcoder.com/images/logo_87_87.png"/>
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" crossorigin="anonymous">
         <link rel="stylesheet" th:href="@{/css/global.css}" />
         <title>牛客网-首页</title>
      </head>
      ```

   2. 在index.js中，在发送AJAX请求之前，将CSRF令牌设置到请求的消息

      ```js
      $(function(){
         $("#publishBtn").click(publish);
      });
      
      function publish() {
         $("#publishModal").modal("hide");
      
         //发送AJAX请求之前，将CSRF令牌设置到请求的消息头中
         var token = $("meta[name='_csrf']").attr("content");
         var header = $("meta[name='_csrf_header']").attr("content");
         $(document).ajaxSend(function (e, xhr, options) {
            xhr.setRequestHeader(header,token);
         })
      }
      ```
