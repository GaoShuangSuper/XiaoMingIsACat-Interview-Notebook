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

- `ConfigureKestrel`可提供的配置 , 我列举了一些个人感觉比较重要的在下面, 如需查看其他的配置可以点[这里](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2#kestrel-options).
    - 这些配置都是配置于`KestrelServerOptions`这个class的实例上.

        - `MaxConcurrentConnections` 默认是null,即不限制数量
            >options.Limits.MaxConcurrentConnections = 100;

        - `MaxRequestBodySize` 默认是 30,000,000 bytes,大约28.6MB
            >options.Limits.MaxRequestBodySize = 10 * 1024;

        - `MinRequestBodyDataRate` 每秒传输的数据大小最小为多少(bytes), 低于这个值会timeout, gracePeriod 是一个宽限期, 当client最初建立tcp链接时发送数据的速率低于前面设置的值也可以接受. 超过宽限期之后, MinRequestBodyDataRate会生效. 默认是240 bytes/second,   5 second grace period.
            >options.Limits.MinRequestBodyDataRate =
            new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        options.Limits.MinResponseDataRate =
            new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));

        - `MaxStreamsPerConnection` 限制每个HTTP / 2连接的并发请求流数量。多余的流被拒绝。
            >options.Limits.Http2.MaxStreamsPerConnection = 100;

        - `ConfigureEndpointDefaults(Action<ListenOptions>)` 配置Endpoint.
            ```csharp
            options.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
            ```
        - `ConfigureHttpsDefaults(Action<HttpsConnectionAdapterOptions>)`
        ```csharp
        options.ConfigureHttpsDefaults(httpsOptions =>
            {
                // certificate is an X509Certificate2
                httpsOptions.ServerCertificate = certificate;
            });
        ```
        - `ListenOptions`
            - `UseHttps` 配置Kestrel以使用HTTPS和默认证书。如果未配置默认证书，则引发异常。
            - [For more](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2#listenoptionsusehttps)

>关于服务器Kestrel的其他配置还需要进一步研究和实践中进行总结.\
update at 2019-4-29 18:21:15

