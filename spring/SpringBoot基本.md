#### 1. SpringBoot核心

1. 基本配置

   1. 启动类和核心注解

      程序入口类 *Application

      核心注解@SpringBootApplication

      @SpringBootApplication作为核心注解，

      是一个组合注解(整合以下三注解)

      1. @SpringBootConfiguration

         同为组合注解，SpringBoot中代替@Configuration的注解

         @Configuration的作用：

         ​	相当于application-context.xml中的<beans>标签，

         ​	作为配置类的起点，启动Spring容器。

      2. @EnableAutoConfiguration

         根据当前项目所依赖的jar,自动配置项目相关配置

      3. @ComponentScan

         在SpringBoot中,该注解会扫描入口类的同级包和它们子包中的Bean.

         故该注解的程序入口类应放在**src/main/java**下

      4. 关闭自动配置

         ```java
         @SpringBootApplication(exclude = {..class})
         ```

   2. SpringBoot的全局配置文件

      文件名称：application.properties/application.yml

      放置位置：src/main/resources 或 类路径/config目录

      1. 设置端口号

         server.port = 8080

      2. 定义测试/生产/开发环境

         ```yaml
         spring:
          profiles: test
          
          spring:
          profiles: pro
          
          spring:
          profiles: dev
         ```

         IDEA配置：

         program arguments: --spring.profiles.active=dev

      3. 设置WEB上下文路径

         server.servlet.context-path=/XXX

      4. yml的使用

         1，原有的key，例如spring.jpa.properties.hibernate.dialect，按“.”分割，都变成树状的配置

         2，key后面的冒号，后面一定要跟一个空格

         3，把原有的application.properties删掉。然后一定要执行一下  maven -X clean install

         ```properties
         #yml
         spring:
          datasource:
           url: ...
           driver: com.mysql.cj.jdbc.Driver
           username: root
           password: 449517
         #properties
         spring.datasource.url=jdbc:mysql://localhost:3306/mydb?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
         spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
         spring.datasource.username=root
         spring.datasource.password=449517
         ```

      5. yaml详解

         专门用于编写配置文件的语言，比JSON格式简洁

         1. 基本规则

            - 大小写敏感
            - 使用缩进表示层级关系
            - 缩进时不允许使用Tab键，只允许使用空格。
            - 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可

         2. 数据结构

            - 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
            - 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
            - 纯量（scalars）：单个的、不可再分的值

         3. 数据结构编码

            ```yaml
            #与json格式做对比
            #对象
            animal: pets	#{"animal":"pets"}
            hash:{name:Steve,foo:bar}	#{"hash":{"name":"Steve","foo":"bar"}}
            
            #数组
            - cat
            - dog
            - goldfish	
            animal: [cat,dog]
            ```

   3. 开箱即用的starters

      自动关联项目开发所需的依赖

      示例：

      spring-boot-starter-web

      spring-boot-starter-aop

2. 应用配置的读取

   1. Environment类

      ```java
      @Autowired
      Environment environment
      
      environment.getProperty("key");
      ```

   2. @Value

      ```java
      @Value("${user.property}")
      private String property;
      ```

   3. @ConfigurationProperties

      创建一个配置类后用ConfigurationProperties注解，并指定key前缀

      ```java
      @ConfigurationProperties(prefix = "user")
      @Component
      @Data
      public class UserProperties {
          private String username;
      
          private String password;
      }
      ```

      使用该注解时的优先级问题(未定义环境)：

      不同文件同时存在一个或几个属性

      1. application.yml
      2. application.properties
      3. 自定义的properties

   4. @PropertySource

      通过这个注解，可以读取其他配置文件，而不仅仅是全局配置application.properties

      *注意：该注解不支持yml文件使用*

      @PropertySource+@Value搭配使用：

      ```java
      @Component
      @Data
      @PropertySource("test.properties") //指定路径
      public class StudentProperties {
          @Value("${student.name}")
          private String SName;
          @Value("${student.no}")
          private String SNo;
      }
      ```

