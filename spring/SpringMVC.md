- ####  请求处理过程

```
graph LR
请求--1-->DispatcherServlet
DispatcherServlet--2-->处理映射
DispatcherServlet--3-->控制器
控制器--4-->模型及逻辑视图名
模型及逻辑视图名-->DispatcherServlet
DispatcherServlet--5-->视图解析器
DispatcherServlet--6-->视图
视图--7-->响应
```

```
1.DispatcherServlet:前端控制器(front controller)，请求首先经过前端控制器
2.处理器映射(handler mapping):根据请求信息决定将请求分发给对应的控制器
3.控制器(controller):处理业务逻辑,打包模型数据(model),标示用于渲染输出的视图名
4.视图解析器(view resolver):将逻辑视图名匹配为特定的视图实现
5.视图(view):获取模型数据，进行视图渲染
```

- #### 配置SpringMVC
```
1.配置DispatcherServlet
  (1)在web.xml文件中配置Servlet
  <!-- 设置根上下文配置文件位置 -->
  <context-param>
   <param-name>contextConfigLocation</param-name>
   <param-value>/WEB-INF/spring/root-context.xml</param-value>
  </context-param>
  <!-- 注册ContextLoaderListener -->
  <listener>
   <listener-class>org.springframework.web.context.ContextLoaderListener</listener>
  </listener>
  <!-- 注册DispatchServlet -->
  <servlet>
   <servlet-name>appServlet</servlet-name>
   <servlet-class>org.springframework.web.servlet.DispatchServlet</servlet-class>
   <!-- 容器在启动时，就加载该servlet -->    
   <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
   <servlet-name>appServlet</servlet-name>
   <url-pattern>/</url-pattern>
  </servlet-mapping>
  (2)在Servlet容器中配置
  public class DemoWebAppInitialized extends AbstractAnnotationConfigDispatcherServletInitializer {
    @override
    protected String[] getServletMapping(){
        return new String[] { "/" };
    }
    @override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?> { RootConfig.class };
    }
    @override
    protected Class<?>[] getServletConfigClasses() {
        return new CLass<?> { WebConfig.class };
    }
  } 
* DiptcherServlet和ContextLoaderListener各自都会加载一个Spring应用上下文  
* DisptcherServlet加载包含Web组件的bean，如控制器、视图解析器、处理器映射
* ContextLoaderListener加载应用中的其他bean，这些bean通常是驱动应用后端的中间层和数据层组件
* 上下文参数contextConfigLocation指定一个XML文件的地址，它会被ContextLoaderListener加载
* DispatcherServlet会根据Servlet的名字在应用根目录找到一个文件，并基于该文件加载应用上下文
  也可以在Servlet上指定一个contextConfigLocation初始化参数，从指定上下文配置文件中加载bean:
  <servlet>
   <servlet-name>appServlet</servlet-name>
   <servlet-class>org.springframework.web.servlet.DisptcherServlet</servlet-class>
   <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
   </init-param>
   <load-on-startup>1</load-on-startup>
 </servlet>
* AbstractAnnotationConfigDispatcherServletInitializer会同时创建SipatcherServlet和ContextLoaderListener
2.启用SpringMVC
 * 在XML配置文件中通过<mvc>元素启用
   <mvc:annotation-driven />
 * 在javaConfig中通过@EnableWebMVC注解启用   
    @Configuration
    @EnableWebMvc
    public class WebConfig {
        
    }   
3.配置视图解析器
  Spring默认使用BeanNameView-Resolver，查找ID与视图名称匹配的bean，并且查找的bean要实现View接口
4.启用组件扫描
  Spring只能找到显式声明在配置类中的控制器
5.配置静态资源的请求
  * 在XML配置文件中通过<mvc>元素设置静态资源访问
  <!-- 不用Spring mvc进行解析处理 -->
  <mvc:default-servlet-handler>
  <servlet-mapping>
   <servlet-name>default</servlet-name>
   <url-pattern>*.js</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
   <servlet-name>default</servlet-name>
   <url-pattern>*.css</url-pattern>
  </servlet-mapping>
  * 在javaConfig中设置静态资源访问
  通过调用DefaultServletHandlerConfigurer的enable()方法将对静态资源的请求转发到Servlet容器中默认的Servlet上
```

