- #### 应用切面编程(AOP)
```
1.AOP(aspect-oriented programming)：允许你把散落应用各处的功能分离出来形成可重用的组件
借助AOP可以将遍布系统的关注点从应用的对象中解耦出来
2.描述切面的术语：
* 通知(advice):定义了切面必须完成的工作，以及何时触发执行
  Spring切面可以应用5种类型的通知：
  前置通知(before):在目标方法调用之前调用通知功能
  后置通知(after):在目标方法完成之后通用通知，不关心方法的输出是什么
  返回通知(after-returning):在目标方法成功执行之后调用通知
  异常通知(after-throwing):在目标方法抛出异常后调用通知
  环绕通知(around):通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为
* 连接点(join point):在应用执行过程中能够应用通知的所有点，可以是调用方法时，抛出异常时,构造方法或设值属性时
* 切点(pointcut):有助于缩小切面所通知的连接点范围，切点的定义通知被应用的具体位置（在那些连接点），定义那些连接点会得到通知
* 横切关注点(cross-cutting concern):散布于应用中多处的功能称为横切关注点
* 切面(aspect):横切关注点可以被模块化为特殊的类，这些类称为切面
              切面是通知和切点的结合
              切面在指定的连接点被织入到目标对象中
* 引入(introduction):在无需修改现有类的情况下，向类添加新方法或属性，让类具有新的行为和状态
* 织入(weaving):把切面应用到目标对象并创建新的代理对象的过程
  在目标对象的生命周期有多个点可以进行织入：
  (1)编译期：切面在目标类编译时被织入
  (2)类加载期：切面在目标类加载到jvm时被织入，需要特定的类加载器，在目标类被引入之前增强该目标类的字节码
  (3)运行期：切面在应用运行的某个时刻被织入，在织入切面时，AOP容器会为目标对象动态创建一个代理对象
```

- #### Spring对AOP的支持
```
1.SpringAOP构建在动态代理基础之上，Spring对AOP的支持局限于方法拦截
2.Spring提供类4种类型的AOP支持：
* 基于代理的经典SpringAOP
* 纯POJO切面
* @AspectJ注解驱动的切面
* 注入式AspectJ切面
3.SpringAOP的关键知识：
* Spring通知是标准的java类编写的，定义通知应用的切点通常会使用注解或在Spring配置文件中采用XML来编写
* Spring在运行时通知对象
  通过在代理类中包裹切面，Spring在运行期把切面织入到Spring管理的bean中
  当代理拦截到被通知方法的调用，在调用目标bean方法前，会先执行切面逻辑
* Spring只支持方法级别的连接点
   因为Spring基于动态代理，所以Spring只支持方法连接点
   如果需要方法拦截之外的连接点拦截功能，可以利用Aspect来补充SpringAOP的功能
```

- #### 使用注解创建切面
```
在SpringAOP中，使用AspectJ的切点表达式语言来定义切点
Spring仅支持AspectJ切点指示器的一个子集
```

AspectJ指示器 | 描述
---|---
arg() | 限制连接点匹配参数为指定类型的执行方法
@arg()  | 限制连接点匹配参数由指定注解标注的执行方法
excution() | 用于匹配是连接点的执行方法
this() | 限制连接点匹配AOP代理的bean引用为指定类型的类
target | 限制连接点匹配目标对象为指定类型的类
@target | 限制连接点匹配特定的执行对象，这些对象对应的类要具有指定类型的注解
within() | 限制连接点匹配指定的类型
@within() | 限制连接点匹配指定注解所标注的类型
@annotation | 限定匹配带有指定注解的连接点

```
@AspectJ注解表示POJO类为一个切面
AspectJ提供5个注解来定义通知：
* @After：通知方法会在目标方法返回或抛出异常后调用
* @AfterReturning：通知方法会在目标方法返回后调用
* @AfterThrowing：通知方法会在目标方法抛出异常后调用
* @Around：通知方法会将目标方法封装起来
* @Before：通知方法会在目标方法调用之前执行
@Pointcut注解能够在一个@AspectJ切面内定义可重用的切点
在使用JavaConfig时，在配置类上通过使用EnableAspectJ-AutoProxy注解启用自动代理功能
```

```
@AspectJ
public class AspectDemo {
    
    @Pointcut("execution(** com.example.AspectClass.method(..))")
    public void pointCut() {
        
    }
    
    @Before("pointCut()")
    public void beforeMethod(){
        
    }
    
    @After("pointCut()")
    public void afterMethod(){
        
    }
}
```

- #### 在XML声明切面 
```
在Spring的aop命名空间中，提供了多个元素用来在XML中声明切面，而不需要使用注解
```

AOP配置元素 | 用途
---|---
<aop:config> | 顶层的AOP配置元素，大多数的<aop:*>元素必须包含在<aop:config>元素中
<aop:aspectj-autoproxy> | 启用@AspectJ注解驱动的切面
<aop:advisor> | 定义AOP通知器
<aop:after> | 定义AOP后置通知（不管被通知的方法是否执行成功）
<aop:after-returning> | 定义AOP返回通知
<aop:after-throwing> | 定义AOP异常通知
<aop:around> | 定义AOP环绕通知
<aop:aspect> | 定义一个切面
<aop:before> | 定义一个AOP前置通知
<aop:declare-parents> | 以透明的方式为被通知的对象引入额外的接口
<aop:pointcut> | 定义一个切点

```
<aop:config>
 <aop:aspect ref="aspcetDemo">
  <aop:pointcut id="pointCut" expression="excution(** com.example.AspectClass.method(..)))" />
  <aop:before pointcut-ref="pointCut" method="beforeMethod" />
  <aop:after pointcut-ref="pointCut" method="afterMethod" />
 </aop:aspect>
</aop:config>
```

