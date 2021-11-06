#### 0.前言

SpringBoot的思想 - 约定大于配置

1. 开发人员仅需规定应用中不符合约定的部分
2. 在没有规定配置的地方，采用默认配置，以力求最简配置为核心思想

SpringBoot的约定实例 - 

1. Maven的目录结构

2. spring boot默认的配置文件必须是，也只能是application.命名的**yml**文件或者**properties**文件，**且唯一**

3. application.yml中默认属性。

   数据库连接信息必须是以spring: datasource: 为前缀；

   多环境配置。该属性可以根据运行环境自动读取不同的配置文件；

   端口号、请求路径等

#### 1. 入门

1. 使用Maven安装

   ```xml
   <!-- Inherit defaults from Spring Boot -->
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>1.5.9.RELEASE</version>
   </parent>
   
   <!-- Add typical dependencies for a web application -->
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
   </dependencies>
   
   <!-- Package as an executable jar -->
   <build>
       <plugins>
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
           </plugin>
       </plugins>
   </build>
   ```

2. 第一个Spring boot应用

   @EnableAutoConfiguration

   ```java
   import org.springframework.boot.*;
   import org.springframework.boot.autoconfigure.*;
   import org.springframework.web.bind.annotation.*;
   
   @RestController
   @EnableAutoConfiguration
   public class Example {
   
       @RequestMapping("/")
       String home() {
           return "Hello World!";
       }
   
       public static void main(String[] args) throws Exception {
           SpringApplication.run(Example.class, args);
       }
   
   }
   ```

   

#### 2. 使用Spring Boot

1. Starter

   ```
   官方的所有 starter 都遵循类似的命名规则：spring-boot-starter-*，
   其中 * 是特定类型的应用。
   spring-boot-starter-json	jackson
   spring-boot-starter-aop		springAOP
   spring-boot-starter-test	junit
   spring-boot-starter-logging	logback
   ```

   pom.xml文件中默认有两个模块：

   spring-boot-starter：核心模块，包括自动配置支持、日志和YAML；

   spring-boot-starter-test：测试模块，包括JUnit、Hamcrest、Mockito。

2. 定位主应用类

   1、Application.java 建议放到跟目录下面,主要用于做一些框架配置

   2、domain目录主要用于实体（Entity）与数据访问层（Repository）

   3、service 层主要是业务类代码

   4、controller 负责页面访问控制

   ```java
   //位于根包下的主类
   @Configuration
   @EnableAutoConfiguration
   @ComponentScan
   public class Application {
   
       public static void main(String[] args) {
           SpringApplication.run(Application.class, args);
       }
   }
   ```

3. SpringBoot配置服务器

   1. spring-boot-starter-web的自动配置
   
      ```xml
      <!--默认使用tomcat,若不想使用tomcat,需要exclusion-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
          <exclusions>
              <exclusion>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-tomcat</artifactId>
              </exclusion>
          </exclusions>
      </dependency>
      
      <!--如导入jetty，替换tomcat-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-jetty</artifactId>
      </dependency>
      ```
   
      
   
