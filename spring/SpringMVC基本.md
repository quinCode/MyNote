### 一.初始化和流程

#### 1.设计概述

1.1 架构
Servlet(Controller)+JSP(View)+java Bean(Model)

传统模型Model被拆分：Service(兼顾对dao的'声明式事务'操作和NoSQL的访问) + Dao(data access object)

1.2 组件与流程：基于Servlet
核心控制器DispatcherServlet

流程：
1.用户向 DispatcherServlet 请求
2.处理器映射 HandlerMapping
3.处理器适配器 HandlerAdapter 运行Handler和拦截器
4.模型和视图 ModelAndView
5.视图解析器 ViewResolver
6.视图 View

1.3 入门实例
1.3.1 
WEB工程：web.xml的配置 
<context-param>配置SpringIOC <listener>配置ContextLoaderListener用以初始化SpringIOC容器
<servlet>配置DispatcherServlet    (/WEB-INF/dispathcer-servlet.xml)

1.3.2 SpringMVC配置文件：
dispatcher-servlet.xml
注解驱动 <mvc:annotation-driven>
配置视图解析器 InternalResourceViewResolver的Bean
p:prefix 和 p:suffix 定义视图指定路径格式

1.3.3 简单Controller
二注解：@Controller表明为一个控制器类  @RequestMapping指定对应的URI 
				(经存放和解析,得到HandlerMapping)
ModelAndView类 设置jsp文件的属性

#### 2.Spring MVC初始化

2.1 初始化Spring IOC上下文
实现接口ServletContextListener，完成ServletDispatcher初始化前和销毁后的工作
SpringMVC的ContextLoaderListener实现该接口

2.2 Dispatcher的初始化
HttpServletBean的init方法 -> FrameworkServlet的initServletBean方法...

2.3 注解配置方式初始化
继承AbstractAnnotationConfigDispathcerServletInitializer 完成SpringIOC和DispathcerServlet初始化
三方法
getRootConfigClasses 获取Spirng IOC的java配置类，用以装载Spring Bean
getServletConfigClasses  获取URI和控制器映射关系
getServletMappings 定义DispatcherServlet拦截的请求

@EnableWebMvc注解
创建视图解析器@Bean

#### 3.SpringMVC

概述：注解@Controller @RequestMapping
多个拦截器+一个控制器 存入HandlerMapping
@GetMapping @PostMapping @DeleteMapping @PatchMapping

3.1 配置@RequestMapping
3.2 控制器开发
重点：获取请求参数、处理业务逻辑、绑定模型和视图
特别：不要使用Servlet容器提供的API(HttpServletRequest,HttpSession等),这会造成控制器依赖Servlet容器
解决：1.@RequestParam 获取请求参数
          2.@SessionAtrribute
          3.为视图添加模型 addObject ，在jsp中使用EL表达式和JSTL ${objectName.attribute}

### 二. Spring MVC组件的开发

#### 1.接收各类请求参数

1.1 简单方式：请求参数name与对应URL的方法参数名称 相同，可以建立各属性名都一一对应的POJO。

1.2 使用@RequestParam
参数列表上使用 @RequestParam("Http请求参数name")
默认参数不为空,通过改变required

1.3 使用URL传递参数 (符合RESTFul风格？)
@ReqeustMapping 和 @PathVariable

1.4 传递JSON参数
jQuery方法：$.post({
		url:"",
		data:JSON.stringify(),
		contentType:"application/json",
		success:function(result){}
		});
	
@RequestBody 接收前台传递的JSON数据 并转换为java对象
@ResponseBody 将后台的java对象转换为JSON数据 传递到前端
(！：需要jackson annotations core databind 三依赖包)

1.5 接收列表数据和表单序列化

序列化：jQuery方法 serialize()  -> name=xxx&&pass=xxx

#### 2.重定向

Model对象 和 ModelAndView对象作参数，Spring MVC会初始化
返回字符串"redirect:xxx.do"
mv.setViewName("redirect:xxx.do")

重定向传递对象属性
数据模型：RedirectAttribute  addFlashAttribute方法加入对象

#### 3 保存并获取属性参数

3.1.@RequestAttribute 请求属性   对参数进行注解
3.2.@SessionAttribute 会话属性	读取Session中的属性
3.3.@SessionAttributes 保存在会话中的数据模型对应的键值对	在类上注解,借此可以设置Session属性
通过名称 names={}	或 通过类型 types={}    加入数据模型(ModelAndView)中的属性就会加入到Session中
3.4.@CookieValue @RequestHeader
获取Cookie值 和 请求头信息 （如何设置他们？）

#### 4.拦截器

@RequestMapping -> @HandlerMapping -> HandlerExecutionChain对象
4.1 拦截器的定义
实现接口 HandlerInterceptor
preHandle 处理器Handler执行前
postHandle Handler执行后
afterCompletion 是否发送异常都会在视图解析后执行的方法
4.2 单个拦截器执行流程
preHandler 返回boolean ,若false结束运行；若true，处理器执行->postHandle
->视图解析、渲染->afterCompletion
4.3 拦截器的开发
默认拦截器 HandlerInterceptorAdapter，继承它，根据需要重写三方法
在这之前：xml配置 <mvc:interceptors><mvc:interceptor><mvc:mapping path=""><bean class="">
4.4 多个拦截器执行流程 (按照xml配置的顺序，依次执行)
preHandle1 preHandle2 Handler postHandle2 postHandle1 视图 afterCompletion2 afterCompletion1
经典的责任链模式
4.5 验证表单 校验数据的合法性
1.JSR303 规范
Errors类型：保存是否存在错误信息   Errors.hasErrors()
2.验证器
实现Validator ：supports \ validate 双方法

4.6 数据模型
ModelAndView 属性model(类型为ModelMap) 继承LinkedHashMap<Stirng,Object>
扩展 ：ExtendedModelMap 和 BindAwareModelMap
作用域：Request
实际Spring MVC自动初始化的BindAwareModelMap，可以使用Model和ModelMap作为数据模型

4.7 视图和视图解析器
1.视图 View接口
非逻辑视图 MappingJackson2JsonView ，不需要解析视图名字
逻辑视图 InternalResourceView ，需要视图解析器
2.视图解析器
3.Excel视图
AbstractXlsView抽象类
需要实现buildExcelDocument

4.8 上传文件
MultipartResolver接口 两个实现类：CommonsMultipartResolver StandardServletMultipartResolver

1. 配置MultipartResolver xml和注解的bean对象即可   BeanName固定为multipartResolver
2.提交上传文件表单
配置multipart-config 或 覆盖 customizeRegistration
<form enctype="multipart/form-data">
使用MultipartFile 和 Part类 接收文件
4.9 我的测试
$.post(url,data,function(back-end_data){})
@RequestParam 可以处理Json字符串
js处理Json字符串 ：JSON.parse()   $.parseJSON()
判断Json字符串是否为空：JSON.stringify(data)=="{}"   $.isEmptyObject({})
函数触发

### 三. Spring MVC高级应用

#### 1. Spring MVC的数据转换和格式化

DispatcherServlet -> HandlerMapping -> HandlerAdapter
@ResponseBody

#### 2. Converter 一对一转换器

java实现 Converter接口 convert方法
XML配置 注册converter ：
class="org.springframework.format.support.FormattingConversionServiceFactoryBean"
<property name="converters"><list><bean class=自己的转换器>

#### 3. 上传文件

