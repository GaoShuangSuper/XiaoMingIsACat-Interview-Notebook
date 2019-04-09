# Asp.net core + 微服务 + docker 架构

## 容器和Docker简介

容器化是软件开发的一种方法, 通过这种方法可以将应用程序, 服务, 其依赖项, 及其配置(配置会抽象成部署清单文件)一起打包成为容器映像.容器化得应用程序可以作为一个单元进行测试. 并可以作为容器映像实例部署到操作系统当中.

![在一个容器主机上运行多个容器](https://img2018.cnblogs.com/blog/1216080/201903/1216080-20190321234040544-2030646621.png)

容器在整个应用程序生命周期工作流中提供以下优点：隔离性、可移植性、灵活性、可伸缩性和可控性。 最重要的优点是可在开发和运营之间提供隔离。

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
SignalR 服务 |.NET Core 2.1 或更高版本 |.NET Framework.NET Core 2.1 或更高版本
WCF、WF 和其他旧框架 | .NET Core 中的 WCF（仅 WCF 客户端库） |.NET Framework.NET Core 中的 WCF（仅 WCF 客户端库）
Azure 服务的消耗 |.NET Core（最终所有 Azure 服务都将为 .NET Core 提供客户端 SDK）。|.NET Framework .NET Core（最终所有 Azure 服务都将为 .NET Core 提供客户端 SDK）。

## 构建基于微服务的容器化应用程序

### 容器设计原则

在容器模型中，容器映像实例表示单个`进程`。 将容器映像定义为进程边界，可以创建可用于对进程进行缩放或批处理的基元。

设计容器映像时，可在 Dockerfile 中看到 EntryPoint 定义。 这定义了一个进程，其生命周期控制容器的生命周期。 该进程完成，则容器的生命周期结束。 容器可以表示 Web 服务器等长时间运行的进程，但也可表示批处理作业等生存期较短的进程，这些进程以前可能已实现为 Azure WebJobs。

如果进程失败，则容器结束，Orchestrator 接管。 如果 Orchestrator 已配置为使五个实例保持运行，而其中一个实例失败，则 Orchestrator 会创建另一个容器实例，来替换失败的进程。 在批处理作业中，使用参数启动该进程。 进程完成，则工作完成。某些情况下，可能需要在单个容器中运行多个进程。 对于这种情况，因为每个容器只有一个入口点，所以在可根据需要启动任意数目的程序的容器中运行脚本。但是这种情况并不常见.

### 微服务体系结构

顾名思义，微服务体系结构是一种将服务器应用程序生成为一组小型服务的方法。 这意味着微服务体系结构主要面向后端，虽然该方法也会用于前端。 每个服务都在自己的进程中运行，并使用 HTTP/HTTPS、WebSocket 或 AMQP 等协议与其他进程进行通信。 每个微服务在特定的上下文边界内实现特定的端到端域或业务功能，每个微服务都必须自主开发，并且可以独立部署。 微服务应该有多大？ 在开发微服务时，大小不应成为重点。 相反，重点应该是创建松散耦合的服务以便自主地为每个服务进行开发、部署和缩放。 当然，在标识和设计微服务时，只要与其他微服务不存在过多的直接依赖项，就应尝试让它们尽可能地小。 比微服务的大小更重要的是，它必须具有内部内聚，并且独立于其他服务。为何选择微服务体系结构？ 简而言之，它提供了长期的灵活性。 通过允许基于许多独立的可部署服务（每个服务都具有粒度和自主生命周期）创建应用程序，微服务在复杂、大型和高度可缩放的系统中支持更好的可维护性。

微服务的另外一大优势是，可以独立横向扩展。 可以横向扩展特定的微服务，而无需将单个整体应用程序作为一个单位扩展。 这样便可单独缩放需要更多处理能力或网络带宽以支撑需求的功能区域，而不用横向扩展应用程序中不需要缩放的其他区域。 这意味着节省成本，因为所需硬件更少。