4. SpringBoot单元测试

   ```xml
   <!--依赖-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-test</artifactId>
   </dependency>
   ```

   

   ```java
   //保证依赖注入
   @RunWith(SpringRunner.class)
   //功能测试，面向完整业务功能和切面测试中的mock能力
   @SpringBootTest	
   public class HelloWorldControlerTests {
       private MockMvc mvc;
       @Resource
      	private WebApplicationContext webApplicationContext;
       
       @Before
       public void setUp() throws Exception {
               mvc = MockMvcBuilders.standaloneSetup(new HelloWorldController()).build();
   	//mvc =  MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
       }
       @Test
       public void getHello() throws Exception {
           mvc.perform(MockMvcRequestBuilders.get("/hello").                		accept(MediaType.APPLICATION_JSON))         .andExpect(MockMvcResultMatchers.status().isOk())
               .andDo(MockMvcResultHandlers.print())
               .andReturn();
       }
   }
   ```

   注意事项：

   1. maven模板项目自带的junit版本太老，不适用
   2. 测试单元需要与Application启动类同一个包

   新的学习：

   []: https://blog.csdn.net/wo541075754/article/details/88983708

   1. Mock是什么

      模拟对象，以可控方式模拟真实对象的行为。

   2. 为什么使用Mock对象

      如果在单元测试中无法使用真实对象，可采用模拟对象进行替代。

   3. MockMvc

      实现了对Http请求的模拟

      创建方法 - 接口：MockMvcBuilder 

      实现类:StandaloneMockMvcBuilder DefaultMockMvcBuilder

      MockMvcBuilders提供了对应的创建方法

      standaloneSetup方法和webAppContextSetup方法

      在使用时直接调用即可。

   4. Junit4中的常用注解

      ```java
      @Before//初始化方法   对于每一个测试方法都要执行一次（注意与BeforeClass区别，后者是对于所有方法执行一次）
      @After//释放资源  对于每一个测试方法都要执行一次（注意与AfterClass区别，后者是对于所有方法执行一次）
      @Test//测试方法，在这里可以测试期望异常和超时时间 
      @Test(expected=ArithmeticException.class)
      //检查被测方法是否抛出ArithmeticException异常 
      @Ignore//忽略的测试方法 
      @BeforeClass//针对所有测试，只执行一次，且必须为static void 
      @AfterClass//针对所有测试，只执行一次，且必须为static void 
      
      一个JUnit4的单元测试用例执行顺序为： 
      @BeforeClass -> @Before -> @Test -> @After -> @AfterClass; 
      ```

   5. MockMvc的各种方法

      ```java
      mockMvc
      //执行一个请求:MockMvcRequestBuilders
      .perform(MockMvcRequestBuilders.get("/hello")
      //设置返回类型(Response的ContentType)
      .accept(MediaType.APPLICATION_JSON_UTF8_VALUE)
      //添加请求参数
      .param("name", "Tom"))
      //添加执行完成的断言
      .andExpect(MockMvcResultMatchers.status().isOk())
      .andExpect(MockMvcResultMatchers.content().string("Hello Tom!"))
      //添加一个结果处理器
      .andDo(MockMvcResultHandlers.print());
      //完成后返回相应的结果
      .andReturn()
      ```

#### 3.开发环境的调试

使用spring-boot-devtools实现SpringBoot应用热部署

依赖：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-devtools</artifactId>
	<scope>runtime</scope>
	<optional>true</optional>
</dependency>
```

#### 4.Web开发

1. 自定义Filter

   ```java
   //实现 Filter 接口，实现 Filter 方法
   //添加@Configuration 注解，将自定义Filter加入过滤链
   
   @Configuration
   public class WebConfiguration {
       @Bean
       public RemoteIpFilter remoteIpFilter() {
           return new RemoteIpFilter();
       }
   	
       @Bean
       public FilterRegistrationBean testFilterRegistration() {
           FilterRegistrationBean registration = new FilterRegistrationBean();
           registration.setFilter(new MyFilter());
           registration.addUrlPatterns("/*");
           registration.addInitParameter("paramName", "paramValue");
           registration.setName("MyFilter");
           registration.setOrder(1);
           return registration;
       }
   
       public class MyFilter implements Filter {
           @Override
           public void destroy() {
               // TODO Auto-generated method stub
           }
   
           @Override
           public void doFilter(ServletRequest srequest, ServletResponse sresponse, FilterChain filterChain)
               throws IOException, ServletException {
               // TODO Auto-generated method stub
               HttpServletRequest request = (HttpServletRequest) srequest;
               System.out.println("this is MyFilter,url :"+request.getRequestURI());
               //必须调用，将过滤器链传递过去
               filterChain.doFilter(srequest, sresponse);
           }
   
           //Servlet实例化过滤器时调用
           @Override
           public void init(FilterConfig arg0) throws ServletException {
               // TODO Auto-generated method stub
           }
       }
   }
   ```

   1. FilterRegistrationBean实例注册

      ```java
      @Bean //放入SpringIOC容器
      public FilterRegistrationBean testFilterRegistration() {
          //实例，可以使用泛型
          FilterRegistrationBean registration = new FilterRegistrationBean();
          //注册自定义过滤器
          registration.setFilter(new MyFilter());
          //过滤的路径
          registration.addUrlPatterns("/*");
          //添加初始化参数，可以在init()上使用
          registration.addInitParameter("paramName", "paramValue");
          //过滤器名称
          registration.setName("MyFilter");
          //过滤器优先级，1为最顶级
          registration.setOrder(1);
          return registration;
      }
      ```

2. 自定义Properites类

   SpringBoot配置文件约定：

   1. 命名：application.properties

   2. 位置优先级

      ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190218112652603.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWw5OQ==,size_16,color_FFFFFF,t_70)

   ```java
   @Component
   public class NeoProperties {
       @Value("${com.neo.title}")
       private String title;
       @Value("${com.neo.description}")
       private String description;
   
       //省略getter settet方法
   }
   ```

3. 利用JPA进行数据库操作

   1. dependency

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-jpa</artifactId>
      </dependency>
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
      </dependency>
      ```

