---
title: "3小时搞定asp.net"
description: "MVC基础篇"
date: 2026-07-05T09:08:08+08:00
---
- IDE: [Visual Studio community](https://visualstudio.microsoft.com)
- DB：MySQL5.7+
- [.net SDK - x64](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-10.0.301-windows-x64-installer)

## 准备
> File-》New-》Project
>> ASP.NET Core Web Application(Model-View-Controller)

![vs2026](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011124537.png)

### Additional information
- .Net 10.0(Lts)
- Authentication type
```
None
```
- Configure for HTTPS
```
checked
```

### Web Server
- http
- https
> default
- IIS Express

  点击“IIS Express”运行（如果弹出证书安装选“是”即可）。
![iis cert - hint](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011134118.png)
![windows - hint](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011134337.png)

# Demo
![演示code](https://img-blog.csdnimg.cn/20210707161947768.png)

## Repository
> Project-》Manage NuGet Packages
![Microsoft EF Core](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011135217.png)

### entity class
数据表的Entity类
```c#
using System.ComponentModel.DataAnnotations;

namespace veic_web.Models
{
    public class Product
    {
        [Key]
        public int Id { get; set; }
        public int Param_id { get; set; }
        public int Statu_id { get; set; }
        public int Lang_id { get; set; }
        public int Img_id { get; set; }
        public string? Name { get; set; }
        public string? Description { get; set; }
    }
}
```

### Db Context
> Solution Explorer
>> Model右键点击Add-》New Item选择Class
>> Name: ApplicationDbContext.cs

  添加引用：
```c#
using Microsoft.EntityFrameworkCore;
```
  添加继承：DbContext;    
  and "Generate Constructor 'ApplicationDbContext(options)'"(shortcut: Ctrl+. or Alt+Enter)
```c#
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options)
    {
    }
}
```
  代码模板在DbContext.cs
  添加Entity类支持
```c#
public DbSet<Product> Products
{
    get; set;
}
```

### 操作类
- 接口：
> Solution Explorer -》 
> Model右键点击Add-》New Item选择Interface
>> Name: IProductRepository.cs
```c#
public interface IProductRepository
{
    IQueryable<Product> Products { get; }
}
```
- 实现：
> Solution Explorer -》 
> Model右键点击Add-》New Item选择Class
>> Name: EFProductRepository.cs
```c#
public class EFProductRepository(ApplicationDbContext context) : IProductRepository
{
    public IQueryable<Product> Products => context.Products;
}
```

### connector
> Project-》Manager NuGet Packages
![MySql EF Core](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011152840.png)
#### connection string
- appsettings.json
```xml
"Data": {
    "VeicWeb": {
        "ConnectionString": "server=127.0.0.1; user id=DBAdmin; password=xbfirst; database=carnumber; pooling=false; Convert Zero Datetime=True;"
    }
},
```

前后代码比较：
![ultra compare p0](https://github.com/tdtc-hrb/cnblogs/raw/main/images/uc202410111701.png)

## DI - Configure Services
> Program.cs

- 数据库context
```c#
//// MySql - Oracle
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseMySQL(builder.Configuration["Data:VeicWeb:ConnectionString"]));
```
- Repository
```c#
builder.Services.AddTransient<IProductRepository, EFProductRepository>();
```
前后代码比较：
![ultra compare p1](https://github.com/tdtc-hrb/cnblogs/raw/main/images/uc202410111716.png)

## View-Controller
Index.cshtml:
```c#
@model IEnumerable<Product>
@foreach (var p in Model)
{
    <div>
        <h3>@p.Name</h3>
        @p.Description
    </div>
}
```

前后代码比较：
![ultra compare p2](https://github.com/tdtc-hrb/cnblogs/raw/main/images/uc202410111737.png)

### Controller
> HomeController.cs

- 引用实例：
```c#
private readonly IProductRepository _productRepository;

public HomeController(ILogger<HomeController> logger, IProductRepository productRepository)
{
    _logger = logger;
    _productRepository = productRepository;
}
```
- 调用他：
```c#
public IActionResult Index()
{
    return View(_productRepository.Products);
}
```

前后代码比较：
![ultra compare p3](https://github.com/tdtc-hrb/cnblogs/raw/main/images/uc202410111746.png)

# Reference
- [pro-asp-net-core-mvc-2-7th-edition](http://www.allitebooks.org/pro-asp-net-core-mvc-2-7th-edition/)
- [The entity type requires a primary key to be defined](https://stackoverflow.com/questions/48225989/the-entity-type-requires-a-primary-key-to-be-defined)