3. SpringBoot中的日志

   SpringBoot默认内置logback	

   1. application.properties下的配置

      logging.config = "配置文件路径"

      logging.level = trace

   2. 编写logback-spring.xml
   
      SpringBoot官方推荐命名，由此可以得到SpringBoot提供的额外配置
   
      1. application.yml可以传递参数给logback-spring.xml
   
         ```yaml
         logback:
          logdir: /..
          appname: name
         ```
   
      2. logback-spring.xml必须使用springProperty才能接收
   
         ```xml
         <springProperty name="logdir" source="logback.logdir"></springProperty>
         <springProperty name="appname" source="logback.appname"></springProperty>
         ```
   
      3. 多环境日志输出
   
         1. application.yml添加环境选择配置activce: dev
   
            ```yaml
            spring:
             profiles: 
              active: dev
            ```
   
         2. logback-spring.xml使用springProfile划分环境
   
            ```xml
            ...
            <springProfile name="test,dev">
                
            </springProfile>
            
            <springProfile name="prod">
                
            </springProfile>
            ```
   
   3. 使用其他日志框架
   
      1. dependency
   
         ```xml
         <!--引入starter-->
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-logging</artifactId>
         </dependency>
         <!--排除默认日志框架-->
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-core</artifactId>
             <exclusions>
                 <exclusion>
                     <groupId>commons-logging</groupId>
                     <artifactId>commons-logging</artifactId>
                 </exclusion>
             </exclusions>
         </dependency>
         ```
   
4. SpringBoot的条件注解

   1. Spring中的条件注解

      ```java
      @Bean
      @Conditional({ConditionTest.class})
      ..
      ```

      通过@Conditional实现条件化装配

      ```java
      public class ConditionTest implements Condition{
          ..实现matches方法
      }
      ```

      @Conditional中的类必须实现Condition,用作装配Bean的判断条件

   2. 自定义Starter



#### 2. Spring Boot的web开发

1. SpringBoot Mvc配置

   1. SpringBoot 1.0 ~~WebMvcConfigurerAdapter~~ (举例使用拦截器)

      重写addInterceptors方法

      调用InterceptorRegitry的addInterceptor方法

      addPathPatterns添加URI 或 excludePathPatterns过滤其他URI

      调用父类addInterceptors(可以不用)

      ```java
      @Configuration //重要，启用该配置类
      public class MyWebAppConfigurer extends WebMvcConfigurerAdapter{
      
          @Override
          public void addInterceptors(InterceptorRegistry registry) {
              registry.addInterceptor(new MyInterceptor1()).addPathPatterns("/**");
              registry.addInterceptor(new MyInterceptor2()).addPathPatterns("/**");
              super.addInterceptors(registry);
          }
      }
      ```

   2. SpringBoot 2.0 使用WebMvcConfigurer

      继承WebMvcConfigurer

      1. 添加拦截器

         ```java
         @Override
         public void addInterceptors(InterceptorRegistry registry) {
             registry.addInterceptor(new LoginInterceptor())
                 .addPathPatterns("/**")
                 .excludePathPatterns("/","user/login","/index.html")
                 .excludePathPatterns("/public/**","/resources/**");
         }
         ```

      2. 添加静态资源(不需要DispatcherServlet调度的请求)

         相当于<mvc:resouce>

         ```java
         @Override
         public void addResourceHandlers(ResourceHandlerRegistry registry) {
                 //第一个方法设置访问路径前缀，第二个方法设置资源路径
                 registry.addResourceHandler("/resources/**","/public/**")
                     .addResourceLocations("classpath:/resources/","classpath:/public/");
             }
         }
         ```

         注意点：

         1. **资源路径最后一定多加"/"**

         2. 访问类路径下 加classpath:

         3. 访问绝对路径 加file:

         4. 若访问Webapp下的资源 - 

            pom.xml配置将资源全部编译到classpath下

            ```java
            <resource>
                <directory>src/main/webapp</directory>
                <targetPath>META-INF/resources</targetPath>
                <includes>
                    <include>**/**</include>
                </includes>
            </resource>
            ```

            重写addResourceHandler

            ```java
            @Override
            public void addResourceHandlers(ResourceHandlerRegistry registry) {
                    //第一个方法设置访问路径前缀，第二个方法设置资源路径
                    registry.addResourceHandler("/resources/**")
                        .addResourceLocations("classpath:META-INF/resources/");
                }
            }
            ```

      3. 添加视图

         使用该手段，可以免去控制器中的页面控制代码

         ```java
         @Override
         public void addViewControllers(ViewControllerRegistry registry) {
             registry.addViewController("/").setViewName("login");
             registry.addViewController("/index.html").setViewName("login");
         }
         ```
         等同于

         ```java
         @GetMapping("/")
         public String index(){
             return "index";
         }
         ```
         
         等同于XML配置
         
         ```xml
         <mvc:view-controller path="/" view-name="login"></mvc:view-controller>
         <mvc:view-controller path="/index.html" view-name="login"></mvc:view-controller>
         ```

