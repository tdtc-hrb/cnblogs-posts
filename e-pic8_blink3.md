---
title: "PIC blinked - init"
description: "Interrupts are disabled and enabled during initialization."
date: 2026-08-10T08:08:08+08:00
---
```
; Disable all interrupts
; some interrupt code ;

; Other peripheral initialization code.

; Enable system interrupts
; some interrupt code ;
```
### Disable all interrupts
```
    clrf    INTCON      ; Disable all interrupts
    banksel PIE0
    clrf    PIE0
    clrf    PIE1
    clrf    PIE2
    clrf    PIE3
    clrf    PIE4
    clrf    PIE5
    clrf    PIE6
    clrf    PIE7
    clrf    PIE8
```

### Enable system interrupts
```
;
; Enable system interrupts
;
    bsf     INTCON,INTCON_PEIE_POSITION
    bsf     INTCON,INTCON_GIE_POSITION
```

## Ref
- [adc+uart+timer0](https://github.com/dsoze1138/MPLABX_pic-as_examples/tree/master/16F18877_DN164136_ADC_UART_v615.X)
