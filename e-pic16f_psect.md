---
title: "psect - Pseudo instruction"
description: "program section"
date: 2026-07-14T10:08:08+08:00
---
```
PSECT psect_name, flags
```
### Table 4-10. Psect Flags
|Flag|Meaning|
|-|-|
|abs| psect is absolute.|
|bit| psect holds bit objects.|
|class=name| Specify class name for psect.|
|delta=size| Size of an addressing unit.|
|global| psect is global (default).|
|inline| psect contents (function) can be inlined when called.|
|keep| psect will not be deleted after inlining.|
|limit=address| Upper address limit of psect (PIC18 only).|
|local| psect is unique and will not link with others having the same name.|
|lowdata| psect will be entirely located below the 0x1000 address.|
|merge=allow| Allow or prevent merging of this psect.|
|noexec| For debugging purposes, this psect contains no executable code.|
|note| psect does not contain any data that should appear in the program image.|
|optim=optimizations| specify optimizations allowable with this psect.|
|ovrld| psect will overlap same psect in other modules.|
|pure| psect is to be read-only.|
|reloc=boundary| Start psect on specified boundary.|
|size=max| Maximum size of psect.|
|space=area| Represents area in which psect will reside.|
|split=allow| Allow or prevent splitting of this psect.|
|with=psect| Place psect in the same page as specified psect.|

## Ref
- [MPLAB XC8](https://www.microchip.com/en-us/tools-resources/develop/mplab-xc-compilers/xc8)
- [MPLAB XC8 PIC Assembler Users Guide](https://ww1.microchip.com/downloads/aemDocuments/documents/DEV/ProductDocuments/UserGuides/MPLAB-XC8-PIC-Assembler-Users-Guide-DS50002974.pdf)
- [MPASM to MPLAB XC8 PIC Assembler Migration Guide](https://ww1.microchip.com/downloads/aemDocuments/documents/DEV/ProductDocuments/UserGuides/MPASM-to-MPLAB-XC8-PIC-Assembler-Migration-Guide-50002973.pdf)
