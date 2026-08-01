---
title: "blinked - pic-as"
description: "pic16f887"
date: 2026-07-31T08:08:08+08:00
---
- TRIS: Set port/bit direction (input or output)
- LAT: Set port state when writing a port bit.
- PORT: Read present state of port bits.

Always setup TRIS in initialization and as needed.
Always write to the LAT (never the PORT)
Read the PORT for the bit status.

You can read the LAT, but it always what you wrote, 
it does not contain any "live " readings.

### Tris x - tri-state
TRISA, TRISB, and TRISC are data direction registers in PIC microcontrollers that 
configure the individual pins of corresponding ports (PORTA, PORTB, and PORTC) as either digital inputs or outputs.

Similar to LPC's FIODIR (Fast I/O Direction Register)

### [Port x](https://forum.microchip.com/s/topic/a5C3l000000MPO4EAO/t327648?comment=P-2587932)
**Make a habit of reading from the PORT register, and writing to the LAT register, and you will avoid problems.**

#### Latch x
```
LATA, LATB, LATC, LATD, LATE
```
Neither the PIC16F877A nor the classic PIC16F887 features a hardware LATB (Data Latch) register in its physical architecture; 
instead, input and output operations rely directly on the PORTB and TRISB registers.

### blinked - pic-as
```
    PROCESSOR   16F887
#include <xc.inc>

; _CONFIG1, _INTOSCIO & _WDT_OFF & _PWRTE_ON & _MCLRE_ON & _CP_OFF & _CPD_OFF & _BOR_OFF & _IESO_OFF & _FCMEN_OFF & _LVP_OFF & _DEBUG_ON
  CONFIG  FOSC = INTRC_NOCLKOUT ; Oscillator Selection bits (INTOSCIO oscillator: I/O function on RA6/OSC2/CLKOUT pin,I/O function on RA7/OSC1/CLKIN)
  CONFIG  WDTE = OFF            ; Watchdog Timer Enable bit (WDT disabled and can be enabled by SWDTEN bit of the WDTCON register)
  CONFIG  PWRTE = ON            ; Power-up Timer Enable bit (PWRT enabled)
  CONFIG  MCLRE = ON            ; RE3/MCLR pin function select bit (RE3/MCLR pin function is MCLR)
  CONFIG  CP = OFF              ; Code Protection bit (Program memory code protection is disabled)
  CONFIG  CPD = OFF             ; Data Code Protection bit (Data memory code protection is disabled)
  CONFIG  BOREN = OFF           ; Brown Out Reset Selection bits (BOR disabled)
  CONFIG  IESO = OFF            ; Internal External Switchover bit (Internal/External Switchover mode is disabled)
  CONFIG  FCMEN = ON            ; Fail-Safe Clock Monitor Enabled bit (Fail-Safe Clock Monitor is enabled)
  CONFIG  LVP = OFF             ; Low Voltage Programming Enable bit (RB3 pin has digital I/O,HV on MCLR must be used for programming)

; CONFIG2
  CONFIG  BOR4V = BOR21V        ; Brown-out Reset Selection bit (Brown-out Reset set to 2.1V)
  CONFIG  WRT = OFF             ; Flash Program Memory Self Write Enable bits (Write protection off)

    PSECT reset_vec,class=CODE,delta=2
ResetVec:
    PAGESEL Main
    GOTO    Main

    PSECT   common_data,class=COMMON,space=1,delta=1,noexec
    GLOBAL  Count1,Count2

Count1: ds 1
Count2: ds 1

    ; ORG   0
    PSECT   main_code,class=CODE,delta=2
Main:
    BANKSEL     TRISB
    bcf         TRISB,0
    BANKSEL     PORTB
Loop:
    bcf         PORTB,0
    CALL        Delay3
    bsf         PORTB,0
    CALL        Delay3
    GOTO        Loop

Delay3:
    decfsz      Count1,1
    GOTO        Delay3
    decfsz      Count2,1
    GOTO        Delay3
    RETURN

END

```

## Ref
- [porta, lata, trisa ?](https://forum.microchip.com/s/topic/a5C3l000000LyKqEAK/t223660?comment=P-1906772)
