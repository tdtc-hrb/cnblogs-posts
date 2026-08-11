---
title: "PIC blinked - standard"
description: "q10 family - pic18f"
date: 2026-08-03T20:08:08+08:00
---
- MPLAB X - v6.35
- Proteus - v8.16

The example below demonstrates how to configure the microcontroller to turn on an LED when a user button is
pressed. To achieve this, the user needs to identify the pins of the microcontroller routed to the user LED and to the
user button.

### VSM
- RC5    
LED
- RC7    
Button

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/blinked46q10a.png)

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/blinked46q10b.png)

### code
```
#include <xc.h>
void main(void)
{
    /* setting pin RC5 as output (LED) */
    TRISCbits.TRISC5 = 0;
    /* setting pin RC7 as input (button) */
    TRISCbits.TRISC7 = 1;
    /* enable digital input buffer for pin RC7 (button) */
    ANSELCbits.ANSELC7 = 0;
    /* enable internal pull-up for pin RC7 (button) */
    WPUCbits.WPUC7 = 1;

    while(1)
    {
        if(PORTCbits.RC7) {
            LATC &= ~_LATC_LATC5_MASK;
        }
        else {
            LATC |= _LATC_LATC5_MASK;
        }
    }
}
```

## Ref
- [PIC1000: Getting Started with Writing C-Code for PIC16F and PIC18F](https://www.microchip.com/en-us/application-notes/tb3261)