4. 使用Thymeleaf

5. 使用MyBatis

   注解开发模式

   1. dependency

      ```xml
      <dependency>
          <groupId>org.mybatis.spring.boot</groupId>
          <artifactId>mybatis-spring-boot-starter</artifactId>
          <version>2.0.0</version>
      </dependency>
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
      </dependency>
      ```

   2. application.properties配置

      ```properties
      mybatis.type-aliases-package=com.neo.model
      
      spring.datasource.url=jdbc:mysql://localhost:3306/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
      spring.datasource.username=root
      spring.datasource.password=root
      spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
      ```

      Spring Boot 会自动加载 `spring.datasource.*` 相关配置，

      数据源就会自动注入到 sqlSessionFactory 中，

      sqlSessionFactory 会自动注入到 Mapper 中，

      对了，你一切都不用管了，直接拿起来使用就行了。

   3. Mapper的开发

      启动类添加扫描 @MapperScan

      ```java
      @SpringBootApplication
      @MapperScan("com.neo.mapper")
      public class MybatisAnnotationApplication {
      
          public static void main(String[] args) {
          SpringApplication.run(MybatisAnnotationApplication.class, args);
          }
      }
      ```

      Mapper接口和SQL

      ```java
      public interface UserMapper {
      
          @Select("SELECT * FROM users")
          @Results({
              @Result(property = "userSex",  column = "user_sex", javaType = UserSexEnum.class),
              @Result(property = "nickName", column = "nick_name")
          })
          List<UserEntity> getAll();
      
          @Select("SELECT * FROM users WHERE id = #{id}")
          @Results({
              @Result(property = "userSex",  column = "user_sex", javaType = UserSexEnum.class),
              @Result(property = "nickName", column = "nick_name")
          })
          UserEntity getOne(Long id);
      
          @Insert("INSERT INTO users(userName,passWord,user_sex) VALUES(#{userName}, #{passWord}, #{userSex})")
          void insert(UserEntity user);
      
          @Update("UPDATE users SET userName=#{userName},nick_name=#{nickName} WHERE id =#{id}")
          void update(UserEntity user);
      
          @Delete("DELETE FROM users WHERE id =#{id}")
          void delete(Long id);
      
      }
      ```

   

   xml配置开发模式

   1. application.properties

      ```properties
      mybatis.config-location=classpath:mybatis/mybatis-config.xml
      mybatis.mapper-locations=classpath:mybatis/mapper/*.xml
      ```

   2. 其他按照xml配置即可

      

   ##### springboot整合mybatis总结

   1. application.properties

      ```properties
      #mysql
      spring.datasource.url=jdbc:mysql://localhost:3306/mydb?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
      spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
      spring.datasource.username=root
      spring.datasource.password=449517
      
      #mybatis
      mybatis.config-location=classpath:mybatis-config.xml
      mybatis.mapper-locations=classpath:mapper/*.xml
      mybatis.type-aliases-package=com.yuan.domain
      ```

   2. hikari连接池的使用

      dependency:

      ```xml
      <dependency>
          <groupId>com.zaxxer</groupId>
          <artifactId>HikariCP</artifactId>
          <version>2.6.1</version>
          <scope>compile</scope>
      </dependency>
      ```

      properties:

      ```properties
      #hikari连接池
      spring.datasource.type=com.zaxxer.hikari.HikariDataSource
      ## 最小空闲连接数量
      spring.datasource.hikari.minimum-idle=5
      ## 空闲连接存活最大时间，默认600000（10分钟）
      spring.datasource.hikari.idle-timeout=180000
      ## 连接池最大连接数，默认是10
      spring.datasource.hikari.maximum-pool-size=10
      ## 此属性控制从池返回的连接的默认自动提交行为,默认值：true
      spring.datasource.hikari.auto-commit=true
      ## 连接池名称
      spring.datasource.hikari.pool-name=MyHikariCP
      ## 此属性控制池中连接的最长生命周期，值0表示无限生命周期，默认1800000即30分钟
      spring.datasource.hikari.max-lifetime=1800000
      ## 数据库连接超时时间,默认30秒，即30000
      spring.datasource.hikari.connection-timeout=30000
      spring.datasource.hikari.connection-test-query=SELECT 1
      ```

   3. Application启动类配置

      ```java
      @SpringBootApplication(scanBasePackages = {"com.yuan"})
      @SpringBootConfiguration()
      @MapperScan(basePackages = {"com.yuan.mapper"})
      ```