- #### 创建控制器
```
1.创建控制器
  @Controller是一个构造型(stereotype)的注解，用来声明控制器
  @RequestMapping注解，value属性指定方法要处理的请求路径，可以接受一个String类型的数组，method属性细化了处理的HTTP请求方法
  @RequestMapping可以进行拆分，将其路径映射部分放到类级别上
2.传递数据模型到视图中
  public  String method(Model model) {
      model.addAttribute("key","value");
      retunn "viewName";
  }
  在请求方法中，可以通过model参数，以key-value对的形式，将数据模型传递给视图
3.接受请求参数
  Spring支持以多种方式将请求数据传递到控制器的方法中
  * 查询参数(Query Parameter)
  @ReruestParam注解应用处理器方法参数的接受，defaultValue属性用来设置默认值
  public String method(@RequestParam(value="arg1",defaultValue=1) arg1,@RequestParam(value="arg2" defaultValue="10") arg2) {
      return "viewName";
  }
  * 路径变量(Path Variable)
  @RequestMapping路径中支持添加占位符，占位符名称要用大括号({})括起来
  @RequestMapping(value="/{arg}",method=RequestMethos.GET)
  public String method(@PathVariable("arg") long arg,Model model) {
     model.addAttribute("arg",arg);
     return "viewName";
  }
  * 表单参数(Form Parameter)
  public String method (@Valid RequestInfo requestInfo,Errors errors) {
    if(errors.hasErrors()) {
        return "viewName";
    } 
    return "redirect:viemName";
  }
4.Java校验API提供了多个注解，来校验提交实体属性的值  
```

注解 | 描述
---|---
@AssertFalse | 所注解的元素必须是boolean类型，并且值是false
@AssertTrue | 所注解的元素必须是boolean类型，并且值必须是true
@DecimalMax | 所注解的元素必须是数字，并且值要小于或等于给定的BigDecimalString的值
@DecimalMin | 所注解的元素必须是数字，并且值要大于或等于给定的BigDecimalString的值
@Digits | 所注解的元素必须是数字，并且值必须有指定的小数位数
@Future | 所注解的元素必须是一个将来的日期
@Past | 所注解的元素必须是一个已过去的日期
@Max | 所注解的元素必须是数字，并且值要小于或等于给定的值
@Min | 所注解的元素必须是数字，并且值要大于或等于给定的值
@NotNull | 所注解的元素的值必须不能为null
@Null | 所注解的元素的值必须为null
@Pattern | 所注解的元素的值必须匹配给定的正则表达式
@Size | 所注解的元素值必须是string、集合或数组，并且长度要符合给定的范围

- #### 视图解析器
```
Spring自带了13个视图解析器，能够将逻辑视图名转换为物理实现
```

视图解析器 | 描述
---|---
BeanNameViewResolver | 将视图解析为Spring上下文中的bean，bena的ID与视图的名称相同
FreeMarkerViewResolver | 将视图解析为FreeMarker模板
InternalResourceViewResolver | 将视图解析为Web应用的内部资源（一般为JSP）
ResourceBundleViewResolver | 将视图解析为资源bundle
UrlBasedViewResolver | 直接根据视图的名称解析视图，视图的名称会匹配一个物理视图的定义
VelocityLayoutViewResolver | 将视图解析为Velocity布局，从不同的Velocity模板中组合页面
VelocityViewResolver | 将视图解析为Velcity模板
XmlViewResolver | 将视图解析为XML文件中的bean定义
XstlViewResolver | 将视图解析为XSLT转换后的结果
JasperReportsViewResolver | 将视图解析为JasperReports定义
TitlsViewResolver | 将视图解析为Apache Tile定义，tile ID与视图名称相同
ContentNegotiatingViewResolver | 通过考虑客户端需要的内容类型来解析视图，委托给另一个能够生产对应内容类型的视图解析器

- #### 异常处理
```
Spring提供了多种方式将异常转换为响应：
1.特定的Spring异常将会自动映射为指定的HTTP状态码
  如果DisptcherServlet无法找到合适处理请求的控制器方法，就会抛出NoSuchRequestHandlingMethodException异常，
  最终结果就是产生404状态码的响应(Not Found)
2.异常上可以添加@ResponseStatus注解，将其映射为某一个状态码
  @ResponseStatus(value=HttpStatus.NOT_FOUND,reason="Not Found")
  pubic class ExampleNotFoundException extends RuntimeException {
      
  }
3.在方法上添加@ExceptionHandler注解，使其用来处理异常
  @ExceptionHandler(ExampleNotFoundException.class)
  public String handleExampleException() {
    return "error/errView"  
  }
  注解@ExceptionHandler标注的方法能够处理同一个控制器类中所有处理器方法的异常
* 控制器通知类可以处理所有控制器中处理器方法所抛出的异常：
  @ControllerAdvice
  public class ExceptionHandleController {
    @ExceptionHandler(ExampleNotFoundException.class)
    public String handleExampleException() {
      return "error/errView"  
    }  
  }
```





