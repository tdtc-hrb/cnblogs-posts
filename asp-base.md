---
title: "3小时搞定asp.net 9.0"
description: "MVC基础篇"
date: 2024-10-12T09:08:08+08:00
---
- IDE: Visual Studio 2022 -[community](https://visualstudio.microsoft.com/vs/preview/#download-preview)
- DB：MySQL5.7+

下载[V9.0](https://dotnet.microsoft.com/download/dotnet/9.0): 
- 32位 :[x86](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-9.0.100-rc.2-windows-x86-installer)
- 64位 :[x64](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-9.0.100-rc.2-windows-x64-installer)


# 建立工程
> File-》New-》Project
>> ASP.NET Core Web Application(Model-View-Controller)

![vs2022 preview](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011124537.png)

## 简单修改主页
> Solution Explorer
>> Views-》Home-》Index.cshtml

增加如下代码：
```asp.net
<p>这是我的第1个ASP.NET程序！</p>
```
  选择使用何种Web Server 运行：
- http
- https
- IIS Express
> default: https

  点击“IIS Express”运行（如果弹出证书安装选“是”即可）。
![iis cert - hint](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011134118.png)
![windows - hint](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011134337.png)

# 增加数据库

## EF Core
> Project-》Manage NuGet Packages
![Microsoft EF Core](https://github.com/tdtc-hrb/cnblogs/raw/main/images/20241011135217.png)

## entity class
  我们建立一个与数据表相对应的Entity类
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

## DB Class
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

### Repository操作类
  接口：
> Solution Explorer -》 
> Model右键点击Add-》New Item选择Interface
>> Name: IProductRepository.cs
```c#
public interface IProductRepository
{
    IQueryable<Product> Products { get; }
}
```
  实现：
> Solution Explorer -》 
> Model右键点击Add-》New Item选择Class
>> Name: EFProductRepository.cs
- Use primary Constructor
```c#
public class EFProductRepository(ApplicationDbContext context) : IProductRepository
{
    public IQueryable<Product> Products => context.Products;
}
```

### connector
The screenshots use MySQL EF Core v8.0.5;
It does not work on .net9.0!    
Version 9.0.0 released in January 2025 supports.

#### 添加MySQLConnector
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

# 配置DI（dependency injection）
> Program.cs
>> Configure Services

  添加数据库context
```c#
//// MySql - Oracle
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseMySQL(builder.Configuration["Data:VeicWeb:ConnectionString"]));
```
  添加Repository
```c#
builder.Services.AddTransient<IProductRepository, EFProductRepository>();
```
前后代码比较：
![ultra compare p1](https://github.com/tdtc-hrb/cnblogs/raw/main/images/uc202410111716.png)

# 添加View
> Index.cshtml
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

# 添加Controller
> HomeController.cs

  引用实例：
```c#
private readonly IProductRepository _productRepository;

public HomeController(ILogger<HomeController> logger, IProductRepository productRepository)
{
    _logger = logger;
    _productRepository = productRepository;
}
```
  调用他：
```c#
public IActionResult Index()
{
    return View(_productRepository.Products);
}
```

前后代码比较：
![ultra compare p3](https://github.com/tdtc-hrb/cnblogs/raw/main/images/uc202410111746.png)

运行效果：
![演示code](https://img-blog.csdnimg.cn/20210707161947768.png)


# Reference
- [pro-asp-net-core-mvc-2-7th-edition](http://www.allitebooks.org/pro-asp-net-core-mvc-2-7th-edition/)
- [pro-asp-net-core-mvc-2-7th-edition pdf](http://file.allitebooks.com/20171025/Pro%20ASP.NET%20Core%20MVC%202,%207th%20Edition.pdf)
- [The entity type requires a primary key to be defined](https://stackoverflow.com/questions/48225989/the-entity-type-requires-a-primary-key-to-be-defined)
