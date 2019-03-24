# Asp.net core + docker 架构

## 容器简介

容器化是软件开发的一种方法, 通过这种方法可以将应用程序, 服务, 其依赖项, 及其配置(配置会抽象成部署清单文件)一起打包成为容器映像.容器化得应用程序可以作为一个单元进行测试. 并可以作为容器映像实例部署到操作系统当中.

![在一个容器主机上运行多个容器](https://img2018.cnblogs.com/blog/1216080/201903/1216080-20190321234040544-2030646621.png)

容器在整个应用程序生命周期工作流中提供以下优点：隔离性、可移植性、灵活性、可伸缩性和可控性。 最重要的优点是可在开发和运营之间提供隔离。

## 什么是Docker

Docker官网:[https://www.docker.com/](https://www.docker.com/)

Docker源代码:[https://github.com/docker/docker](https://github.com/docker/docker)

Docker 是一种开源项目，用于将应用程序自动部署为可在云或本地运行的便携式独立容器。 Docker 也是一家公司，它与云、Linux 和 Windows 供应商（包括 Microsoft）协作，致力于推广和发展这项技术。

![Docker 在混合云的所有层部署容器](https://img2018.cnblogs.com/blog/1216080/201903/1216080-20190321234809179-1995419508.png)

关于docker的相关概念可以参考MSDN的doc: [Docker术语](https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)

下表描述了不同的体系结构/应用类型,在不同的系统容器中所适用的技术:

体系结构/应用类型 | Linux容器 | Windows容器
-----|-----|-----
容器上的微服务 |.NET Core |.NET Core
单一应用程序 | .NET Core |.NET Framework .NET Core
一流性能和可扩展性 |.NET Core |.NET Core
到容器的 Windows Server 旧应用程序（“棕色字段”）迁移 | -- |.NET Framework
基于容器的新开发（“绿色字段”）| .NET Core |.NET Core
ASP.NET Core |.NET Core |.NET Core（推荐）.NET Framework
ASP.NET 4（MVC 5、Web API 2 和 Web 窗体）| -- |.NET Framework