---
title: "ASP.net Core 的RESTful应用"
description: ".tabset mean tabs."
date: 2021-11-11T12:38:08+08:00
---
  使用Scaffolder模板创建api。

- PowerShell v5.1
- IDE: Visual Studio Code/[Visual Studio 2022](https://docs.microsoft.com/en-us/visualstudio/releases/2022/release-notes)
- NuGet    
> ..\AppData\Roaming\NuGet\NuGet.Config
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="api.nuget.org" value="https://api.nuget.org/v3/index.json" />
  </packageSources>
</configuration>
```

# Create Project
vs code

```bash
dotnet new webapi -o MyWebApp --package Swashbuckle.AspNetCore:PackageVersion=6.2.3
```

### add NuGet package
```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package MySql.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

### Option NuGet package
Pomelo:
```bash
CLI> dotnet add package Pomelo.EntityFrameworkCore.MySql
```

## Test
[Browser](https://localhost:5001/swagger/index.html)


# Models
  建立Entity类：Supplier、Product、Category

## DbContext
```bash
public class StorageBuyDbContext : DbContext
{
    public StorageBuyDbContext(DbContextOptions<StorageBuyDbContext> options) : base(options){}

    public DbSet<Category> Categories { get; set; }
    public DbSet<Supplier> Suppliers { get; set; }
    public DbSet<Product> Products { get; set; }
}
```

## add service
### add connectiong string
```xml
"Data": {
  "StorageBuy": {
    "ConnectionString": "server=127.0.0.1; user id=DBAdmin; password=xbfirst; database=storagebuy; pooling=false; Convert Zero Datetime=True;"
  }
}
```

## add mysql
```bash
services.AddDbContext<StorageBuyDbContext>(options =>
    options.UseMySQL(Configuration["Data:StorageBuy:ConnectionString"])
);
```



# 数据表
install ef-tool
```bash
dotnet tool install --global dotnet-ef
```

## 生成db执行文件
```bash
dotnet ef migrations add Initial
```

## 生成Table
```bash
dotnet ef database update
```

## 写入数据
seedData.cs

## exec
> Startup.cs
> >  Configure方法中

```bash
SeedData.EnsurePopulated(app);
```

[Part I](https://pan.baidu.com/s/1zX1TVAe_NONChAAGNp7k-w)  提取码: mmig



# 建立MC<sub>j</sub>

## 建立Controller
使用VS 2022 Scaffolder模板:
![VS2022 prj1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/202110283000732.png)

![vs2022 pre2](https://gitee.com/xiaobin80/cnblogs/raw/master/images/202110280005040.png)

### 配置路由
- class
```bash
[ApiController]
[Route("api/[controller]")]
```

- method    
以Get举例:
```bash
[HttpGet]
```

### 处理Data

所有数据:
```bash
return await _context.Products.ToListAsync();
```

指定数据:
```bash
var product = await _context.Products.FindAsync(id);
```

## 配置Json
忽略空值
```bash
services.Configure<JsonOptions>(
    opts =>
    {
        opts.JsonSerializerOptions.IgnoreNullValues = true;
    }
);
```



# commit operator
  指定操作方式(Table 3-1), 并指定源<sup>1</sup>(Table 3-2).    
  推荐使用[PostMan](https://www.postman.com/downloads/)来进行调试.


Table 3-1. [HTTP Methods and Operations](https://restfulapi.net/http-methods/)

|HTTP|Method Description|
|-|-|  
|GET|从服务器获取数据|
|POST|向服务器提交新的Object|
|PUT|向服务器提交修改(整体)|
|DELETE|向服务器提交删除object.|
|PATCH|向服务器提交更新部分object.|

Table 3-2. 源属性

|Name|Description|
|-|-|
|[FromQuery](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.fromqueryattribute) | Gets values from the query string.|
|[FromRoute](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute) | Gets values from route data.|
|[FromForm](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.fromformattribute) | Gets values from posted form fields.|
|[FromBody](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) | Gets values from the request body.|
|[FromHeader](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.fromheaderattribute) | Gets values from HTTP headers.|

## PUT/POST/DELETE
- 更新
```bash
_context.Entry(product).State = EntityState.Modified;
```

- 添加
```bash
_context.Products.Add(product);
```

- 移出
```bash
_context.Products.Remove(product);
```

## Patch {.tabset}
在客户端patch时的操作码见Table3-3

Table 3-3. httpPatch操作码

|Operation|Notes|
|-|-|
|add|Add a property or array element. For existing property: set value.|
|remove|Remove a property or array element.|
|replace|Same as remove followed by add at same location.|
|move|Same as remove from source followed by add to destination using value from source.|
|copy|Same as add to destination using value from source.|
|test|Return success status code if value at path = provided value.|

### PM
```bash
Install-Package Microsoft.AspNetCore.Mvc.NewtonsoftJson
```

### CLI
```
dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
```


## config
> Startup

```bash
            services.AddControllers().AddNewtonsoftJson();

            services.Configure<MvcNewtonsoftJsonOptions>(opts => {
                opts.SerializerSettings.NullValueHandling
                = Newtonsoft.Json.NullValueHandling.Ignore;
            });
```

## enjoin parameter
  需要指定从哪里获取JSON, 普遍是[FromBody](https://docs.microsoft.com/zh-cn/aspnet/core/mvc/models/model-binding?view=aspnetcore-5.0#sources),并用JsonPatchDocument<T>解析JSON, 最后在方法里使用ApplyTo<sup>2</sup>
```bash
ModifyProduct(long id, [FromBody] JsonPatchDocument<Product> patchDoc)
```
```bash
patchDoc.ApplyTo(product);
```

## patch method
```csharp
        [HttpPatch("{id}")]
        public async Task<IActionResult> ModifyProduct(long id, [FromBody] JsonPatchDocument<Product> patchDoc)
        {
            var product = await _context.Products.FindAsync(id);

            if (product != null)
            {
                patchDoc.ApplyTo(product);

                await _context.SaveChangesAsync();
            }

            return Ok();
        }
```


## Test Data
> PowerShell

### get
```bash
Invoke-WebRequest https://localhost:7255/api/products/1 | Select-Object Content
```

### post
```bash
Invoke-RestMethod https://localhost:7255/api/products -Method POST -Body (@{ Name="Swimming Goggles";
Price=12.75; Category_id=1; Supplier_id=1} | ConvertTo-Json) -ContentType "application/json"
```

```bash
Invoke-RestMethod https://localhost:7255/api/products -Method POST -Body (@{ Name="Soccer Boots"; Price=89.99;
Category_id=2; Supplier_id=2} | ConvertTo-Json) -ContentType "application/json"
```

```bash
Invoke-RestMethod https://localhost:7255/api/products -Method POST -Body (@{ Name="Swim Buoy";
Price=19.99; Category_id=1; Supplier_id=1} | ConvertTo-Json) -ContentType "application/json"
```

### update
```bash
Invoke-RestMethod https://localhost:7255/api/products/1 -Method PUT -Body (@{ Id=1; Name="Green Kayak";
Price=275; Category_id=1; Supplier_id=1} | ConvertTo-Json) -ContentType "application/json"
```

### delete
```bash
Invoke-RestMethod https://localhost:7255/api/products/12 -Method DELETE
```

### status code
```bash
Invoke-WebRequest https://localhost:7255/api/products/100 |Select-Object StatusCode
```

### patch
> allow tls1.2<sup>4</sup>
```ps
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

```bash
Invoke-RestMethod https://localhost:7255/api/products/1 -Method PATCH -Body '[{"op":"replace", "path":"Price", "value":"618.00"}]' -ContentType "application/json"
```


[MyWebApp v6.0](https://pan.baidu.com/s/1g1xXeNC1ootHACUopvRIwg) 提取码: tocx


# Reference
- [<sup>1</sup> Sources](https://docs.microsoft.com/en-us/aspnet/core/mvc/models/model-binding?view=aspnetcore-5.0#sources)
- [<sup>2</sup> JsonPatchDocument](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.jsonpatch.jsonpatchdocument?view=aspnetcore-5.0)
- [<sup>3</sup> Operations](https://docs.microsoft.com/en-us/aspnet/core/web-api/jsonpatch?view=aspnetcore-5.0#operations)
- [<sup>4</sup> Invoke-RestMethod : The underlying connection was closed: The connection was closed unexpectedly](https://evotec.xyz/invoke-restmethod-the-underlying-connection-was-closed-an-unexpected-error-occurred-on-a-send-while-connecting-graph-api/)
- [Why use ICollection and not IEnumerable or List<T> on many-many/one-many relationships?](https://stackoverflow.com/questions/10113244/why-use-icollection-and-not-ienumerable-or-listt-on-many-many-one-many-relatio)
- [ASP.NET CORE HTTPPOST - CREATE](https://www.cosmiclearn.com/dotnet/aspnet_webapi_httppost.php)
- [.net core 中的FromBody](https://www.cnblogs.com/KQNLL/p/10322143.html)
- [ASP.NET Core 中的 JSON Patch](https://docs.microsoft.com/zh-cn/aspnet/core/web-api/jsonpatch?view=aspnetcore-5.0#json-patch-in-aspnet-core)
- [Unable to connect to web server 'IIS Express'](https://stackoverflow.com/questions/58346617/unable-to-connect-to-web-server-iis-express)    
  删除.vs文件夹即可
