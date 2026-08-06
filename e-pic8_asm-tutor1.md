---
title: "MPASM to MPLAB XC8 PIC Assembler"
description: "v0.1"
date: 2023-06-18T08:08:08+08:00
---
- [tutor1a](e-pic8_asm-tutor1a.md)
- [tutor1b](e-pic8_asm-tutor1b.md)
- [tutor1c](e-pic8_asm-tutor1c.md)

## specific controller
|MPASM|XC8 Assembler|
|-|-|
|LIST P = DeviceName|PROCESSOR   DeviceName|

LIST (indicates specific controller, e.g., LIST P=18F452) unique to PIC assembler.

## Include Files
|MPASM|XC8 Assembler|
|-|-|
|INCLUDE <DeviceName.INC>|#include <xc.inc>|

## Set device's configuration bits
|MPASM|XC8 Assembler|
|-|-|
|__CONFIG |The CONFIG directive with appropriate settings and values|

- MPASM:
```
__CONFIG  _WDT_OFF & _PWRTE_ON & _CP_OFF
```
- PIC Assembler:
```
CONFIG WDT   = OFF
CONFIG PWRTE = ON
CONFIG CP    = OFF
```

## Label name
Labels in PIC Assembler must be followed by a colon “:”    
In MPASM the colon is optional.

## Constants and Radices
By default, MPASM interprets the numeric constants as hexadecimal values.    
PIC Assembler interprets them by default as decimal values!    
This is where explicit definition can help with code portability.

|MPASM|XC8 Assembler|
|-|-|
|-|RADIX DEC|

### numeric constants
Table 3-1. Equivalent Constants Radix Specifiers

|MPASM Constant Forms |Radix |PIC Assembler Equivalent|
|-|-|-|
|B'binary_digits' |Binary| binary_digitsB|
|O'octal_digits' |Octal| octal_digits[O|o|Q|q]
|D'decimal_digits' <br>or .decimal_digits</br>|Decimal| decimal_digits[D|d|nothing]|
|H'hexadecimal_digits' or <br>0xhexadecimal_digits</br>|Hexadecimal| 0hexadecimal_digits[H|h] or <br>0xhexadecimal_digits</br>|
|A'character' or 'character'| ASCII| 'character'|

#### MPASM
```
movlw b'10110011' ; binary value
movlw o'72' ;octal value
movlw d'34' ;decimal value
movlw 4F ;hexadecimal value
movlw ‘b’ ;ASCII value
```
- decimal
```
movlw .34 ;decimal value
```

#### PIC Assembler
```
movlw 10110011B ;binary value
movlw 72q ;octal value
movlw 34 ;decimal value
movlw 04Fh ;hexadecimal value
movlw ‘b’ ;ASCII value
```

# Ref
- [MPASM to MPLAB XC8 PIC Assembler Migration Guide 50002973A](https://ww1.microchip.com/downloads/en/DeviceDoc/MPASM%20to%20MPLAB%20XC8%20PIC%20Assembler%20Migration%20Guide%2050002973A.pdf)
- [MPLAB XC8 PIC Assembler User's Guide 50002974A](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20PIC%20Assembler%20User%27s%20Guide%2050002974A.pdf)
- [MPLAB-XC8-PIC-Assembler-User-Guide-50002974](https://ww1.microchip.com/downloads/aemDocuments/documents/DEV/ProductDocuments/UserGuides/MPLAB-XC8-PIC-Assembler-User-Guide-50002974.pdf)
- [MPLAB XC8 PIC Assembler User's Guide for Embedded Engineers](https://ww1.microchip.com/downloads/en/DeviceDoc/50002994B.pdf)
- [MPASM to MPLAB XC8 PIC Assembler](https://www.circuitbread.com/tutorials/mpasm-to-mplab-xc8-pic-assembler)
- [When to use banksel](https://microcontrollerlog.wordpress.com/2012/08/17/when-to-use-banksel/)
- [There is no make executable in the path. - MPLABX v5.35](https://forum.microchip.com/s/topic/a5C3l000000MbBxEAK/t373025)
- [4.43 list - Listing Options](https://ww1.microchip.com/downloads/en/devicedoc/33014l.pdf)
- [4.8.5 Simple Example - MPASM Assembler User's Guide](https://ww1.microchip.com/downloads/en/devicedoc/33014l.pdf)
- [What is the meaning of Microchip's acronym TRIS for data direction registers?](https://electronics.stackexchange.com/a/12364)
- [2.4 Ports](http://www.matidavid.com/pic/picbook_site/2_05chapter.htm)
- [MPASM Removed? !!!](https://forum.microchip.com/s/topic/a5C3l000000McGfEAK/t377161?comment=P-2834053)
