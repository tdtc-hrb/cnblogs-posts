---
title: "Build your own PHP on Windows"
date: 2023-08-20T10:08:08+08:00
---

- Visual C++ 14.0 (Visual Studio 2015) for PHP 7.0 or PHP 7.1.
- Visual C++ 15.0 (Visual Studio 2017) for PHP 7.2, PHP 7.3 or PHP 7.4.
- Visual C++ 16.0 (Visual Studio 2019) for master.

1. Enter build
```bash
git clone https://github.com/Microsoft/php-sdk-binary-tools
cd php-sdk-binary-tools
phpsdk-vs16-x64.bat
``` 

2. Gen deps directory
```bash
phpsdk_buildtree phpdev
```

3. git php src
```
cd d:\progFiles\phpdev\vs16\x64
git clone -b PHP-8.0.* https://github.com/php/php-src.git
```
*: version number

4. deps updates
```bash
phpsdk_deps --update
```

5. build
```bash
buildconf && configure && nmake
```

- [Build your own PHP on Windows](https://wiki.php.net/internals/windows/stepbystepbuild_sdk_2)
