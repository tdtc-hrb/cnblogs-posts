---
title: "ASP.net Core 的gRPC Service应用"
description: "通过[Greeter示例](https://github.com/grpc/grpc-dotnet/tree/master/examples/Greeter)，来演示gRPC Service"
date: 2021-03-16T08:08:08+08:00
---
> Server 在Ubuntu V20.04; Client 在Windows CMD运行。

- PowerShell v5.1
- IDE: VS2019 Community(v16.9)/VS Code([docker extension](https://code.visualstudio.com/docs/containers/overview#_installation))
- VM: VBox(v6.1)

# 接口文件说明
> Protos/greet.proto
>> 现版本(v16.9)已可以自动生成

## 定义service
```bash
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}
```

## 定义message
- request
```bash
message HelloRequest {
  string name = 1;
}
```
- response
```bash
message HelloReply {
  string message = 1;
}
```

# server

## Create {.tabset}

### VS2019
![VS2019 pre1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201122182726.png)
注意：服务器需要docker支持！
![VS2019 pre2](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201122183151.png)

![VS2019 pre3](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201122184828.png)

### VS code
```bash
dotnet new grpc -o GreeterServer
```

#### add docke file
* View -> Command Palette(Ctrl+Shift+P)
+ Enter: Docker fi
+ choice1: Add Docker Files to Workspace...    
+ choice2: .NET: ASP.NET Core   
+ choice3: Linux
+ Enter: 80
+ choice4: No(Include compose file)

#### 修改Dockerfile
+ 移出Ubuntu版本信息(-focal)
> focal: Ubuntu 20.04
+ 移出调试URL
> ENV ASPNETCORE_URLS=http://+:80


## 实现的service
> VS2019/vs code自动生成

```bash
public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
{
    return Task.FromResult(new HelloReply
    {
        Message = "Hello " + request.Name
    });
}
```

## 添加SSL支持<sup>1</sup>
> Program.cs

更改生成的Code:
```bash
 webBuilder.UseStartup<Startup>();
```
变更:
```bash
webBuilder.UseStartup<Startup>().ConfigureKestrel(options =>
               {
                   options.Listen(IPAddress.Any, 5001, listenOptions =>
                   {
                       listenOptions.Protocols = HttpProtocols.Http2;
                   });
               });
```

# client
建一个Console应用程序，来测试gRPC服务。

## Create {.tabset}

### vs2019
主要的NuGet：
![VS2019 NuGet1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201122193736.png)
```bash
Install-Package Grpc.Core" -Version 2.41.0
```
其他NuGet:
```bash
Install-Package Google.Protobuf -Version 3.18.0
Install-Package Grpc.Net.Client -Version 2.39.0
Install-Package Grpc.Tools -Version 2.41.0
```

### vs code
```bash
dotnet new console -o GreeterClient
```

#### NuGet
```bash
dotnet add GreeterClient.csproj package Grpc.Net.Client
dotnet add GreeterClient.csproj package Google.Protobuf
dotnet add GreeterClient.csproj package Grpc.Tools
```


## 增加Proto<sup>2</sup>
> GreeterClient.csproj
```bash
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## test程序<sup>3</sup>
> Program.cs

```bash
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    using var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client =  new Greeter.GreeterClient(channel);
    var reply = await client.SayHelloAsync(
                      new HelloRequest { Name = "GreeterClient" });
    Console.WriteLine("Greeting: " + reply.Message);
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

# Test picture
![cmd vmware](https://img-blog.csdnimg.cn/20201123192847962.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9iaW5fSExKODA=,size_16,color_FFFFFF,t_70#pic_center)
![cmd](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201123191050.png)


# Reference
- [<sup>1</sup> grpc server not working in docker-compose](https://stackoverflow.com/questions/64093550/grpc-server-not-working-in-docker-compose)
- [<sup>2</sup> gRPC Meets .NET SDK And Visual Studio: Automatic Codegen On Build](https://grpc.io/blog/grpc-dotnet-build/)
- [<sup>3</sup> 生成的 C# 资产](https://docs.microsoft.com/zh-cn/aspnet/core/grpc/basics?view=aspnetcore-5.0#generated-c-assets)
- [ASP.NET Core in a container](https://code.visualstudio.com/docs/containers/quickstart-aspnet-core)
- [Create a gRPC client and server in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/tutorials/grpc/grpc-start?view=aspnetcore-5.0&tabs=visual-studio-code)
