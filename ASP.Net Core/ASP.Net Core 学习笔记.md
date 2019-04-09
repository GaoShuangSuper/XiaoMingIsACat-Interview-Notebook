<font face="Microsoft YaHei"/>

# ASP.Net Core 学习笔记

## 2.2添加的新内容

- `OpenAPI(Swagger)`

支持OpenAPI这个东西, 过去被称为Swagger, 是一种非常强大的自动化生成RESTfull API文档的工具. 感觉这个东西已经成为一个比较时髦的技术了, 需要抽时间了解一下.

- `ProblemDetails`

简单来说这个东西是展示一个http请求如果出现了错误, 在http的response中会显示错误的细节. 而不是单纯的一个错误code代码(类似于之前的4xx这种若干). 而且这个东西居然也和Swagger相关, 这就看出来这个B东西管的有多宽了. 看来不搞懂Swagger,都不好意思说我会ASP.net了.

- `EndPoint Routing`

2.2又增添了一种routing system, 为了提升request的分发的. 看来以后学完还得回来给多做解释了
