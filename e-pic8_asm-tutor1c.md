---
title: "MPASM to MPLAB XC8 PIC Assembler - Psects"
description: "lacing Psects into Memory"
date: 2023-06-18T08:08:08+08:00
---
- MPASM supports absolute mode, using the ORG directive, or relocatable mode, using program sections.
- PIC Assembler only supports the relocatable mode.

## lacing Psects into Memory
|MPASM|XC8 Assembler|
|-|-|
|ORG|PSECT|

- Since the tutorials use the absolute mode you will need to remove the ORG directive and add a program section for the code and data.    
I have just added a code section for all the instructions and used the already defined equates to access the data memory variables (user registers).
So, I have just replaced the ORG directive with the PSECT directive like this:
```
PSECT Reset_Vec,class=CODE,delta=2
```
other:
```
PSECT   ISR_Vec,class=CODE,delta=2
```

- To make sure the linker places this section at address 000h and 004h, 
we need to provide an additional command line parameter:    
Go to “Project Properties” > “pic-as Global Options” > “Additional Options:” and add the following parameter (without spaces!): 
```
-Wl,-pReset_Vec=0h,-pISR_Vec=4h
```

### About section name
Where “MyCode” is the section name, “class=CODE” tells the linker to use the default code section, 
“delta=2" tells the linker the number of data bytes that are associated with each address.

We can also just define a code section like this:
```
PSECT code
```
But I’d rather define my own code section “MyCode” so that I can tell the linker to specifically place this code section at the zero address.    
Otherwise, the linker will place it somewhere else, 
and it will probably overwrite the RC oscillator calibration instruction at the top of the memory (address 0FFh).

### class - psect flag
Table 5-2. Assembler-provided Psects and Linker Classes

|Psect name| Linker class| Target device families |Purpose|
|-|-|-|-|
|code |CODE |All |To hold executable code|
|eedata| EEDATA |All| To hold data in EEPROM|
|data| STRCODE| Baseline, Mid-range |To hold data in program memory|
|udata| RAM| All| To hold objects allocatable anywhere in GPR|
|udata_bankn| BANKN| All| To hold object allocatable in a particular data memory bank|
|udata_shr| COMMON| Baseline, Mid-range |To hold objects allocatable in common memory|

### delta - psect flag
The delta psect flag defines the size of the addressable unit.    
In other words, the number of data bytes that are associated with each address.    
With PIC Mid-range and Baseline devices, the program memory space is word addressable; so, psects in this space must use a delta of 2.    
That is to say, each address in program memory requires 2 bytes of data in the HEX file to define their contents.    
So, addresses in the HEX file will not match addresses in the program memory.    
The data memory space on these devices is byte addressable; so, psects in this space must use a delta of 1.    
This is the default delta value.    
All memory spaces on PIC18 devices are byte addressable; so a delta of 1 (the default) should be used for all psects on these devices.    
The redefinition of a psect with conflicting delta values can lead to phase errors being issued by the assembler.

### global - psect flag
the global psect flag indicates that the linker should concatenate this psect with global psects in other modules and which have the same name.


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
