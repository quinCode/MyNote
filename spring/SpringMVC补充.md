#### 1.@ModelAttribute

 1. 注解在非请求处理方法上

    常用于访问权限控制

    ```java
    //注解在非请求方法上，会在 该控制器调用请求处理方法前 被调用
    @ModelAttribute
    public void isLogin(@SessionAttribute User user){
        if(user.role != UserRole.ADMIN){
            //没有权限
        }
    }
    ```

    注意点- 与Filter过滤器 MVC拦截器的先后顺序

    1. Filter的doFilter
    2. MVC拦截器的preHandle
    3. @ModelAttribute注解的非请求处理方法

#### 2.请求转发和重定向

1. 请求转发

   ```java
   @RequestMapping("/...")
   public String login(){
       if(...)
           //请求转发到另一个请求方法
           return "forward:/loginVerify";
       return "/loginSuccess";
   }
   ```

2. 重定向

   ```java
   @RequestMapping("/...")
   public String login(){
       if(...)
           //重定向到另一个请求方法
           return "redirect:/loginVerify";
       return "/loginSuccess";
   }
   ```

#### 3. 类型转换

1. Converter<S,T>接口

   S:源数据类型，一般为请求参数String类型

   T:目标数据类型，一般为转换后的对象类型

2. 自定义类型转换器

   1. 实现Converter接口的convert方法

   2. 进行字符串处理

   3. 代码示例

      ```java
      @Component
      public class GoodsConverter implements Converter<String, Goods> {
          @Override
          public Goods convert(String goodInfo) {
              Goods goodModel = null;
              String[] goods = goodInfo.split(",");
              if(goodInfo != null && goods.length == 3){
                  try{
                      Double goodPrice = Double.parseDouble(goods[1]);
                      Integer goodNo = Integer.parseInt(goods[2]);
                      goodModel = new Goods(goods[0], goodPrice, goodNo);
                  }catch (Exception e){
                      e.printStackTrace();
                      System.out.println("数据格式错误");
                  }
              }else{
                  System.out.println("数据格式错误");
              }
              return goodModel;
          }
      }
      ```

3. 配置类注册类型转换器

   装配自定义转换器对象

   装载ConfigurableConversionService类对象,用于注册类型转换器

   注册方法上使用@PostConstruct，表示在加载Servlet时运行

   使用ConfigurableConversionService的addConverter方法

4. 自定义代码实例

   1. dispatcher-servlet.xml实现

      ```xml
      <mvc:annotation-driven conversion-service="conversionService"/>
      <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
          <property name="converters">
              <set>
                  <ref bean="goodsConverter"></ref>
              </set>
          </property>
      </bean>
      ```

   2. 注解+java实现

      在WebMvcConfigurer下编写
      
      ```java
      @Resource
      ConfigurableConversionService configurableConversionService;
      @Resource
      GoodsConverter goodsConverter;
      
      @PostConstruct
      public void addConverter(){
          if (configurableConversionService != null) {
              configurableConversionService.addConverter(goodsConverter);
          }
      }
      ```

5. SpringMVC内置类型转换器

#### 4.格式化转换器Formatter

1. 与Converter

   1. 相同点：

      同样是将一种数据类型 转换为 另一种数据类型

   2. 不同点：

      Formatter<T> 源数据类型必须为String(在WEB应用中更合理)

      Converter<S,T> 源数据类型可以任意指定

2. 内置格式化转换器

   NumberFormatter CurrencyFormatter PercentFormatter DateFormatter

3. 自定义格式化转换器

   实现org.springframework.format.Formatter接口的parse和print

   parse：利用指定的Locale 将String转换为目标类型

   print：利用指定的Locale 将一个类型转换为String

