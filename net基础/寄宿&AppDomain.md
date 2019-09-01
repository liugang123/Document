```
寄宿(Hosting):使任何程序都能利用CLR的功能，使现有应用程序能部分使用托管代码编写，
             并为应用程序提供了通过编程进行自定义和扩展的能力
AppDomain:允许第三方的、不受信任的代码在现有进程中运行，CLR保证
          数据结构、代码和安全上下文不被滥用或破坏
```

- #### CLR寄宿
```
* 任何windows应用程序都能寄宿（容纳）CLR
* MSCorEE.dll，垫片，在c:\windows\system32目录中，决定创建那个版本的CLR
* 一台机器可安装多个版本的CLR，但只有一个版本的垫片文件
* 通过垫片文件宿主应用程序可以做如下事情：
  1.设置宿主管理器：内存分配、线程调度/同步、程序集加载
  2.获取CLR管理器
  3.初始化并启动CLR
  4.加载程序集并执行其中的代码
  5.停止CLR，阻止更多的托管代码在windows进程中运行
```

- #### AppDomain
```
* AppDomain是一组程序集的逻辑容器，提供隔离而设计
* CLR初始化时会创建一个默认的AppDomain，在windows进程结束时才会销毁
* 使用非托管的com接口方法或托管类型方法的宿主还可要求CLR创建额外的AppDomain
* AppDomain的具体功能：
  1.一个AppDomain的代码不能直接访问另一个AppDomain的代码创建的对象
    这种隔离可以使AppDomian很容易的从进程中卸载
  2.AppDomain可以卸载，
    卸载AppDomian将卸载所有程序集
  3.AppDomian可以单独保护
    AppDomian创建后会应用一个权限集，当宿主加载一些代码后可以保证这些代码不会破坏宿主本身使用的一些数据结构 
  4.AppDomain可以单独配置
```

- #### Asp.Net 使用AppDomain
```
* Asp.Net作为IsApi的实现，首次请求有这个dll处理URL时，Asp会加载CLR，
  CLR会加载web应用虚拟根目录下的所有程序集
* 客户端请求不同的web应用，CLR会在工作进程中创建新的AppDomian
  多个AppDomain在同一个Windows进程中运行
* 当一个web应用的文件在磁盘上发生改动时，asp会卸载旧版本的AppDomain，创建新的AppDain（想象复制）  
```
