---
title: "PIC blinked"
description: "pic16f887/pic16f877a & pic16f18877"
date: 2026-07-13T08:08:08+08:00
---
- MPLAB X v6.35
- [Proteus v8.16](https://www.jb51.net/softs/905053.html)

# Blinking LED
- First, use the MPLAB X to generate hex;    
- Then, use protelus to draw "Schematic Capture";

![blink-16f877a](https://github.com/tdtc-hrb/cnblogs/raw/main/images/schematic_capture-887a.png)

- Finally, import the hex file into the MCU component.
```
Right-click the "mcu" icon and select "Edit Properties".
```
![load hex](https://electrosome.com/wp-content/uploads/2012/05/Proteus-Edit-Component-PIC-16F877A.jpg)

## PIC16F887
- LED
- 470R Resistor
- 8Mhz Crystal
- Two 22pF capacitors in parallel

```asm
    PROCESSOR   16F887
#include <xc.inc>

  config FOSC=INTRC_NOCLKOUT
  config WDTE=OFF, PWRTE=ON, MCLRE=ON, CP=OFF, CPD=OFF, BOREN=OFF, IESO=OFF, FCMEN=OFF, LVP=OFF

#define Count1 0x20
#define Count2 0x21

PSECT   MainCode,global,class=CODE,delta=2
MAIN:
    BANKSEL     TRISB
    BCF         TRISB,0
    BANKSEL     PORTB
MainLoop:
    BCF         PORTB,0
    CALL        DELAY3
    BSF         PORTB,0 
    CALL        DELAY3
    GOTO        MainLoop

DELAY3:
    DECFSZ      Count1,1
    GOTO        DELAY3
    DECFSZ      Count2,1
    GOTO        DELAY3
    RETURN

END
```

## Ref
- [pic16F887](https://web.archive.org/web/20260120160017/https://www.fypsolutions.com/assembly-language/pic-microcontroller-led-blink-code-example-in-assembly-language)
