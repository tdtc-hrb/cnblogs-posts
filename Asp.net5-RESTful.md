---
title: "ASP.net Core 的RESTful应用"
description: ".tabset mean tabs."
date: 2021-10-23T12:08:08+08:00
---
  在发布具有历史意义的.net 5.0的2H前，写下此博文。
![windows msg1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201110005040.png)  

- PowerShell v5.1
- IDE: VS Code/VS 2019
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

# Create Project {.tabset}
  Choice vs2019 or vs code.

- VS Code
- VS 2019

## VS2019
![VS2019 pre1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201110010117.png)

### add NuGet packages
```bash
Install-Package Microsoft.EntityFrameworkCore
Install-Package MySql.Data.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.Tools
```

## vs code

```bash
dotnet new webapi -o MyWebApp --package Swashbuckle.AspNetCore:PackageVersion=6.2.3
```

### add NuGet package
```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package MySql.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.Tools
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



# 数据表 {.tabset}

- PM
- Dotnet CLI

## PM

### 生成db执行文件
```bash
PM> Add-Migration Initial
```

### 生成Table
```bash
PM> Update-Database
```

## CLI


### 生成db执行文件
```bash
dotnet ef migrations add Initial
```

### 生成Table
```bash
dotnet ef database update
```

## 写入数据
seedData.cs

### exec
> Startup.cs
> >  Configure方法中

```bash
SeedData.EnsurePopulated(app);
```

[Part I](https://pan.baidu.com/s/15tjlrHp2Gmg8pJm7W9delg)  提取码: cn56




# 建立MC<sub>j</sub>

## 建立Controller
使用Scaffolder模板:
![VS2019 prj1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20201113000732.png)

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
var result = _db.Products;
```

指定数据:
```bash
 var result = _db.Products.FindAsync(id);
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

[Part II](https://pan.baidu.com/s/1o2VQ0b4-BuTosIvnZ6jH_w) 提取码: 5deh





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

## POST/DELETE/PUT
- 添加
```bash
_db.Products.AddAsync(product);
```

- 移出
```bash
_db.Products.Remove(product);
```

- 更新
```bash
_db.Update(product);
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
Install-Package Microsoft.AspNetCore.Mvc.NewtonsoftJson --Version 5.0.11
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

## Test Data
> PowerShell

### get
```bash
Invoke-WebRequest https://localhost:44309/api/products/1 | Select-Object Content
```

### post
```bash
Invoke-RestMethod https://localhost:44309/api/products -Method POST -Body (@{ Name="Swimming Goggles";
Price=12.75; Category_id=1; Supplier_id=1} | ConvertTo-Json) -ContentType "application/json"
```

```bash
Invoke-RestMethod https://localhost:44309/api/products -Method POST -Body (@{ Name="Soccer Boots"; Price=89.99;
Category_id=2; Supplier_id=2} | ConvertTo-Json) -ContentType "application/json"
```

```bash
Invoke-RestMethod https://localhost:44309/api/products -Method POST -Body (@{ Name="Swim Buoy";
Price=19.99; Category_id=1; Supplier_id=1} | ConvertTo-Json) -ContentType "application/json"
```

### update
```bash
Invoke-RestMethod https://localhost:44309/api/products -Method PUT -Body (@{ Id=1; Name="Green Kayak";
Price=275; Category_id=1; Supplier_id=1} | ConvertTo-Json) -ContentType "application/json"
```

### delete
```bash
Invoke-RestMethod https://localhost:44309/api/products/13 -Method DELETE
```

### status code
```bash
Invoke-WebRequest https://localhost:44309/api/products/100 |Select-Object StatusCode
```

### patch
> 无法在PS中执行,只能在swaggerUI.

输入ID: 5； 并在Request body,更改为如下内容:
```xml
[
  {
    "path": "Price",
    "op": "replace",
    "value": "66800"
  }
]
```


[Part III](https://pan.baidu.com/s/1FZjGGSH0kaUWjcsLzKlNfw) 提取码: v44x


# Reference
- [<sup>1</sup> Sources](https://docs.microsoft.com/en-us/aspnet/core/mvc/models/model-binding?view=aspnetcore-5.0#sources)
- [<sup>2</sup> JsonPatchDocument](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.jsonpatch.jsonpatchdocument?view=aspnetcore-5.0)
- [<sup>3</sup> Operations](https://docs.microsoft.com/en-us/aspnet/core/web-api/jsonpatch?view=aspnetcore-5.0#operations)
- [Why use ICollection and not IEnumerable or List<T> on many-many/one-many relationships?](https://stackoverflow.com/questions/10113244/why-use-icollection-and-not-ienumerable-or-listt-on-many-many-one-many-relatio)
- [ASP.NET CORE HTTPPOST - CREATE](https://www.cosmiclearn.com/dotnet/aspnet_webapi_httppost.php)
- [.net core 中的FromBody](https://www.cnblogs.com/KQNLL/p/10322143.html)
- [ASP.NET Core 中的 JSON Patch](https://docs.microsoft.com/zh-cn/aspnet/core/web-api/jsonpatch?view=aspnetcore-5.0#json-patch-in-aspnet-core)
- [Unable to connect to web server 'IIS Express'](https://stackoverflow.com/questions/58346617/unable-to-connect-to-web-server-iis-express)    
  删除.vs文件夹即可
