### 一.Spring IOC/AOP 基本概念

#### 1. IOC和依赖注入的概念：

IOC：一种设计思想，旨在解除对象与对象之间的耦合度，将创建和查找依赖对象(Bean资源)的
控制权交由Spring IOC容器。

DI：依赖注入，实现在系统运行时动态地将依赖对象提供给某个对象，通过反射实现（反射是指
可以在程序运行时创建对象和执行对象的方法）

#### 2. Spring Bean的生命周期

1.初始化
2.BeanNameAware接口的setBeanName方法
3.BeanFactoryAware接口的setBeanFactory方法
4.ApplicationContextAware的setApplicationContext方法
（要求容器实现ApplicationContext接口）
5.BeanPostProcessor接口的postProcessBeforeInitialization方法
6.InitializingBean的afterPropertiesSet方法
7.自定义初始化方法
8.BeanPostProcessor接口的postProcessAfterInitialization方法
9.生存期
10.Disposable的destory方法
11.自定义销毁方法
（BeanPostProcessor针对所有Bean,DisposableBean针对SpringIOC容器,其他均只针对单个Bean对象）

### 二. 装配Spring Bean

#### 1.三种依赖注入

构造器注入、setter注入（常用）、接口注入

#### 2.三种将Bean装配到容器的方法（约定高于配置的开发原则）

XML（3）、java接口和类（2）、隐式Bean自动装配（1）

##### 2.1 XML配置装配 

引入对应的XSD文件（XML模式）
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xsi:schemaLocation="http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
装配简单值和类、装配集合
命名空间装配 c:_0="1" p:id="1" util

使用的ApplicationContext实现类是ClassPathXmlApplicationContext

##### 2.2 通过注解装配（主流）

2.2.1 使用@Component （只能注解在类上）
@ComponentScan 扫描该类所在包的所有Pojo（专门一个Java类）
@Component(value = "role")
@Value("1")

使用的AnnotationConfigApplicationContext实现类

扫描所指定的类：
@ComponentScan 配置项basePackages basePackageClasses 
使用接口编写操作类

2.2.2 使用@Autowired
属性自动装配和setter自动装配

自动装配的歧义性（单个接口可有多个实现类/父类有多个子类）
@Primary 首要装配该实现类
@Qualifier(xxx) 这个属性首要装配这个Component    (较优先 并且不用写Autowired)

2.2.3 装载有参构造的类
@Autowired @Qualifier 支持参数注解

2.2.4 使用@Bean装配
注解在方法上，在引入第三方包并且生成其中的对象Bean时使用
配置项： initMethod destoryMethod name、

2.2.5 装配使用：第三方服务使用XML 、内部系统开发使用注解

##### 2.3 使用Profile 

2.3.1 使用注解
@Profile("dev")		@Profile("test")
用于开发			用于测试
2.3.2 使用XML
<beans profile="test"></beans>
2.3.3 激活Profile
@ActiveProfiles("dev") 

##### 2.4 加载属性文件

2.4.1 注解方式加载
@PropertySource(name="" value={"",""} )
context.getEnvironment().getProperty()

属性文件解析类：PropertySourcesPlaceholderConfigurer 解析属性占位符能力
使得注解形式装配Bean可以使用 $占位符 传递变量

2.4.2 XML方式
<context:property-placeholder>
<bean class = "org.springframework.context.support.PropertySourcePlaceholderConfigurer"

2.4.3 条件化装配Bean
注解在方法\类上：@Conditional(xxxCondition.class) 
xxxCondition.class 实现Condition接口的matches方法

##### 2.5 Bean的作用域

四种作用域：单域singleton 原型prototype 会话session 请求request
@Scope(ConfigurableBeanFactory.SCOPE.PROTOTYPE)

##### 2.6 使用Spring表达式：对Bean属性进行注入

使用类：ExpressionParser Expression SpelExpressionParser实现类
为何使用表达式？：使用变量解析表达式，创建对象，调用对象方法获取属性
变量的解析：EvaluationContext StandardEvaluationContext 

使用注解Value("#{}")

2.6.1 简单值的注入/调用方法/调用Bean的属性和方法
表达式声明：#{}    注入字符串 #{''}   注入int/long #{1}
防止空指针：?. 调用

2.6.2 类的静态常量/静态方法   Spring EL运算
#{T(全限定类名).变量名/方法名}   注意类名要+包名

### 三.面向切面编程

#### 3.1 Interceptor约定

​      before -> bean方法 -> after -> 是否异常 -> afterThrowing / afterReturning
(JDK动态代理)获取Bean：ProxyBeanFactory.getBean (ProxyBeanFactory实现InvocationHandler接口)	

#### 3.2 Spring AOP 基本概念

before：打开数据连接
method执行：执行SQL
afterThrowing：执行SQL过程发生异常，及时回归
afterReturning：执行SQL正常，及时提交

3.2.1 术语
切面：拦截器类即为一个切面类
通知：before after afterReturning afterThrowing aroundThrowing
引入
切点：被切面拦截的方法

#### 3.3 Spirng对 AOP的支持 使用@AspectJ

基于方法拦截：使用ProxyFactoryBean、接口实现AOP\实现XML\使用@AspectJ(重点)

