---
title: "MPASM to MPLAB XC8 PIC Assembler"
description: "v0.1"
date: 2023-06-18T08:08:08+08:00
---
|old|Alternate Products|
|-|-|-|-|
| [PIC16F84-1998](https://www.microchip.com/en-us/product/PIC16F84) |pic16f15243|
| [PIC16F877a-2001](https://www.microchip.com/en-us/product/PIC16F877A) |PIC16F18877|

- MPASM    
[MPLAB X v5.35(x64) - 2020.02.21](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLABX-v5.35-windows-installer.exe)

- PIC-AS    
[MPLAB X v5.40+](https://www.microchip.com/en-us/tools-resources/develop/mplab-x-ide) and 
[XC8 v2.41](https://www.microchip.com/en-us/tools-resources/develop/mplab-xc-compilers/downloads-documentation#XC8)

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

MPASM:
```
__CONFIG  _WDT_OFF & _PWRTE_ON & _CP_OFF
```
PIC Assembler:
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
|B'binary_digits' |Binary| binary_digitsB|
|O'octal_digits' |Octal| octal_digits[O|o|Q|q]
|D'decimal_digits' <br>or .decimal_digits</br>|Decimal| decimal_digits[D|d|nothing]|
|H'hexadecimal_digits' or <br>0xhexadecimal_digits</br>|Hexadecimal| 0hexadecimal_digits[H|h] or <br>0xhexadecimal_digits</br>|
|A'character' or 'character'| ASCII| 'character'|

- MPASM
```
movlw b'10110011' ; binary value
movlw o'72' ;octal value
movlw d'34' ;decimal value
movlw 4F ;hexadecimal value
movlw ‘b’ ;ASCII value
```

- PIC Assembler
```
movlw 10110011B ;binary value
movlw 72q ;octal value
movlw 34 ;decimal value
movlw 04Fh ;hexadecimal value
movlw ‘b’ ;ASCII value
```

## lacing Psects into Memory
|MPASM|XC8 Assembler|
|-|-|
|ORG|PSECT|

MPASM supports absolute mode, using the ORG directive, or relocatable mode, using program sections.    
PIC Assembler only supports the relocatable mode.    

Since the tutorials use the absolute mode you will need to remove the ORG directive and add a program section for the code and data.    
I have just added a code section for all the instructions and used the already defined equates to access the data memory variables (user registers).    
So, I have just replaced the ORG directive with the PSECT directive like this:
```
PSECT MyCode,class=CODE,delta=2
```

To make sure the linker places this section at address 000h, 
we need to provide an additional command line parameter:    
Go to “Project Properties” > “pic-as Global Options” > “Additional Options:” and add the following parameter (without spaces!): 
```
-Wl,-pMyCode=0h
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


# Directive
Table 4-1.Equivalent Directives

|MPASM Directive |PIC Assembler Replacement|
|-|-|
|ACCESS_OVR |A psect with the ovrld flag set|
|__BADRAM and __BADROM No replacement|
|BANSISEL |The instruction sequence to write indirect access registers|
|BANKSEL |The BANKSEL directive (no change required)|
|CBLOCK |Consider the SET/EQU directives, or DS.|
|CODE |The code psect or similar|
|CODE_PACK |The code psect or similar|
|__CONFIG |The CONFIG directive with appropriate settings and values|
|CONFIG |The CONFIG directive with appropriate settings and values|
|CONSTANT |The EQU directive|
|DA |Consider the DB or IRPC directives|
|DATA |Consider the DW directive|
|DB |The DB directive|
|DE |Consider the DB directive inside a suitable psect|
|#DEFINE |The #define preprocessor directive|
|DT |The IRP directive|
|DTM |The IRP directive|
|DW |The DW or DB directive|
|ELSE |The ELSE directive (no change required)|
|END |The END directive (no change required)|
|ENDC |No replacement|
|ENDM |The ENDM directive (no change required)|
|ENDW |No replacement|
|EQU |The EQU directive (no change required)|
|ERROR |The ERROR directive|
|ERRORLEVEL |Consider the -w driver option|
|EXITM |No replacement|
|EXPAND |The EXPAND directive (no change required)|
|EXTERN |The EXTRN directive (note different spelling)|
|FILL |Consider the --fill driver option|
|GLOBAL |The GLOBAL directive (no change required)|
|IDATA |A psect with the initial values in program memory and another reserving space for the data objects|
|IDATA_ACS |A psect with the initial values in program memory and another reserving space for the data objects|
|IF |The IF directive (no change required)|
|IFDEF |Consider the #ifdef preprocessor directive|
|IFNDEF |Consider the #ifndef preprocessor directive|
|#INCLUDE |The #include preprocessor directive|
|LIST |The LIST directive or consider alternate assembler options|
|LOCAL |The LOCAL directive (no change required)|
|MACRO |The MACRO directive (no change required)|
|__MAXRAM <sub>and</sub> __MAXROM |No replacement|
|MESSG |The MESSG directive (no change required)|
|NOEXPAND |The NOEXPAND directive (no change required)|
|NOLIST |The NOLIST directive (no change required)|
|ORG |Consider the ORG directive|
|PAGE |No replacement|
|PAGESEL |The PAGESEL directive (no change required)|
|PAGESELW |Consider the PAGESEL directive|
|PROCESSOR |The PROCESSOR directive (no change required)|
|RADIX |The RADIX directive (no change required)|
|RES |Consider the DS directive|
|SET |The SET directive (no change required)|
|SPACE |The SPACE directive (no change required)|
|SUBTITLE |The SUBTITLE directive (no change required)|
|TITLE |The TITLE directive (no change required)|
|UDATA |The udata_bankn psect or similar|
|UDATA_ACS |The udata_acs psect or similar|
|UDATA_OVR |A psect with the ovrld flag set|
|UDATA_SHR |A psect with the ovrld flag set|
|#UNDEFINE |The #undefine preprocessor directive|
|VARIABLE |Consider the SET directive|
|WHILE |Consider the REPT directive|


### List Directive
TABLE 4-2: LIST DIRECTIVE OPTIONS

|Option| Default| Description|
|-|-|
|b=nnn| 8 |Set tab spaces.|
|c=nnn| 132| Set column width.|
|f=format| INHX8 <br>M</br> |Set the hex file output. format can be INHX32, INHX8M, or INHX8S. <br>Note: Hex file format is set in the IDE (Build Options dialog.)</br>|
|free| FIXED| Use free-format parser. Provided for backward compatibility.|
|fixed| FIXED| Use fixed-format parser.|
|mm={ON|OFF}| On| Print memory map in list file.|
|n=nnn| 60| Set lines per page.|
|p=type| None| Set processor type; for example, PIC16F877. See also processor. <br>Note: Processor type is set in MPLAB IDE or MPLAB X IDE projects.</br>
|pe=type| None| Set processor type and enable extended instruction set, for example; LIST pe=PIC18F4620 <br>Only valid with processors which support the extended instruction set and the generic processor PIC18XXX. Is overridden by command-line option /y- or -y- (disable extended instruction set).<br>Note: Processor type is set in MPLAB IDE or MPLAB X IDE projects.</br>
|r=radix| hex| Set radix: hex, dec, oct. See also radix.|
|st={ON|OFF}| On| Print symbol table in list file.|
|t={ON|OFF}| Off| Truncate lines of listing (otherwise wrap).|
|w={0|1|2}| 0| Set the message level. See also errorlevel.|
|x={ON|OFF}| On| Turn macro expansion on or off|

### Define a Block Of Constants
Do not use <b>cblock</b> or <b>equ</b> to define variable location names for relocatable code.
```
CBLOCK = accumulative equ
```
- CBLOCK
```
cblock 0x20 ; name_1 will be assigned 20
name_1, name_2 ; name_2, 21 and so on
name_3, name_4 ; name_4 is assigned 23.
endc
```
- EQU
```
    name_1    EQU 0x20
    name_2    EQU 0x21
    name_3    EQU 0x22
    name_4    EQU 0x23
```

### TRIS register
In order to define a pin as input or output pin, 
the right combination of zeros and ones must be written in TRIS register.
```
TRIS: The Register Input/output State.

TRISA: which is the address of the Tri-State register for PortA
...
TRISC: which is the address of the Tri-State register for PortC
```



### banksel
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
