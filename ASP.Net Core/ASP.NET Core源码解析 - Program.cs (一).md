<font face="microsoft yahei">

# ASP.NET Core源码解析 - Program.cs (一)

## 目录

[ASP.Net Core 学习笔记](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/ASP.Net%20Core%20%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md)

## 创建一个 asp.net core的项目

1. 参考微软文档吧, 很详细: [https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc?view=aspnetcore-2.2&tabs=visual-studio](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc?view=aspnetcore-2.2&tabs=visual-studio)

2. 创建完后会有个web项目, 打开后我这面的层级目录如下:

    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428160442326-1514188724.png)

## 代码解析

```csharp
    //为什么关注这个类, 因为这里有main函数, 一般来说main函数都是程序启动的时候的启动类. 看一下这行代码:
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[]args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>();
    }
```

基本上做了两件事, 在下面我将分两个section解读这两部分:
1. [创建 WebHostBuilder 对象](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/ASP.NET%20Core%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%20-%20Program.cs%20(%E4%B8%80).md#%E5%88%9B%E5%BB%BA-webhostbuilder-%E5%AF%B9%E8%B1%A1)
2. [让这个 WebHostBuilder 对象 build一个 webhost 并run起来](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/ASP.NET%20Core%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%20-%20Program.cs%20(%E4%BA%8C).md#%E8%AE%A9%E8%BF%99%E4%B8%AA-webhostbuilder-%E5%AF%B9%E8%B1%A1-build%E4%B8%80%E4%B8%AA-webhost-%E5%B9%B6run%E8%B5%B7%E6%9D%A5)

### 创建 WebHostBuilder 对象

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>();
```

1. 分开来看, 先看第一部分 `WebHost.CreateDefaultBuilder(args)`

    那么看一下 `WebHost`, 哦, 底层的一个类, 长得大概是这个模样.
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428161853509-1998333969.png)

    打开源码文件路径(..\AspNetCore-2.2.4\src\DefaultBuilder\src), 在如图所示找到了源码文件:
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428162243306-804666607.png)

    我们来看一下 `CreateDefaultBuilder` 这个方法干嘛了,篇幅有限我这里不贴出全部代码, 
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428163103164-796131209.png)

    至于这个 `UseKestrel` 我当然也找到了. 贴到下面:
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428164045290-1649662780.png)

    如果想了解一下这个 `Kestrel` , 参考这篇吧 [解读ASP.NET Core中的Kestrel.md](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/%E8%A7%A3%E8%AF%BB%20ASP.NET%20Core%E4%B8%AD%E7%9A%84Kestrel.md)

    然后是其内部的代码逻辑:
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190429182444829-1410273946.png)

    >那么我们来回顾和总结一下 `CreateDefaultBuilder` 所做的工作:\

        1. 创建一个webhostbuilder. 
        2. 对服务器进行各项配置

2. 再看第二部分 `.UseStartup<Startup>();`

    - 我们直接F12
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190429184746980-269996555.png)
        当然这些不够, 谁知道 底层用startup干嘛了
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190429185320826-55016933.png)
    继续看, 
        - `.UseSetting(WebHostDefaults.ApplicationKey, startupAssemblyName)` 注册了一个key & value
            ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190429190243325-1187487284.png)
        - `.ConfigureServices(Action<IServiceCollection> configureServices)` 实际上也是对list集合进行一个缓存操作,
        `private readonly List<Action<WebHostBuilderContext, IServiceCollection>> _configureServicesDelegates;`
        ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190429190155585-2070417961.png)
    
那么`webhostbuilder`创建出来后, 里面有两个缓存, 对startup的类型进行缓存之外, 还对startup的services进行缓存. 至于有什么作用, 需要后文解读. 