2. SpringBoot 使用Thymeleaf

   1. 依赖(starter)

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-thymeleaf</artifactId>
      </dependency>
      ```

   2. 资源位置(自动配置)

      1. 视图页面放在**src/main/resources/templates**下，SpringBoot自动解析
      2. JS CSS 图片等静态资源放在**src/main/resources/static**目录下

   3. application全局配置

      ```yaml
      spring:
        thymeleaf:
          enabled: true  #开启thymeleaf视图解析
          encoding: utf-8  #编码
          prefix: classpath:/templates/  #前缀
          cache: false  #是否使用缓存
          mode: HTML  #严格的HTML语法模式
          suffix: .html  #后缀名
      ```
   
3. SpringBoot开发国际化

4. SpringBoot表单验证

   1. 嵌套验证的概念

      实体类

      ```java
      public class Item {
      
          @NotNull(message = "id不能为空")
          @Min(value = 1, message = "id必须为正整数")
          private Long id;
      
          @NotNull(message = "props不能为空")
          @Size(min = 1, message = "至少要有一个属性")
          private List<Prop> props;
      }
      
      public class Prop {
      
          @NotNull(message = "pid不能为空")
          @Min(value = 1, message = "pid必须为正整数")
          private Long pid;
      
          @NotNull(message = "vid不能为空")
          @Min(value = 1, message = "vid必须为正整数")
          private Long vid;
      
          @NotBlank(message = "pidName不能为空")
          private String pidName;
      
          @NotBlank(message = "vidName不能为空")
          private String vidName;
      }
      ```

      控制器验证

      ```java
      @RestController
      public class ItemController {
      
          @RequestMapping("/item/add")
          public void addItem(@Validated Item item, BindingResult bindingResult) {
              doSomething();
          }
      }
      
      //@Validated或@Valid只能验证item项，而不能验证item列表内prop
      ```

   2. 实现嵌套验证

      ```java
      public class Item {
      
          @NotNull(message = "id不能为空")
          @Min(value = 1, message = "id必须为正整数")
          private Long id;
      
          @Valid // 嵌套验证必须用@Valid
          @NotNull(message = "props不能为空")
          @Size(min = 1, message = "props至少要有一个自定义属性")
          private List<Prop> props;
      }
      ```

      @Validated：用在方法入参上无法单独提供嵌套验证功能。不能用在成员属性（字段）上，也无法提示框架进行嵌套验证。能配合嵌套验证注解@Valid进行嵌套验证。

      @Valid：用在方法入参上无法单独提供嵌套验证功能。能够用在成员属性（字段）上，提示验证框架进行嵌套验证。能配合嵌套验证注解@Valid进行嵌套验证。
   
5. SpringBoot juint测试

   ```java
   @SpringBootTest(classes={com.yuan.ApplicationTest.class})
   @RunWith(SpringRunner.class)
   public class MyControllerTest {
       private MockMvc mockMvc;
   
       @Autowired
       private Environment environment;
   
       @Autowired
       private WebApplicationContext webApplicationContext;
   
       @Before
       public void setup() {
           // 实例化方式一
           //        mockMvc = MockMvcBuilders.standaloneSetup(new MyController()).build();
           // 实例化方式二
           mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
       }
   
       @Test
       public void testHello() throws Exception {
           String property = environment.getProperty("user.username");
           System.out.println(property);
   
           mockMvc
               .perform(MockMvcRequestBuilders.get("/hello")
                        .accept(MediaType.APPLICATION_JSON))
               .andExpect(MockMvcResultMatchers.status().isOk())
               .andDo(MockMvcResultHandlers.print())
               .andReturn();
       }
   }
   ```

   
