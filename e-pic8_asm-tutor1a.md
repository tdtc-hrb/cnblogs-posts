---
title: "MPASM to MPLAB XC8 PIC Assembler - Directive"
description: "Equivalent Directives"
date: 2026-08-06T08:08:08+08:00
---
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
|-|-|-|
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
