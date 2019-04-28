# 解读 ASP.NET Core中的Kestrel

>参考自 [https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)

- 首先你需要知道的是 Kestrel asp.net core的内置的默认的一个可跨平台的web 服务器.包含在 asp.net core 2.1 及以后的版本中.
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428235003605-499364164.png)

- 它可以和反向代理(reverse server)结合使用, 比如IIS, Nginx, Apache. 这些反向代理会接收http request并转发这些request到kestrel.
    ![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428235036667-1115423996.png)

- 推荐使用反向代理
    - 反向代理可以很好地避免将应用程序暴露给外部;
    - 提供额外的配置和防护;
    - 可能会更好的与现有的基础架构进行集成;
    - 简化负载平衡和安全通信（HTTPS）配置。只有反向代理服务器需要X.509证书，并且该服务器可以使用纯HTTP与内部网络上的应用服务器通信。

- 在 asp.net core中默认使用 kestrel. 默认在 `CreateDefaultBuilder` 方法内部调用. 并且提供了可配置项, 如下代码:
    ```csharp
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        })
        .Build();

        host.Run();
    }
    ```

- `ConfigureKestrel`可提供的配置
    - 这些配置都是配置于`KestrelServerOptions`这个class的实例上.
    ```csharp
    // MaxConcurrentConnections 默认是null,即不限制数量
    // MaxRequestBodySize 默认是 30,000,000 bytes,大约28.6MB
    .ConfigureKestrel((context, options) =>
    {
        options.Limits.MaxConcurrentConnections = 100;
        options.Limits.MaxConcurrentUpgradedConnections = 100;
        options.Limits.MaxRequestBodySize = 10 * 1024;
        options.Limits.MinRequestBodyDataRate =
            new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        options.Limits.MinResponseDataRate =
            new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        options.Listen(IPAddress.Loopback, 5000);
        options.Listen(IPAddress.Loopback, 5001, listenOptions =>
        {
            listenOptions.UseHttps("testCert.pfx", "testPassword");
        });
    });
    ```
