#### 1.动态代理

1. JDK动态代理 (java.lang.reflect.*)

   1. 借助接口，并实现它
   2. 代理类实现InvocationHandler
   3. Proxy类和它的静态方法newProxyInstance，创建代理对象
   4. 代理方法逻辑,invoke方法
   5. 代码解析

   ```java
   public class ServiceProxy implements InvocationHandler {
       //真实对象
       private Object target;
   
       public Object bindProxy(Object target){
           this.target = target;
           return Proxy.newProxyInstance(this.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
           //建立代理对象和真实对象的关系
       }
   
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           System.out.println("before you say hello");
   
           Object invoke = method.invoke(target, args);
   
           System.out.println("after you say hello");
           return invoke;
       }
   }
   ```

#### 2.再看SpringAOP

前言：AOP常见使用场景 - 

```
Authentication 权限
Caching 缓存
Context passing 内容传递
Error handling 错误处理
Lazy loading　懒加载
Debugging　　调试
logging, tracing, profiling and monitoring　记录跟踪　优化　校准
Performance optimization　性能优化
Persistence　　持久化
Resource pooling　资源池
Synchronization　同步
Transactions 事务
```

1. 动态代理加拦截器 约等于 SpringAOP

   我的代码

   ```java
   @Override
   public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
       //调用方法前
       this.interceptor.before(target);
       Object invoke = null;
       try{
           invoke = method.invoke(target, args);
           //成功调用方法
           this.interceptor.afterRunning(target);
       }catch (Exception e){
           //方法调用出现异常
           this.interceptor.afterThrowing(target);
           throw e;
       }finally {
           //方法调用无论是否有异常
           this.interceptor.after(target);
       }
       return invoke;
   }
   ```

2. 面向切面编程的重要术语

   1. Aspect 切面

      在动态代理中，可以理解为一个拦截器。

      可以在被代理对象的之前、之后切入我们的代码

   2. Adice 通知

      切面(拦截器)开启后，它的每个方法。

      before 前置通知：...

      after 后置通知：...

      afterReturning 返回通知: 如上面的afterRunning

      afterThrowing 异常通知：...

      aroundThrowing 环绕通知：取代当前被拦截对象的方法，通过参数和反射调用被拦截对象的方法

   3. introduction 引入

      允许在现有的类添加自定义的类和方法

   4. pointcut 切点 

      被切面拦截的方法称为 切点 

   5. join point 连接点

      一个判断条件，通过它可以指定切点，指定后Spring会产生代理对象，使用对应的切面拦截这个切点。

   6. weaving 织入 

      生成代理对象的过程。

3. Spring实现AOP的方式

   1. @AspectJ注解开发 (主流)

      1. 切点选择

      2. 创建切面 @Aspect

      3. 编写通知

         @Before @After @Around @AfterReturning @AfterThrowing

      4. 连接点 指定切点

         execution正则表达式

         1. 示例

            @Before(execution(*com.yuan.service.impl.UserServiceImpl.getUsers(..)))

            *：代表任意返回类型

            (..)：任意参数

         2. AspectJ指示器

         3. @Pointcut

            避免重复书写AspectJ指示器

            ```java
            @Pointcut("execution(* com.yuan.service.impl.UserService.print(..))")
            public void print(){}
            
            //默认参数JoinPoint
            @Before("print()")
            public void before(JoinPoint jp){
               ..
            }
            
            @After("print()")
            public void after(JoinPoint jp){
                ..
            }
            
            @AfterReturning("print()")
            public void afterReturining(JoinPoint jp){
                ..
            }
            
            @AfterThrowing(value = "print()",throwing = "e")
            public void afterThrowing(Throwable e){
                ..
            }
            
            @Around("print()")
            public void around(ProceedingJoinPoint joinPoint){
                ..
            }
            ```

   2. 测试由ApsectJ实现的SpringAOP

      ```java
      @Configuraion
      @EnableAspectJAutoProxy //启	动Aspect的自动代理
      public class SpringConfig{
          @Bean //将Bean放入SpringIOC容器
          public MyAspect getMyAspect(){
              return new MyAspect();
          }
      }
      ```

      也可以由XML装载AspectJ

      ```xml
      <aop:aspectj-autoproxy/>
      <bean id="myAspect" class="com.yuan.aspect.MyAspect">
      ```

   3. 环绕通知

      SpringAOP功能最强大的通知

      可以同时实现before和after通知，也保留了调度被代理对象原有方法的功能。

      ```java
      @Around("print()")
      public void around(ProceedingJoinPoint jp){
      //   before ..
          jp.proceed(); //Spring提供参数，可用于反射切点方法
      //	after ..
      }
      ```

   4. 给通知传递参数

      使用args()指示器

      ```java
      //使用&&连接指示器
      @Before("execution(* com.yuan.service.impl.UserServiceImpl.listUserService(..))" + 
             "&& args(pageIndex,pageSize)")
      public void before(int pageIndex,int pageSize){
          ..
      }
      ```

   5. introduction 引入的实现

      使用场景：希望通过引入其他类的方法增强某个类。

      ```java
      //需要在切面中加入注解@DeclareParents()
      @DeclareParents(
          //加号表示对某个类进行增强
      	value="com.yuan.service.impl.UserServiceImpl+",
          //默认实现类
      	defaultImpl=UserVerifierImpl.class
      )
      ```

      1. 使用方法

         ```java
         @AutoWired
         UserService service;
         //通过强转后，调用增强类的方法
         UserVerifier verifier = (UserVerifier)service;
         ```

      2. 实现原理：动态代理中代理对象可以挂在多个接口上，故可以拓展增强接口

   6. 多个拦截器的使用

      重点：@Order注解

      在切面类上面使用@Order(1)/@Order(2)...
   
4. SpringBoot中使用AOP

   1. starter

      ```xml
      <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-aop</artifactId>
      </dependency>
      ```

   2. Application类

      ```java
      @EnableAspectJAutoProxy
      ```

   3. Aspect类

      ```
      @Aspect
      @Component
      ```

#### 3.再看Spring数据库事务管理

1. @Transactional

   <tx:annotation-driven transaction-manager="transactionManager">

   使用步骤：

   1. 注解在方法或类上
   2. 设置@Transactional属性，如隔离级别和传播行为，超时等(存放在TransactionDefinition)
   3. 依靠SpringAOP织入事务控制代码

2. 数据库事务特性

   ACID：原子性 一致性 隔离性 持久性

   丢失更新：

   ​	第一类：事务的回滚时间晚于另一事务的提交，导致数据不一致

   ​	第二类：事务之间无法探知各自的操作

3. 事务隔离级别

   **脏读** dirty read：

   ​	事务可以读到另一事务未提交的数据

   **读/写提交**read commit:

   ​	事务只能读到另一事务已提交的数据

   ​	存在问题：不可重复读，事务不能知道另一事务在提交前做了什么，二者共同控制的值是随时变化的

   可重复读repeatable read:

   ​	可重复读取是指在一个事务内，多次读同一个数据，在这个事务还没结束时，其他事务不能访问该数据(包括了读写)，这样就可以在同一个事务内两次读到的数据是一样的，因此称为是可重复读隔离级别，读取数据的事务将会禁止写事务(但允许读事务)，写事务则禁止任何其他事务(包括了读写)，这样避免了不可重复读和脏读，但是有时可能会出现幻读。(读取数据的事务)可以通过“共享读镜”和“排他写锁”实现。

   序列化: 锁表

4. 事务传播行为

   方法之间的调用事务策略