3.3.1 选择切点 

3.3.2 创建切面(拦截器)@Aspect 
@Before @After @AfterReturning @AfterThrowing

3.3.3 连接点(判断方法是否需要拦截)
execution表达式	
指示器：execution within
@Pointcut注解和print()

3.3.4 测试 
AopConfig类 将Aspect配置为一个Bean
@EnableAspectJAutoProxy 

总结一波实战测试：
1.切点（被拦截的方法）：接口与实现类 作为Bean放入Spring IOC容器
2.切面：@Aspect注解 @Before @After @AfterThrowing @AfterReturning 
3.连接点：@PointCut修饰一个方法，execution指示器
4.配置类：AOPConfig配置类 @EnableAspectProxy

3.3.5 环绕通知 @Around
提供了调度被代理对象原有方法的功能，可大量改变业务逻辑
ProceedingJoinPoint参数  proceed方法调用原方法

3.3.6 织入：Spring生成代理对象的过程
切点类有接口时，采用JDK动态代理
切点类没有接口时，当然采用CGLIB动态代理

3.3.7 给通知传递参数 
指示器之 args()
连接点 指示器间使用&&连接

3.3.8 引入：
代理对象挂在多个接口之下，现为该代理对象添加新接口（得到更多实现的方法）
@DeclareParents 修饰 切面类的一个该接口类型的新属性
value = "全限定类名+"（表示增强）
defaultImpl=新增实现类全限定名

#### 3.4 使用XML配置开发Spring AOP

1.切面Bean 2.aop:config 3.aop:aspect 4.aop:before/after/afterReturning/afterThrowing
5.aop:pointcut 6.aop:declare-parents 

#### 3.5 多个切面

在切面上@Order注解	（采用了责任链模式处理）
xml:<aop:aspect ref=... order="1">

### 四.Spring和数据库编程

#### 4.1. 配置数据库资源

4.1.1 简单数据库配置 SimpleDriverDataSource
4.1.2 第三方数据库连接池 jdcp

#### 4.2 jdbcTemplate

通过数据源DataSource配置jdbcTemplate Bean
执行单条SQL : query 和 update 方法
执行多条SQL：execute方法使用回调接口ConnectionCallback和StatementCallback

#### 4.3 MyBatis-Spring项目

4.3.1 配置SqlSessionFactroyBean

4.3.2 配置SqlSessionTemplate

4.3.3 配置MapperFactoryBean
两个属性 ： Mapper接口 和 SqlSessionFactory/SqlSessionTemplate(SqlSessionTemplate优先)
配置后，直接通过Spring IOC容器获取Mapper

4.3.4 配置MapperScannnerConfigurer
应对Mapper接口数量众多的问题
主要属性：
1.basePackage 
2.annotationClass （@Repository标记在接口上）：org.springframework.stereotype.Repository
3.SqlSessionFactroyBeanName 
4.markerInterface 不常用，扩展一个只用作标记的接口(例：BaseMapper）

### 五.深入Spring数据库事务管理

#### 5.1 事务管理器PlatformTransactionManager 和 TransactionTemplate

接口基本三方法：getTransaction commit rollback 
5.1.1 配置事务管理器 MyBatis常用DataSourceTransactionManager
5.1.2 Java配置 @EnableTransactionManagement 接口

#### 5.2 编程式事务 

接口TransactionDefinition 和 实现类DefaultTransactionDefinition

#### 5.3 声明式事务 （分为XML和注解）

5.3.1 Transactional配置
@Transactional 重要属性propagation isolation timeout

#### 5.4 数据库相关

5.4.1 数据库事务特性 ACID
5.4.2 丢失更新  第一类丢失更新：一事务先提交 一事务后回滚	第二类丢失更新
5.4.3 隔离级别
 1.脏读 事务可以访问另一事务未提交的数据
 2.读/写提交 只能读写已提交的数据 （不可重复读出现）
 3.可重复读 （出现幻读）
 4.序列化
5.4.4  7大传播行为   （当方法A调用方法B时，它们的事务变化）
PROPAGATION_REQUIRED 有事务，加入这个事务；无事务，开一条事务
PROPAGATION_SUPPORTS 有事务，加入这个事务；无事务，无事务执行
PROPAGATION_MANDATORY 有事务，加入这个事务；无事务，抛出异常
PROPAGATION_REQUIRES_NEW 必开启一条事务；有事务，挂起该事务（JtaTransactionManager）
PROPAGATION_NOT_SUPPORTED 无事务执行；有事务，挂起该事务（JtaTransactionManager）
PROPAGATION_NEVER 无事务执行；有事务，抛出异常
PROPAGATION_NESTED 嵌套事务执行；内层依赖外层

6.mybatis+springIOC+springAOP+spring事务
<context:annotation-config>
<context:component-scan base-package = " ">
<tx:annotation-driven transaction-manager="  ">  注册 事务管理器 注解驱动

7.@Transactional 注解失效
实现原理：AOP （动态代理），故static和not public的方法会失效
自调用：一个类的一个方法调用另一个方法，造成不存在代理对象的调用
解决之道：两个方法，两个服务类；方法调用时采用Spring IOC的代理对象（但是不符合设计原则）