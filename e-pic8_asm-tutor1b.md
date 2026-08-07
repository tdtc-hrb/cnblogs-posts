---
title: "MPASM to MPLAB XC8 PIC Assembler - b"
description: "bankmask and banksel"
date: 2026-08-06T08:08:08+08:00
---
|old|Alternate Products|
|-|-|-|-|
| [PIC16F84-1998](https://www.microchip.com/en-us/product/PIC16F84) |pic18f26q10|
| [PIC16F877a-2001](https://www.microchip.com/en-us/product/PIC16F877A) |PIC16F18877|

- MPASM    
[MPLAB X v5.35(x64) - 2020.02.21](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLABX-v5.35-windows-installer.exe)

- PIC-AS    
[MPLAB X v5.40+](https://www.microchip.com/en-us/tools-resources/develop/mplab-x-ide) and 
[XC8 v2.20+](https://www.microchip.com/en-us/tools-resources/develop/mplab-xc-compilers/downloads-documentation#XC8)

### bankmask - macro
It is recommended that you mask all addresses used by PIC file register instructions whose operand represents a bank offset.
```
#include <xc.inc>
copy:
  BANKSEL (src)              ;select the bank of src
  movf     BANKMASK(src),w   ;move from src, masking the address
  BANKSEL (dst)              ;select the bank of dst
  movwf    BANKMASK(dst)     ;move to dst, masking the address
```
The BANKMASK() macro, can perform the mask using the correct values, based on the selected device.

### banksel - Pseudo-instructions
The file registers of the pic are held in banks, 
some are shared across all banks and some program data can also be shared across all banks but this is obviously 
wasteful of memory so it’s limited to select registers and shared data.

Special function registers, SFR’s are used to control, configure and check the pic’s status.    
These tend to be on different banks to the programs variables so you need to change banks or your program will be accessing the wrong bank.    
This is where the ‘banksel’ instruction comes in. It makes sure you are addressing the correct memory bank.

#### When to use banksel
- first access of any UDATA section variable
- first access of register after a subroutine call/external module
- when accessing a banked special function register
- after having accessed a banked SFR(special function register)

#### When to don’t need to use banksel
- after first access to a UDATA variable other UDATA variables are in the same bank
- UDATA_SHR variables are available on any bank
- accessing Non-banked SFR’s like STATUS etc.. can be done from any bank

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
