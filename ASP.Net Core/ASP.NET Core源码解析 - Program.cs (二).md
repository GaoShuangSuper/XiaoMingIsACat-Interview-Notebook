# ASP.NET Core源码解析 - Program.cs (二)

<font face="microsoft yahei">

## 目录

[ASP.Net Core 学习笔记](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/ASP.Net%20Core%20%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md)

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
1. [创建 WebHostBuilder 对象](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/ASP.NET%20Core%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%20-%20Program.cs%20(%E4%B8%80).md#%E5%88%9B%E5%BB%BA-webhostbuilder-%E5%AF%B9%E8%B1%A1)
2. [让这个 WebHostBuilder 对象 build一个 webhost 并run起来](https://github.com/itdennis/XiaoMingIsACat-Interview-Notebook/blob/master/ASP.Net%20Core/ASP.NET%20Core%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%20-%20Program.cs%20(%E4%BA%8C).md#%E8%AE%A9%E8%BF%99%E4%B8%AA-webhostbuilder-%E5%AF%B9%E8%B1%A1-build%E4%B8%80%E4%B8%AA-webhost-%E5%B9%B6run%E8%B5%B7%E6%9D%A5)

### 让这个 WebHostBuilder 对象 build一个 webhost 并run起来

```csharp
.Build().Run();
```
紧跟着`webhostbuilder`创建成功后, 代码执行 `.Build()`
>Builds an Microsoft.AspNetCore.Hosting.IWebHost which hosts a web application.

- 解析 `.Build()` 干了什么.
