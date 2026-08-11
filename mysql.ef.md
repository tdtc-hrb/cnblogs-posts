---
title: "MySQL EF 的简单使用"
description: "NuGet"
date: 2025-08-08T00:08:08+08:00
---
- [v4.5.2 Developer pack](https://dotnet.microsoft.com/en-us/download/dotnet-framework/net452)
- entityframework:6.4.4

## NuGet
> %USERPROFILE%\AppData\Roaming\NuGet
NuGet.Config:
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <config>
    <add key="http_proxy" value="http://192.168.3.19:8580" />
    <add key="https_proxy" value="http://192.168.3.19:8580" />
  </config>
  <packageRestore>
    <add key="enabled" value="True" />
    <add key="automatic" value="True" />
  </packageRestore>
  <packageSources>
    <add key="nuget.org" value="https://www.nuget.org/api/v2/" />
    <add key="NuGet V3" value="https://api.nuget.org/v3/index.json" />
  </packageSources>
  <activePackageSource>
    <add key="nuget.org" value="https://www.nuget.org/api/v2/" />
  </activePackageSource>
</configuration>
```

### New
Update [NuGet.Tools.vsix](https://dist.nuget.org/visualstudio-2013-vsix/v2.12.0/NuGet.Tools.vsix)
and 
[Visual Studio 2013 NuGet package management not working](https://stackoverflow.com/a/63574949):
```Package Manager Console
PM > [Net.ServicePointManager]::SecurityProtocol=[Net.ServicePointManager]::SecurityProtocol-bOR [Net.SecurityProtocolType]::Tls12
```
Put the following nuget packages into the Offline folder:
- mysql.data.entityframework:8.0.32
- mysql.data:8.0.32.1
```
portable.bouncycastle:1.9.0
Google.Protobuf:3.21.12
K4os.Compression.LZ4.Streams:1.2.6
Portable.BouncyCastle:1.9.0
System.Buffers:4.5.1
System.Runtime.CompilerServices.Unsafe:5.0.0
```

# Ref
- [Entity Framework 6 Support](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
- [Common NuGet configurations](https://learn.microsoft.com/en-us/nuget/consume-packages/configuring-nuget-behavior)
- [Cps_x32-fx](https://github.com/tdtc-hrb/Cps_dotnet-fx)
