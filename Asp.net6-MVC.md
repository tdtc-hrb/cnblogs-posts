---
title: "3小时搞定ASP.NET Core 6.0"
description: "MVC基础篇(MVC basics)"
date: 2023-03-14T08:18:02+08:00
---
- OS    
[Windows 10 LTSB 2016+](https://github.com/dotnet/core/blob/main/release-notes/6.0/supported-os.md)
- IDE    
[Visual Studio Code](https://code.visualstudio.com/download) - v1.76.1
- SDK    
[.Net 6.0](https://dotnet.microsoft.com/download/dotnet/6.0)
- 终端(Terminal)    
 [PowerShell Core](https://github.com/PowerShell/PowerShell#get-powershell)
- 数据库(Database)    
[MySQL](https://dev.mysql.com/downloads/mysql/) / Microsoft SQL Server 2012 Express LocalDB (vs2013 inside)


# 一、Create Project(VS Code)
- Init project
> Terminal -> New Terminal
```bash
cd .\Documents\
dotnet new mvc -o veic_web --framework net6.0
```

- Set workspace dir
> File -> Add Folder to Workspace


## 设置缺省首页(Home Set Default)
- Program.cs
```c#
app.UseStaticFiles();
```
变更为:
```c#
app.UseDefaultFiles();
app.UseStaticFiles();
```

- Html    
  新建wwwroot/index.html，See附录<sup>1</sup>.

## Run
```
Run -> Start Debugging
```

运行效果:
![home page](https://img-blog.csdnimg.cn/20201102052153747.png)


# 二、Data
namespace:
```c#
namespace veic_web.Models;
```

## Table
  You can choose between MySQL or MSSQL.

  Ms SQL Server See附录<sup>2</sup>

## 1. Add package
Enter the project folder (the directory where the ".csproj" file is located).
```bash
cd veic_web
```

update NuGet:
```bash
dotnet nuget add source --name nuget.org https://api.nuget.org/v3/index.json
```

### EF Core
- Release
```bash
CLI> dotnet add package Microsoft.EntityFrameworkCore --version 6.0.14
```
- Preview
```bash
CLI> dotnet add package Microsoft.EntityFrameworkCore --prerelease
```

### MySQL
- Pomelo
```bash
CLI> dotnet add package Pomelo.EntityFrameworkCore.MySql --version 6.0.2
```

- Oracle
```bash
CLI> dotnet add package MySql.EntityFrameworkCore --version 6.0.7
```

### MS Sql
```bash
CLI> dotnet add package Microsoft.EntityFrameworkCore.Relational --version 6.0.14
```
- Release
```bash
CLI> dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 6.0.14
```
- Preview
```bash
CLI> dotnet add package Microsoft.EntityFrameworkCore.SqlServer --prerelease
```

## 2. Entity Framework

### entity class
Product.cs:
```c#
using System.ComponentModel.DataAnnotations;

namespace veic_web.Models;

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
```


### DB Context
> ApplicationDbContext.cs

引用：
```c#
using Microsoft.EntityFrameworkCore;
```
实现：
```c#
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options)
    {

    }
}
```
Entity支持
```c#
    public DbSet<Product> Products { get; set; } = default!;
```


## 3. Usage EF
首先添加数据库context，然后添加Repository。

### addDbContext
- MySQL    
Pomelo:
```c#
//// MySql - Pomelo
builder.Services.AddDbContext<ApplicationDbContext>(options =>
                        options.UseMySql(builder.Configuration["Data:VeicWeb:ConnectionString"],
                            new MySqlServerVersion(new Version(5, 7, 38)))
                .EnableSensitiveDataLogging());
```
Oracle:
```c#
//// MySql - Oracle
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseMySQL(builder.Configuration["Data:VeicWeb:ConnectionString"]));
```
- LocalDB    
```c#
//// MS Sql
builder.Services.AddDbContext<ApplicationDbContext>(options =>
                        options.UseSqlServer(builder.Configuration["Data:mslocaldb:ConnectionString"])
                .EnableSensitiveDataLogging());
```

#### DB Connection String
> appsettings.json

- MySQL
```xml
"Data": {
    "VeicWeb": {
        "ConnectionString": "server=127.0.0.1; user id=DBAdmin; password=xbfirst; database=carnumber; pooling=false; Convert Zero Datetime=True;"
    }
}
```

- [LocalDB](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15#connect-to-the-automatic-instance)
```xml
    "Data":{
       "mslocaldb": {
          "ConnectionString": "Server=(LocalDB)\\MSSQLLocalDB;Database=carnumber;MultipleActiveResultSets=true"
        }
    }
```

### addTransient
use瞬时模式, 添加Repository:
```c#
builder.Services.AddTransient<IProductRepository, EFProductRepository>();
```

#### Repository类
引用：
```c#
using System.Linq;
```
接口：
```c#
public interface IProductRepository
{
    IQueryable<Product> Products { get; }
}
```
实现：
```c#
public class EFProductRepository : IProductRepository
{
    private readonly ApplicationDbContext context;

    public EFProductRepository(ApplicationDbContext ctx)
    {
        this.context = ctx;
    }

    public IQueryable<Product> Products => context.Products;
}
```


# 三、View-Controller

Views/Home/Index.cshtml:
由
```html
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
```
替换为：
```html
    <p>Learn about <a href="/home/list">查看产品列表</a></p>
```

Views/Home/ProductList.cshtml:
```html
@model IEnumerable<Product>
@foreach (var p in Model!)
{
    <div>
        <h3>@p.Name</h3>
        @p.Description
    </div>
}
```


## Controller
> HomeController.cs

声明product repository：
```c#
    private readonly IProductRepository _repository;
```
DI:
```c#
    public HomeController(ILogger<HomeController> logger, IProductRepository repo)
    {
        _logger = logger;
        _repository = repo;
    }
```
引用实例：添加新方法
```c#
    public IActionResult List(int countPage = 1)
    {
        var repo = _repository.Products.Where(lang => lang.Lang_id.Equals(langId)).OrderBy(p => p.Id)
            .Skip((countPage - 1) * pageSize)
            .Take(pageSize);
        return View("ProductList", repo);
    }
```
增加分页: 声明公共成员变量
```c#
    public int pageSize = 3;
    public int langId = 4075;
```

- [运行效果](https://localhost:5001/home/list?countPage=2)：    
![debug vs code](https://img-blog.csdnimg.cn/20210707161947768.png)


project code:
- asp.net 6.0 m3: [提取码: 38qh](https://pan.baidu.com/s/1pRbJhKfU--HLW9TEk71x1A?pwd=38qh)


# 附录

## 1
```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>公司类网站ASP.NET 6.0</title>
    </head>
<body>
    <div>
        <marquee>欢迎来到ASP.NET世界</marquee>
    </div>

    <div>
        <p align="center"><a href="javascript:void(0)" onClick="callPage('home')" >中文</a>&nbsp;&nbsp;&nbsp;&nbsp;
        <a href="javascript:void(0)" onClick="callPage('home/privacy')" >ENGLISH</a></p>
    </div>

    <script type="text/javascript">
        callPage = function(linkAddr) {;
            window.location.href=window.location + linkAddr;
            // window.open(window.location + linkAddr);
        };
    </script>

</body>

</html>
```

## 2
down [navicat for sqlserver](https://navicat.com/en/download/navicat-for-sqlserver); later install Microsoft SQL Server 2012 Native Client(sqlncli.msi - amd64)    
Host:    
```bash
(localdb)\v11.0
```

create db:    
```sql
CREATE DATABASE carnumber;
```

create table:    
```sql
USE [carnumber]
GO

/****** Object:  Table [dbo].[products]    Script Date: 2021/8/14 8:47:00 ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[products](
    [id] [int] NOT NULL,
    [param_id] [int] NOT NULL,
    [statu_id] [int] NOT NULL,
    [lang_id] [int] NOT NULL,
    [img_id] [int] NOT NULL,
    [name] [varchar](80) NULL,
    [description] [text] NULL,
 CONSTRAINT [PK_products] PRIMARY KEY CLUSTERED
(
    [id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
```

### LocalDB
About vs2013 installation files:    
All feature packages are in "packages";    
- sqllocaldb_amd64    
64-bit installation package of ms sql2012 localdb    
- sqlncli_amd64    
64-bit installation package of Microsoft SQL Server 2012 Native Client

[see instances](https://stackoverflow.com/questions/26007913/the-specified-localdb-instance-does-not-exist):    
```bash
sqllocaldb i
```

## Nullable

### Variable
声明中增加：
```
?
```
使用中增加：
```
!
```

### Program
增加注释：
```
#nullable disable
```


# Reference
- [Pro ASP.NET Core 3, 8th. Edition](https://www.apress.com/gp/book/9781484254394)
- [error: there are no version available for package](https://stackoverflow.com/a/67937895)
- [The entity type requires a primary key to be defined](https://stackoverflow.com/questions/48225989/the-entity-type-requires-a-primary-key-to-be-defined)
- [Migration to ASP.NET Core in .NET 6](https://gist.github.com/davidfowl/0e0372c3c1d895c3ce195ba983b1e03d)
- [C# 中的 null 包容运算符 “!” —— 概念、由来、用法和注意事项](https://zhuanlan.zhihu.com/p/344575879)
- [Manually add .vscode](https://github.com/OmniSharp/omnisharp-vscode/issues/3702)
