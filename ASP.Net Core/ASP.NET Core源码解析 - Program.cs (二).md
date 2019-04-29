# ASP.NET Core源码解析 - Program.cs (二)

<font face="microsoft yahei">

本篇旨在解读 asp.net core 的代码层面的知识点, 也是本人边学习源码边总结的一个过程. 如果有任何不对的地方请指出来, 全篇皆是个人观点, 仅供参考.

## 本篇文章在写作时的基础环境信息
>.net core 版本: 2.2\
vs版本: vs2019\
asp.net core 源码: [https://github.com/aspnet/AspNetCore/releases/tag/v2.2.4](https://github.com/aspnet/AspNetCore/releases/tag/v2.2.4)

## Links

[ASP.NET Core源码解析 - Program.cs (一)](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/ASP.NET%20Core%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90(%E4%B8%80)%20-%20Program.cs.md)

## 代码解析

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
1. [创建 WebHostBuilder 对象](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/ASP.NET%20Core%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90(%E4%B8%80)%20-%20Program.cs.md)
2. [让这个 WebHostBuilder 对象 build一个 webhost 并run起来]

### 让这个 WebHostBuilder 对象 build一个 webhost 并run起来

```csharp
.Build().Run();
```
紧跟着`webhostbuilder`创建成功后, 代码执行 `.Build()`
>Builds an Microsoft.AspNetCore.Hosting.IWebHost which hosts a web application.

- 解析 `.Build()` 干了什么.