4. 分步代码实现

   1. 实现Formatter

      ```java
      @Component
      public class MyDateFormatter implements Formatter<Date> {
          DateFormat dateFormat = new SimpleDateFormat("yyyy/MM/dd");
      
          @Override
          public Date parse(String s, Locale locale) throws ParseException {
              Date date = dateFormat.parse(s);
              return date;
          }
      
          @Override
          public String print(Date date, Locale locale) {
              return dateFormat.format(date);
          }
      }
      ```

   2. 注册Formatter

      同ConfigurableConversionService相近

      需要装载FormattingConversionService

      ```java
      @Resource
      MyDateFormatter dateFormatter;
      @Resource
      FormattingConversionService formattingConversionService;
      
      @PostConstruct
      public void addFormatter(){
          if (formattingConversionService != null) {
              formattingConversionService.addFormatter(dateFormatter);
          }
      }
      ```
      
   3. 问题：后台时间Date传回前台会变成Long类型的时间戳
   
      解决：
   
      1. Jackson的注解@JsonFormat
   
         ```java
         @JsonFormat(pattern="yyyy-MM-dd HH:mm",timezone = "GMT+8")
         private Date createdDate;
         ```
   
      2. application.yml全局配置
   
         ```yaml
         spring: 
             jackson:
                 #如果使用字符串星表示用这行格式
                 date-format: yyyy-MM-dd 
                 #设置为东八区时间
                 timezone: GMT+8
                 serialization：
                     #使用数值timestamp表示日期 值为13413451345数字
                     write-dates-as-timestamps: true
                     #想要值为2016-01-01
                     write-dates-as-timestamps: false
         ```
   
      3. 若时间表示为String类型
   
      4. 前端JS实现
   
         ```java
         function changeDateFormat(obj) {
             var dateVal = obj+ "";
             if (obj!= null) {
                 var date = new Date(parseInt(dateVal.replace("/Date(", "").replace(")/", ""), 10));
                 var month = date.getMonth() + 1 < 10 ? "0" + (date.getMonth() + 1) : date.getMonth() + 1;
                 var currentDate = date.getDate() < 10 ? "0" + date.getDate() : date.getDate();
         
                 var hours = date.getHours() < 10 ? "0" + date.getHours() : date.getHours();
                 var minutes = date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes();
                 var seconds = date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
         
                 return date.getFullYear() + "-" + month + "-" + currentDate + " " + hours + ":" + minutes + ":" + seconds;
             }
         }
         ```

#### 5.数据验证

1. 数据验证

   客户端验证：JS代码实现，过滤正常用户的错误输入

   服务端验证：服务器编程实现，拦截非法用户的非法输入

   服务端验证和转换器格式化的关系：

   SpringMVC中，先进行数据类型转换(数据验证在其中介入)，再进行服务器端数据验证。

2. Spring验证器

   1. Validator接口

      ```java
      //需要实现Validator的两个方法
      boolean supports(Class<?> class) //决定验证器支持什么类
      void validate(Object obj,Errors errors) 
      //验证目标对象obj,错误消息会放在Errors对象
      ```

      Errors对象

      ```java
      //存入消息有reject和rejectValue方法，它们有不少重载
      void reject(String errorCode)
      void rejectValue(String field,String errorCode)
      ```

      supports方法

      ```java
      public boolean supports(Class<?> class){
          return Goods.class.isAssignableForm(class);
      }
      
      isAssignableForm方法：父类型.class.isAssignableForm(子类型.class)
      instanceof 关键字：子类实例 instanceof 父类型.class
          
      JVM相关引申：    
      引申学习1：Class对象
          每个类的运行时的类型信息就是用Class对象表示的。它包含了与类有关的信息。
      引申学习2：A.class 和 a.getClass() 的异同 (A a = new A())
          1.getClass()是Object的实例方法，故只有a可以调用
          2..class是每个类的Class对象，故类型类A可以调用
      ```

   2. ValidationUtils

      工具类，能有效帮助验证数据是否为空或空值

      ```java
      ValidationUtils.rejectIfEmpty(errors,"gname","gname.empty");
      ValidationUtils.rejectIfEmptyOrWhiteSpace(errors,"gname","gname.empty");
      //相当于 ->
      if(gname == null || gname.trim().isEmpty)
      if(gname == null || gname.isEmpty())
          errors.rejectValue("gname","gname.empty");
      ```

   3. 错误消息属性文件 errorMessages.properties

      ```properties
      goods.gname.required = 请输入商品名
      ...
      -> 转换为unicode
      ```

      SpringMVC配置：设置Bean放入Spring容器

      ```java
      //Bean名字Spring规定为此
      @Bean("messageSource")
      public ResourceBundleMessageSource getMessageSource(){
          ResourceBundleMessageSource rbms = new ResourceBundleMessageSource();
          //默认寻找src/main/resources下的errorMessages.properties
          rbms.setBasename("errorMessages");
          rbms.setDefaultEncoding("UTF-8");
          return rbms;
      }
      ```

   4. 在控制器装配并调用validate

      ```java
      public String test(BindingResult result,Goods goods){
          goodsValidator.validate(goods,result)//添加验证
      	if(result.hasErrors()){
              return "addGoods";
          }
          return "suceess";
      }
      ```

      BindingResult类：继承自Errors类，理所应当拥有更多的功能
   
