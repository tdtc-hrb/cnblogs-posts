---
title: "In-Circuit Debugger and pic product"
description: "ice, icd and product number"
date: 2026-07-12T08:08:08+08:00
---
- [In-Circuit Emulator and Debugger Selection Guide](https://www.microchip.com/en-us/tools-resources/debug/programmers-debuggers)

|Feature|ICE 4|ICD|PICkit|
|-|-|-|-|
|Part Number|DV244140|DV164055|PG164150|
|MSRP|$1,799.00|$399.99|$94.99|

## How to connect the pins from the PICkit
- Vpp/MCLR
- V<sub>DD</sub> Target (Power on Target) : ‘ + ‘
- V<sub>SS</sub> (Ground) : ‘ – ‘.
- ICSPDAT/PGD (Standard COM Data)
- ICSPCLK/PGC (Standard COM Clock)
- LVP (Low Voltage Programming)
```
pic16f887 - connect
pic16f18877 - not connect
```

![pic16f887-c9.svg](https://www.northernsoftware.com/nsdsp/gr/c9.svg)
![pic16f18877-c4.svg](https://www.northernsoftware.com/nsdsp/gr/c4.svg)

### Ref
- [PIC16F887](https://www.northernsoftware.com/dev/pic16f/pic16f887.htm)
- [PIC16F18877](https://www.northernsoftware.com/dev/pic16f/pic16f18877.htm)

## PIC product
- [PIC16F18877-I/P](https://www.digikey.cn/zh/products/detail/microchip-technology/PIC16F18877-I-P/6098396)
- [PIC16F18877-I/P](https://www.mouser.cn/zh/ProductDetail/Microchip-Technology/PIC16F18877-I-P?qs=BA62vJVifGqHcm2iAjr4Vw%3D%3D)

The PIC16F877A is quite an old design, supplanted by the PIC16F887,
 which also is an old design which itself was replaced by the 16F18877.

from [EE_Tim](https://www.reddit.com/r/embedded/comments/qpgji7/comment/hjtkwpm/)

### Temperature
E/SP:
```
-40C to +125C
```
I/SP:
```
-40C to +85C
```
"SP" for SPDIP in "Skinny Dual In-line Package"; "I" for "industrial grade"
["E" is likely "Extended Temperature"](https://electronics.stackexchange.com/a/10076)
