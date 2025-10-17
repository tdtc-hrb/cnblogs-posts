---
title: "Qt6 for android"
description: "cmdline, ndk"
date: 2025-10-03T12:38:08+08:00
---

- ndk
- avd(Android Virtual Device)


# Preparation
- Environment
- cmdline

## Environment variables
:warning:    
After installing [Android Studio](https://developer.android.google.cn/studio), don't go into settings yet!

### Java Home
- Qt6.8/6.5    
set [JDK17](https://adoptium.net/temurin/releases?version=17&os=any&arch=any)
```bash
C:\java\jdk-7.0.16+8
```

### [Android Home](https://developer.android.com/tools/variables#envar)
Sets the path to the SDK installation directory. 
Once set, the value does not typically change and can be shared by multiple users on the same machine. 
ANDROID_SDK_ROOT, which also points to the SDK installation directory, is deprecated. 
If you continue to use it, Android Studio and the Android Gradle plugin will check that the old and new variables are consistent. 

default value:
```bash
C:\Users\<user name>\AppData\Local\Android\Sdk
```

### open ssl
Download "[android openssl](https://github.com/KDAB/android_openssl)" 
and place it in the "%ANDROID_SDK_ROOT%"/"%ANDROID_HOME%" directory.


## cmdline-tools
Qt Creator's version info(sdk_definitions.json):
```
<QT_INSTALL>\Tools\QtCreator\share\qtcreator\android
```

### [versions](https://packages.debian.org/source/sid/google-android-installers)
|ver|name|qt creator|jdk|
|-|-|-|-|
|2.1|6609375_latest|v5.0.2|v8|
|3.0|6858069_latest|none|v8|
|4.0|7302050_latest|none|v8|
|5.0|7583922_latest|none|v8|
|6.0|8092744_latest|none|v8|
|7.0|8512546_latest|none|v8|
|8.0|9123335_latest|v11.0.3|v8|
|9.0|9477386_latest|none|v11|
|10.0|9862592_latest|none|v11|
|11.0|10406996_latest|none|v17|
|12.0|11076708_latest|v14.0.1|v17|
|13.0|11479570_latest|none|v17|

Take v12.0 as an example: Open [list of files](https://packages.debian.org/sid/i386/google-android-cmdline-tools-12.0-installer/filelist) 
and see the version information: <strong>11076708_latest</strong>
```
/usr/share/google-android-cmdline-tools-12.0-installer/commandlinetools-linux-11076708_latest.zip.sha1
```

### Download
down [Command line tools only](https://dl.google.com/android/repository/commandlinetools-win-10406996_latest.zip) 
放置到"Android Home"目录下, 
解压缩(extract here)这个文件, 进入目录(cmdline-tools), 建立一个新的子文件夹
```
latest
```
把所有文件(夹)移到这个子目录(latest)中。
```
bin
lib
NOTICE.txt
source.properties
```


# ndk
|Qt version|NDK [version](https://github.com/android/ndk/wiki/Unsupported-Downloads)|
|-|-|
|Qt 6.8|r26b(26.1.10909125) and r25b|
|Qt 6.5|r25b(25.1.8937393)|

usb driver:
```bash
sdkmanager --sdk_root=%ANDROID_SDK_ROOT% --install "extras;google;usb_driver"
```

### Qt6.8
- Android SDK
```
sdkmanager --sdk_root=%ANDROID_HOME% --install "platform-tools" "platforms;android-35" "build-tools;35.0.1"
```

- Android NDK
```
sdkmanager --sdk_root=%ANDROID_HOME% --install "ndk;25.1.8937393" "ndk;26.1.10909125"
```

### Qt6.5
- Android SDK
```
sdkmanager --sdk_root=%ANDROID_HOME% --install "platform-tools" "platforms;android-31" "build-tools;31.0.0"
```
```
sdkmanager --sdk_root=%ANDROID_HOME% --install "platform-tools" "platforms;android-33" "build-tools;33.0.0"
```

- Android NDK
```
sdkmanager --sdk_root=%ANDROID_HOME% --install "ndk;25.1.8937393"
```


# avd
install emulator:
```bash
sdkmanager --sdk_root=%ANDROID_HOME% --install "emulator" "patcher;v4"
```

### Hypervisor
If you use android_x86/android_x86_64, you must install HAXM and AEHD.
- HAXM
```
android_x86
```
- AEHD
```
android_x86_64
```

# network issues
- Gradle

### [Gradle](https://mirrors.aliyun.com/gradle)
- qt creator
> v17.0.2

Put "gradle-8.10-bin.zip" into a random folder under
```
C:\Users\<USER>\.gradle\wrapper\dists\gradle-8.10-bin
```
for example - random folder:
```
deqhafrv1ntovfmgh0nh3npr9
```

# Ref
- [qt6](https://doc.qt.io/qt-6/android-getting-started.html)
- [qt6.8](https://doc.qt.io/qt-6.8/android-getting-started.html)
- [qt6.5](https://doc.qt.io/qt-6.5/android-getting-started.html)
