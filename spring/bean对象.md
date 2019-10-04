- #### 对象容器
```
Spring容器(container)：负责创建对象，装配对象，配置对象，管理对象的这个生命周期
Spring容器使用DI管理构造应用的组件，它会创建相互协作的组件之间的依赖关系
Spring容器负责把需要相互协作的对象引用赋予各个对象
Spring容器类型：
* bean工厂：最简单的容器，提供基本的DI支持
* 应用上下文：基于BeanFactory构建，并提供应用框架级别的服务
```

- #### 应用上下文
```
* AnnotationConfigApplicationContext:从一个或多个基于java的配置类中加载Spring应用上下文
* AnnotationConfigWebApplicationContext：从一个或多个基于java的配置类中加载Spring Web应用的上下文
* ClassPathXmlApplicationContext：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类文件
* FileSystemXmlApplicationContext：从文件系统下的一个或多个XML配置文件中加载上下文定义
* XmlWebApplicationContext：从web应用下的一个或多个xml配置文件中加载上下文定义
```

- #### 依赖注入
```
DI(DependencyInjection)能够让相互协作的软件组织保持松散耦合
1.装配(wiring)：创建应用对象之间协作关系的行为称为装配，依赖注入的本质就是装配
2.装配机制：
* 在XML文件中进行显示配置
* 在Java中进行显式配置
* 隐式的bean发现机制和自动装配
3.Spring应用上下文：全权负责对象的创建和组装，Spring提供的多种应用上下文主要区别在于如何加载配置
classPathXmlApplicationContent：加载xml文件配置的bean
```

- #### 自动装配bean
```
Spring从两个角度实现自动化装配：
* 组件扫描(component scanning)：Spring会自动发现应用上下文中创建的bean
* 自动装配(autowiring)：Spring自动满足bean之间的依赖
组件扫描和自动装配组合在一起可以将显示配置降低到最少
@Component
@ComponentScan
public class Demo {
    
}
@Component注解表明该类会作为组件类
@ComponentScan注解启动组件扫描，因为组件扫描默认不启用
@ComponentScan注解默认扫描与配置类相同的包，以及这个包下的所有子包，查找带有@Component注解的类，并在Spring中自动为其创建一个bean
使用xml来启用组件扫描：
Spring context命名空间的<context:component-scan>
@autowired注解声明要进行自动装配，让Spring自动满足bean依赖的一种方法
如果没有匹配的bean装配，在应用上下文创建时，spring会抛出异常
可以将@autowried的required属性设置为false，使Spring尝试进行自动装配，如果还没有匹配的bean，Spring将设置这个bean的状态为‘未装配’
```

- #### XML装配bean
```
* 在XML配置中，要创建一个XML文件，并且要已<beans>元素为根
* 通过<bean>元素声明一个bean，借助ID属性可以为bean命名
* 构造器注入方式：
  <constructor-arg>元素
  使用Spring3.0所引入的c-命名空间
* 通过<property>元素实现属性注入，p-命名空间是简洁的替代方案  
* 通常会创建一个根配置(root configuration)，通过<import>元素将更多的XML文件组合起来，通常在根配置中开启组件扫描<context:component-scan>
```
- #### 运行时值注入
```
1.Spring提供了两种运行时求值的方式：
* 属性占位符(Property placeholder)
* Spring表达式语言(SpEL)
2.占位符的形式为${属性名称}
3.Spring的Environment会加载属性文件，用来检索属性，加载属性源
4.可以通过PropertySourcePlaceholderConfigurer基于Environment将其属性源来解析占位符
5.在XML配置中，为了解析占位符，还需要引用context命名空间的元素来生产PropertySourcePlaceholderConfigurer对象
 <context:propertyplacehlder>
```

- #### bean的作用域
```
默认情况下，Spring应用上下文中的所有bean都是作为单例形式创建的
Spring定义了多种作用域，可以基于这些作用域创建bean
* 单例(singleton):在整个应用中只创建bean的一个实例
* 原型(prototype):每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例
* 会话(session):在web应用中为每个会话创建一个bean实例
* 请求(request):在web应用中为每个请求创建一个bean实例
创建作用域代理的两种方式：
* 基于类的代理：使用CGLib来生成基于类的代理
* 基于接口的代理：通过实现接口生成类的代理
```

- #### bean的生命周期
```
graph LR
实例化-->填充属性
填充属性-->调用BeanNameAware的setBeanName方法
调用BeanNameAware的setBeanName方法-->调用BeanFactoryAware的setBeanFactory方法
调用BeanFactoryAware的setBeanFactory方法-->调用applicationContextAware的setApplicationContext方法
调用applicationContextAware的setApplicationContext方法-->调用BeanPostProcessor的预初始化方法
调用BeanPostProcessor的预初始化方法-->调用InitializingBean的afterPropertiesSet方法
调用InitializingBean的afterPropertiesSet方法--> 调用自定义初始化方法
调用自定义初始化方法-->调用BeanPostProcessor的初始化后方法
```



