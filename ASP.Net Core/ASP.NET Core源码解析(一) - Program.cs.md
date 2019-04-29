# ASP.NET Core源码解析(一) - Program.cs

<font face="microsoft yahei">

本篇旨在解读 asp.net core 的代码层面的知识点, 也是本人边学习源码边总结的一个过程. 如果有任何不对的地方请指出来, 全篇皆是个人观点, 仅供参考.

## 本篇文章在写作时的基础环境信息
>.net core 版本: 2.2\
vs版本: vs2019\
asp.net core 源码: [https://github.com/aspnet/AspNetCore/releases/tag/v2.2.4](https://github.com/aspnet/AspNetCore/releases/tag/v2.2.4)

## 创建一个 asp.net core的项目

1. 参考微软文档吧, 很详细: [https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc?view=aspnetcore-2.2&tabs=visual-studio](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc?view=aspnetcore-2.2&tabs=visual-studio)

2. 创建完后会有个web项目, 打开后我这面的层级目录如下:
![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428160442326-1514188724.png)

## 代码解析

### Program.cs
```csharp
    //为什么关注这个类, 因为这里有main函数, 一般来说main函数都是程序启动的时候的启动类. 看一下这行代码:
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>();
    }
```

基本上做了两件事, 在下面我将分两个section解读这两部分:
1. 创建webhost对象
2. 让这个webhost对象run起来

### 创建webhost对象

 `public static IWebHostBuilder CreateWebHostBuilder(string[] args)` 这个方法返回了一个 `IWebHostBuilder` , 通过 `WebHost.CreateDefaultBuilder().UseStartup<Startup>();`

1. 分开来看, 先看第一部分 `WebHost.CreateDefaultBuilder(args)`

    那么看一下 `WebHost`, 哦, 底层的一个类, 长得大概是这个模样.
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428161853509-1998333969.png)

    打开源码文件路径(..\AspNetCore-2.2.4\src\DefaultBuilder\src), 在如图所示找到了源码文件:
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428162243306-804666607.png)

    我们来看一下 `CreateDefaultBuilder` 这个方法干嘛了,篇幅有限我这里不贴出全部代码, 
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428163103164-796131209.png)

    至于这个 `UseKestrel` 我当然也找到了. 贴到下面:
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428164045290-1649662780.png)

    如果想了解一下这个 `Kestrel` , 参考这篇吧 [解读ASP.NET Core中的Kestrel.md](.\解读 ASP.NET Core中的Kestrel.md)

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

### 让这个webhost对象run起来

紧跟着`webhostbuilder`创建成功后, 代码执行 `.Build()`
>Builds an Microsoft.AspNetCore.Hosting.IWebHost which hosts a web application.

不翻译了, f12看到的. 




