---
title: "ASP.net Core 的gRPC Service应用"
description: "通过[Greeter示例](https://github.com/grpc/grpc-dotnet/tree/master/examples/Greeter)，来演示gRPC Service"
date: 2021-11-09T20:38:18+08:00
---
> Server 在Ubuntu V20.04; Client 在Windows CMD运行。

- PowerShell Core v7.2
- IDE: VS2022 Community(v17.0)/VS Code([docker extension](https://code.visualstudio.com/docs/containers/overview#_installation))
- VM: VBox(v6.1)

# 接口文件说明
> Protos/greet.proto

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

### VS2022
![VS2022 pre1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20211109172442.png)
注意：服务器需要docker支持！
![VS2022 pre2](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20211109173447.png)

![VS2022 pre3](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20211109174850.png)

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

## 实现的service
> VS2022/vs code自动生成

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

configure WebHost<sup>4</sup>:
```csharp
builder.WebHost.ConfigureKestrel(options =>
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

### vs2022
主要的NuGet：
```bash
Install-Package Grpc.Core" -Version 2.41.1
```
其他NuGet:
```bash
Install-Package Google.Protobuf -Version 3.19.1
Install-Package Grpc.Net.Client -Version 2.40.0
Install-Package Grpc.Tools -Version 2.41.1
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

![VS2022 proto](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20211109193955.png)

## test程序<sup>3</sup>
> Program.cs
>> 注意：需先build生成gRPC stub类

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
![cmd vbox](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20211109205247.png)
![cmd](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20211109203435.png)



# Reference
- [<sup>1</sup> grpc server not working in docker-compose](https://stackoverflow.com/questions/64093550/grpc-server-not-working-in-docker-compose)
- [<sup>2</sup> gRPC Meets .NET SDK And Visual Studio: Automatic Codegen On Build](https://grpc.io/blog/grpc-dotnet-build/)
- [<sup>3</sup> 生成的 C# 资产](https://docs.microsoft.com/zh-cn/aspnet/core/grpc/basics?view=aspnetcore-6.0#generated-c-assets)
- [<sup>4</sup> Configure application startup in ASP.NET Core 6](http://www.binaryintellect.net/articles/d2dff6f5-c02b-4d3d-9a3b-c5ed805fffb4.aspx)
- [ASP.NET Core in a container](https://code.visualstudio.com/docs/containers/quickstart-aspnet-core)
- [Create a gRPC client and server in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/tutorials/grpc/grpc-start?view=aspnetcore-6.0&tabs=visual-studio-code)
