---
title: "avr gnu toolchain 开发环境"
description: "mplab x and Atmel Studio"
date: 2026-08-15T22:08:08+08:00
---
- [avr-gcc-<version num>-x64-windows](https://github.com/ZakKemble/avr-gcc-build/releases)

Copy the `.\avr\include\avr` folder 
from [avr8-gnu-toolchain-4.0.0.52-win32.any.x86_64.zip](https://www.microchip.com/en-us/tools-resources/develop/microchip-studio/gcc-compilers) 
to `.\avr\include`

## MPLAB X v6.35 - Toolchain
```
Tools -> Options -> Embedded
```
Add (Base Directory):
```
C:\Users\tdtc\Programs\avr8-gnu-toolchain\bin
```

## Microchip Studio(Atmel Studio) - Toolchain
```
Tools -> Options -> Toolchain
Package Configuration
```
### xc8 (update)
Toolchains: Microchip XC8 AVR 8-bit (C language)

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/xc8-as7.png)

**安装as7时，取消xc8的安装**

### avr8-gcc (update)
Toolchains: Atmel AVR 8-bit (C language)

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/avr8-gcc_a_as7.png)

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/avr8-gcc_b_as7.png)

#### Replaced - other way
removed "avr8-gnu-toolchain" at:
```
C:\Program Files (x86)\Atmel\Studio\7.0\toolchain\avr8\
```
copy new version avr8-gnu-toolchain.

### AS 7.0 is no longer maintained
This means that new devices will not be able to appear in its device list.

For example: "AVR32DD28" etc.

## example
```
#define F_CPU 24000000UL
#include <avr/io.h>
#include <avr/interrupt.h>
#include <util/delay.h>

int main(void)
{
    CCP = CCP_IOREG_gc; // Protected write
    CLKCTRL.OSCHFCTRLA = CLKCTRL_FRQSEL_24M_gc; // Set clock to 24MHz
    
    PORTD.DIR = 0xFF;

    while (1)
    {
        _delay_ms(500);
        PORTD.OUT = 255;
        _delay_ms(500);
        PORTD.OUT = 0;
    }
}
```
The macro [F_CPU](https://onlinedocs.microchip.com/oxy/GUID-78362176-487F-41B9-95C7-B478A9A186EB-en-US-2/GUID-19D17420-19A7-43D6-9517-1FAD8F0A3143.html) 
specifies the CPU frequency to be considered by the delay macros.

## Ref
- [example](https://github.com/swharden/AVR-projects/)
