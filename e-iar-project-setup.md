---
title: "iar 工程基本设置"
description: "sam4s-ek2"
date: 2020-07-02T08:08:08+08:00
---
> IAR7.50
>> J-Link 5.10m

建立一个c工程：    
  project -> Create new project    
  “Project Templates”选择empty project    
  选择工程目录下的prj（可自定义，主要是放IAR生成的相关文件）
> 注意：没有更改的项，使用缺省设置！

# General Options
  主要设置：目标芯片, CMSIS库, 打印和输入函数

## 设备
> Target
>> Device

```bash
Atmel SAM4SD32C
```

## CMSIS
> Library Configuration    

  ✔ Verify DownloadUse CMSIS    
     ✔ DSP library

## 不使用低级库
> Library Configuration

  Library low-level interface implementation
```bash
None
```

## 打印和输入
> Library Options    

  Printf formatter
```bash
Large
```
  Scanf formatter
```bash
small
```

---

# Runtime Checking
> Note：一定要设置Preprocessor（C/C++和Assembler）！！！    

  在C/C++ Complie和Linker中开启诊断程序：
> Diagnostics    
  ✔ Enable remarks

## C/C++ Complie
  设置方言和程序的大小
### C方言设置
> Language1
>>  C dialect

```bash
Require prototype
```

### 生成最小的程序
> Optimizations
>> Level

  High
```bash
Size
```

## 生成格式
> Output Converter
>> output

```bash
binary
```
  ✔  “Override default”

## Linker
> Config
  设置脚本
```bash
$PROJ_DIR$/../config/linker_scripts/sam4sd32/flash.icf
```

### 可以指定命令参数
> Extra Options

  在使用rtos，必须指定！
```bash
Use Command line options
```

## Debugger
>  Setup

  Driver
```bash
J-Link/J-Trace
```
### Setup macros
  Use macro file(s)
```bash
$PROJ_DIR$/../config/debug_scripts/sam4s_ek2_flash.mac
```

### Download
  ✔ Verify Download
  ✔ Use flash loader

### 设置起始频率
> J-Link/J-Trace
>> JTAG/SWD speed

  datasheet: 32.768kHz
  Auto
```bash
Initial 32kHz
```

# 附件
## preprocessor列表
> .ewp
### 初始时
main.c和asf.h的引用头文件

```bash
<option>
  <name>CCIncludePath2</name>
  <state>$PROJ_DIR$\..</state>
  <state>$PROJ_DIR$\..\config</state>
  <state>$PROJ_DIR$\..\usr</state>
  <state>$PROJ_DIR$\..\common\utils</state>
  <state>$PROJ_DIR$\..\common\utils\stdio\stdio_serial</state>
  <state>$PROJ_DIR$\..\common\services\clock</state>
  <state>$PROJ_DIR$\..\common\services\gpio</state>
  <state>$PROJ_DIR$\..\common\services\ioport</state>
  <state>$PROJ_DIR$\..\common\services\serial</state>
  <state>$PROJ_DIR$\..\drivers\pio</state>
  <state>$PROJ_DIR$\..\drivers\pmc</state>
  <state>$PROJ_DIR$\..\drivers\tc</state>
  <state>$PROJ_DIR$\..\drivers\uart</state>
  <state>$PROJ_DIR$\..\drivers\usart</state>
  <state>$PROJ_DIR$\..\drivers\pio</state>
  <state>$PROJ_DIR$\..\utils</state>
  <state>$PROJ_DIR$\..\utils\preprocessor</state>
  <state>$PROJ_DIR$\..\utils\header_files</state>
  <state>$PROJ_DIR$\..\utils\cmsis\sam4s\include</state>
</option>
```
