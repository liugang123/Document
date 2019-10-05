- #### Rest定义
```
1.Rest(Representational State Transfer):以信息和资源为中心的表述性状态转移
2.Rest并不是基于URL的web服务（作为另一种类型的远程过程调用(RPC)机制，通过简单的HTTP URL来触发，而不是使用SOAP大量的XML命名空间）
  Rest与RPC几乎没有任何关系，RPC是面向服务的，关注与行为和动作
  Rest是面向资源的，强调描述应用程序的事物和名词
3.Rest的构成部分：
  * 表述性(Representational):Rest资源可以用各种形式来进行表述，如XML，json，甚至HTML
  * 状态(State):当使用Rest时，更关注资源的状态而不是对资源采取的行为
  * 转移(Transfer):Rest涉及到转移资源数据，它以某种表述性形式从一个应用转移到另一个应用
4.Rest就是将资源的状态以最适合客户端或服务端的形式从服务端转移到客户端  
5.在Rest中，资源通过URL进行标示和定位
6.Rest会有行为和动作，通过HTTP方法通常会匹配CRUD动作：
  * Create：POST
  * Read：GET
  * Update：PUT或PATCH
  * Delete：DELETE
```

- #### Spring对REST的支持
```
Spring支持以下方式来创建REST资源：
1.控制器可以处理所有的HTTP方法：GET，PUT，DELETE，POST，PATCH
2.借助@PathVariable注解，控制器可以处理参数化的URL
3.借助Spring的视图和视图解析器，资源能够以多种方式进行表述(XML,JSON,HTML)
4.可以使用视图解析器ContentNegotatingViewResolver来选择最合适的客户端表述
5.借助@ResponseBody注解和各种HttpMethodConverter实现，能够替换基于视图的渲染方式
6.借助@RequestBody注解和各种HttpMethodConverter实现，可以将传入的HTTP数据转化为传入控制器处理方法的java对象
7.借助RestTemplate，Spring应用能够方便的使用REST资源
```

- #### 创建REST资源
```
Spring提供了两种方式将资源的java表述形式转换为发送给客户端的表述形式：
1.内容协商(content negotiation):选择一个视图，将模型渲染为呈现给客户端的表述形式
  内容协商的两个步骤：
  * 确定请求的媒体类型 
  * 找到适合请求媒体类型的最佳视图
  内容类型确定后，ContenNegotiatingViewResolver就将逻辑视图名解析为渲染模型的view
  ContenNegotiatingViewResolver本身不会解析视图，而是委托给其他的视图解析器，让它们来解析视图，解析到的每个视图都会放在一个列表中
  根据客户端请求的媒体类型，在视图列表中查找能够产生对应内容类型的视图
2.消息转化器(message conversion):通过消息转换器将控制器返回的对象转换为呈现给客户端的表述形式
  Spring提供来多个HTTP信息转换器，应用实现资源表述与各种java类型间的相互转换：
  * MappingJacksonHttpMessageConverter实现JSON消息和Java对象的相互转换
  Jackson JSON库在将返回的java对象转换为JSON资源表述时，默认会使用反射
  可以在java类型上使用jackson的映射注解，改变产生JSON资源表述的行为
  * Jaxb2RootElementHttpMessageConverter实现XML消息和Java对象的相互转换
3.为控制器默认设置消息转换
@RestController注解用于标示服务于资源的控制器，同时，Spring会为该控制器所有处理方法应用消息转换功能，
不必在每个方法都添加@ResponseBody注解

@RestController
@RequestMapping("/example")
public class ExampleRestController {
    
    @RequestMapping(value="/list",method=RequestMethod.GET)
    public List<ExampleVO> method1() {
        
    }  
    
    @RequestMapping(value="/save",method=HttpMethod.POST)
    public int method2(@RequestBody ExampleVO exampleVO) {
        
    }
    
} 
```