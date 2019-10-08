- #### 创建Servlet
```
1.Servlet是一个运行在Web服务器中的java小程序，Servlet将会接受和响应Web客户端的请求，使用HTTP(超文本传输协议)进行通信
2.所有Servlet都默认实现了javax.servlet.Servlet接口
public interface Servlet {
    void init(ServletConfig var1) throws ServletException;
    ServletConfig getServletConfig();
    void service(ServletRequest var1,ServletResponse var2) throws ServletException,IOException;
    String getServletInfo();
    void destroy();
}
3.多数情况下，Servlet都继承自响应http请求的HttpServlet抽象类，它提供了响应每种HTTP请求类型的方法空实现
```

- HttpServlet在针对Http方法类型的方法实现

Http请求类型 | Servlet方法 | 描述
---|--- |---
GET | doGet() | 从指定的URL中获取资源
HEAD | doHead() | 与GET一致，该请求只返回页面的头部数据
POST | doPost() | 通常用于处理Web表单的提交
PUT | doPut() | 存储URL中提供的实体
DELETE | doDelete() | 删除URL标示的资源
OPTIONS | doOptions() | 返回支持的HTTP方法
TRACE | doTrace() | 用于诊断目的

- 继承自HttpServlet的Servlet
```
public class ExampleServlet extends HttpServlet {
    
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
        
    }
    
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
        
    } 
}
```

- #### 配置Servlet

- #### Servlet生命周期 

- #### Servlet内置对象 

1. out 输出流对象
 
header 1 | header 2
---|---
row 1 col 1 | row 1 col 2
row 2 col 1 | row 2 col 2

2. request 请求对象

header 1 | header 2
---|---
row 1 col 1 | row 1 col 2
row 2 col 1 | row 2 col 2

3. response 响应对象

header 1 | header 2
---|---
row 1 col 1 | row 1 col 2
row 2 col 1 | row 2 col 2

4. config 配置对象

header 1 | header 2
---|---
row 1 col 1 | row 1 col 2
row 2 col 1 | row 2 col 2

5. session 会话对象

header 1 | header 2
---|---
row 1 col 1 | row 1 col 2
row 2 col 1 | row 2 col 2

6. application 应用程序对象

header 1 | header 2
---|---
row 1 col 1 | row 1 col 2
row 2 col 1 | row 2 col 2



- #### Servlet线程安全 