3. JSR303验证

   1. 依赖 Hibernate Validator

      Hibernate Validator是JSR303的其中一个实现

      ```xml
      <dependency>
          <groupId>org.hibernate.validator</groupId>
          <artifactlId>hibernate-validator</articalId>
          <version>6.1.2.Final</version>
      </dependency>
      ```

      ​	SpringBoot starter

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-validation</artifactId>
      </dependency>
      ```

   2. 标注类型

      利用标注类型在实体模型的属性上嵌入约束

      1. 空检查

         ```java
         @Null:对象是否为空
         @NotNull:对象是否不为空
         @NotBlank:用于字符串，trim后是否为空
         ```

      2. boolean检查

         ```java
         @AssertTrue @AssertFalse
         ```

      3. 长度检查

         ```java
         @Size(min=1,max=100):可验证对象Array\Collection\Map\String
         @Length(min=1,max=100):仅验证String
         ```

      4. 日期检查

         验证Date和Calendar对象

         ```java
         @Past:之前
         @Future:之后
         @Pattern:正则表达式验证String
         ```

      5. 数据检查

         ```java
         @Max 验证Number\String
         @Min
         @DecimalMax
         @DecimalMin
         
         @Digits(integer=,fraction=)整数精度和小数精度
         @Range(min=,max=)检查数字
         
         @Valid 对关联对象验证
         @CreditCardNumber 信用卡验证
         @Email 邮件地址
         ```

      6. 错误消息属性文件

         errorMessages.properties -> ValidationMessages.properties

         Hibernate Validator默认从classpath取到这个文件

         不需要MVC配置

         ```java
         //通过表达式获取配置文件信息
         @NotBlank(message="{goods.ganme.required}")
         private String gname;
         ```

         想写中文？

         GBK -> UTF-8

      7. 通过@Valid开启验证

         ```java
         public String save(@Valid Goods goods,BindingResult result)
         ```

      8. 完成测试

         1. model

            ```java
            @Data
            @AllArgsConstructor
            public class Goods {
                private Integer goodNo;
                @NotBlank(message = "{good.goodName.notBlank}")
                private String goodName;
                @NotNull(message = "{good.goodPrice.invalid}")
                @Range(min = 0,max = 500,message = "{good.goodPrice.invalid}")
                private Double goodPrice;
                @Past(message = "{good.goodCreatedDate.invalid}")
                @JsonFormat(pattern = "yyyy-MM-mm",timezone="GMT+8")
                private Date goodCreatedDate;
            }
            ```

         2. GoodsAspect

            ```java
            @Around("pointcut()")
            public Object validate(ProceedingJoinPoint joinPoint) throws Throwable {
                Object[] args = joinPoint.getArgs();
                BindingResult bindingResult = (BindingResult) args[1];
                if(bindingResult.hasErrors()){
                    Map<String, String> hashMap = new HashMap<>();
                    for(FieldError error:bindingResult.getFieldErrors()){
                        hashMap.put(error.getCode(),error.getDefaultMessage());
                    }
                    JsonResult<Goods> jsonResult = new JsonResult<>(0,hashMap,null);
                    return jsonResult;
                }
                return joinPoint.proceed();
            }
            ```

#### 6.国际化（页面输出国际化）

错误消息国际化 与 页面输出国际化

1. 概述 Java中的国际化

   1. 国际化思想：程序信息放入配置文件中,程序根据语言环境选择相应资源

   2. 重要类：java.util.Locale java.util.ResourceBundle

   3. 资源文件命名

      baseName.properties baseName_language.properties baseName_language_country.properties

      language_country即为各语言环境：地区代码_国家代码 zh_CN en_US

   4. 测试用例

      ```java
      Locale locale = Locale.getDefaultLocale();
      ResourceBundle rb = ResourceBundle.getBundle("messageResource",locale);
      
      //先查找messageResource_zh_CN.properties
      //若找不到,再找messageResource_zh.properties
      //再找messageResource.properties
      ```

   5. 使用占位符 ...

2. SpringMVC 国际化

   1. 加载资源属性文件

      不直接使用ResourceBundle加载资源属性文件

      而是通过Bean(messageSource)告知

      ```java
      //加载多组资源属性文件
      ResourceBundleMessageSource rbms = new ResourceBundleMessageSource();
      String messageFiles[] = {'messages','labels'};
      rbms.setBasenames(messageFiles);
      rbms.setDefaultEncoding("UTF-8");
      return rbms;
      ```

      ...

#### 7.统一异常处理

将所有类型的异常处理从各层种解耦出来

SimpleMappingExceptionResovler HandlerExceptionResolver 

@ExceptionHandler @ControllerAdvice

1. SimpleMappingExceptionResolver类

   用于异常类和视图的简单对应关系

   ```java
   @Bean
   public SimpleMappingExceptionResolver getExceptionResolver(){
       SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
       //定义默认的异常处理页面
       exceptionResolver.setDefaultErrorView("error");
       //定义需要特殊处理的异常,以异常全限定类名为键，以自定义异常页面名为值
       Properties mappings = new Properties();
       mappings.put("java.sql.SQLException","sql-error");
   
       exceptionResolver.setExceptionMappings(mappings);
       return exceptionResolver;
   }
   ```

2. HandlerExceptionResolver接口

   用于解析请求处理过程中产生的异常

   开发者需要实现它

   ```java
   @Component
   public class MyExceptionHandler implements HandlerExceptionResolver {
       @Override
       public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
           if(e instanceof SQLException){
               return new ModelAndView("sql-error");
           }
           return new ModelAndView("error");
       }
   }
   ```

3. @ExceptionHandler注解

   1. 作用范围:在某个Controller内

   2. 注解作用：该Controller中任何方法中抛出了异常，那么就会调用@ExceptionHandler注解的方法，交由它来处理异常

   3. 实例

      提高代码复用，使用抽象类BaseController，在某一Controller需要统一处理异常时继承即可。

      ```java
      public abstract class BaseController {
          @ExceptionHandler
          public String handleException(Exception e){
              if(e instanceof SQLException){
                  return "sql-error";
              }
              return "error";
          }
      }
      ```

4. @ControllerAdvice

   使用继承抽象父类的方式，虽可代码复用，但是增加了父子耦合。

   使用@ControllerAdvice，可以对**全局**控制器实现三方面功能

   全局异常处理，全局数据绑定，全局数据预处理

   @ControllerAdvice对于注解了@RequestMapping(get,post,put,delete)等控制器方法有效

   ```java
   @ControllerAdvice(basePackages = {"com.yuan.controller"})
   public class GlobalExceptionHandlerController {
       @ExceptionHandler
       public String handleException(Exception e){
           if(e instanceof SQLException){
               System.out.println("sql错误");
               return "sql-error";
           }
           return "error";
       }
   }
   ```

   补充：@ControllerAdvice相当于为控制器织入通知(与SpringAOP类似)

   可以指定对应包，缩小范围 basePackages

#### 8.文件上传和下载

1. 文件上传

   1. commons-flieupload组件

      SpringMVC基于commons-flieupload组件的文件上传，

      性能优秀，支持任意大小文件的上传

   2. 基于表单的文件上传

      ```html
      <form action="upload" method="post" enctype="multipart/form-data">
          <input type="file"/>
      </form>
      ```

      重要:enctype属性 multipate/form-data ,method post

   3. MultipartFile接口

      SpringMVC将文件相关信息和操作封装到该对象中

      内部方法一览：

      ```java
      String getName(); // 获取参数的名称
      String getOriginalFilename(); // 获取文件的原名称
      String getContentType(); // 文件内容的类型
      boolean isEmpty(); // 文件是否为空
      long getSize(); // 文件大小
      byte[] getBytes(); // 将文件内容以字节数组的形式返回
      InputStream getInputStream(); // 将文件内容以输入流的形式返回
      void transferTo(File dest); // 将文件内容传输到指定文件中
      ```

   4. MultipartResolver接口

      1. 作用

         用于处理文件上传的解析器

         收到请求后，Dispatcher调用MultipartResolver的方法判断请求是否包含文件；

         若包含，Dispatcher调用MultipartResolver的方法对请求数据解析为MultipartFile对象并封装到HttpServletRequest(MultipartHttpServletRequest)中，最终传递给控制器

      2. 两个实现类

         StandardServletMultipartResolver ：基于Servlet 3.0，不需要第三方依赖

         CommonsMultipartResolver : 基于commons Fileupload,需要第三方依赖

         1. 配置CommonsMultipartResolver 

            ```java
            @Bean
            public CommonsMultipartResolver getMultipartResolver(){
                CommonsMultipartResolver multipartResolver = new CommonsMultipartResolver();
                //设置编码
                multipartResolver.setDefaultEncoding("UTF-8");
                //设置文件大小限制,单位字节
                multipartResolver.setMaxUploadSize(100*1024*1024);
                //设置临时上传路径
                Resource resource = new FileSystemResource(staticResourceDir);
                try {
                    multipartResolver.setUploadTempDir(resource);
                } catch (IOException e) {
                    e.printStackTrace();
                }
                return multipartResolver;
            }
            ```

            Resource类：对文件资源的操作和数据进行了封装

            1. FileSystemResource

               相当于- ("file:D:\uploads\");

            2. ClassPathResource

               相当于-("classpath:")

               ```java
               Resource resource = new FileSystemResource("D:/uploads/error.jpg");
               Resource resource1 = new ClassPathResource("/static/img/dog.jpg");
               ```

      3. 补充：SpringBoot默认使用StandardServletMultipartResolver解析Multipart请求,不需要配置
      
         若要配置CommonsMultipartResolver
      
         ```yaml
         spring:
           autoconfigure:
             exclude: org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration
         ```
      
      4. 控制器代码
      
         单文件上传
      
         文件上传过程，使用传统的IO流API传输数据
      
         ```java
         @Controller
         public class FileUploadController {
             @Value("${static.resources.dir}")
             private String path;
         
             @PostMapping("/upload")
             public String uploadFile(MultipartFile uploadFile){
                 String fileName = uploadFile.getOriginalFilename();
         
                 InputStream inputStream = null;
                 OutputStream outputStream = null;
                 try {
                     //将上传文件内容传输到指定文件
                     inputStream = uploadFile.getInputStream();
                     outputStream = new FileOutputStream(path+fileName);
         
                     int readcount;
                     byte[] bytes = new byte[1024*1024];
                     while ((readcount = inputStream.read(bytes)) != -1){
                         outputStream.write(bytes,0,readcount);
                         outputStream.flush();
                     }
                     System.out.println("上传成功！");
                 } catch (IOException e) {
                     System.out.println("出错惹！");
                     e.printStackTrace();
                 }finally {
                     if (outputStream != null) {
                         try {
                             outputStream.close();
                         } catch (IOException e) {
                             e.printStackTrace();
                         }
                     }
                     if(inputStream != null){
                         try {
                             inputStream.close();
                         } catch (IOException e) {
                             e.printStackTrace();
                         }
                     }
                 }
                 return "/test";
             }
         }
         ```
   
2. 文件下载

   1. 实现下载功能的两种方法

      超链接下载：暴露文件下载路径

      程序编码控制下载：扩展性强，安全性高

   2. 响应设置两个报头

      ```java
      //服务器告知浏览器，输出内容类型是保存到本地的文件
      Content-Type:application/x-msdownload
      //该报头指定了接收程序处理数据内容的方式
      Content-Disposition:attachment;filename=...
      //attachment表示用户干预
      ```

   3. 操作
   
      FileUtils类依赖
   
      ```xml
      <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.3</version>
          </dependency>
      ```
   
      超链接下载
   
      ```html
      <a href="/download/">
                  文件下载
              </a>
      ```
   
      控制器处理 使用Servlet API
   
      ```java
      @GetMapping("/download")
      public String downloadFile(HttpServletResponse response){
          OutputStream stream = null;
          String filename = "admin.png";
          try {
              File file = new File("D:/uploads/",filename);
              response.reset();
              response.setContentType("application/octet-stream; charset=utf-8");
              response.setHeader("Content-Disposition", "attachment; filename=" + file.getName());
              stream = response.getOutputStream();
              stream.write(FileUtils.readFileToByteArray(file));
      
              stream.flush();
          } catch (IOException e) {
              e.printStackTrace();
          }finally {
              try {
                  stream.close();
              } catch (IOException e) {
                  e.printStackTrace();
              }
          }
          return "test";
      }
      ```
   
      

