# Assets file project.assets.json not found. Run a NuGet package restore

<font face="Microsoft YaHei">

## 问题起因

添加 .net core的Project build失败. 提示以下error:
>Assets file project.assets.json not found. Run a NuGet package restore.

## 问题原因

发生此错误是因为dotnet cli最初未创建所有必需的文件。 执行dotnet restore会添加所需的文件。

## 解决方案

Tools > NuGet Package Manager > Package Manager Console simply run
>dotnet restore

## 参考资料

[stackoverflow](https://stackoverflow.com/questions/48440223/assets-file-project-assets-json-not-found-run-a-nuget-package-restore)


