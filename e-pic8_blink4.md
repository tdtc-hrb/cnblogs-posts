---
title: "PIC blinked - interrupt"
description: "Use the TIMER0 interrupt to control blinking."
date: 2026-08-10T08:08:08+08:00
---
- [Initialation: Peripheral](e-pic8_blink2.md)
- [Initialation: System interrupt](e-pic8_blink3.md)

## INTERRUPT VECTOR
```
; Add this line in the project properties box, pic-as Global Options -> Additional options: 
;    -Wa,-a -Wl,-DCODE=2,-pResetVec=0h,-pISR_Vec=4h
;
```
### Timer0 Vector
```
;
; Interrupt vector
;
    PSECT   ISR_Vec,class=CODE,delta=2
    global  ISR
ISR:
    banksel PIE0
    btfsc   PIE0,PIE0_TMR0IE_POSITION
    btfss   PIR0,PIR0_TMR0IF_POSITION
    goto    ISR_TIMER0_EXIT
    bcf     PIR0,PIR0_TMR0IF_POSITION
    movf    LED_D2_wait,F
    skipnz
    goto    ISR_TIMER0_EXIT
    decfsz  LED_D2_wait,F
    goto    ISR_TIMER0_EXIT
    movlw   TIMER0_TICKS_TO_LED_TOGGLE
    movwf   LED_D2_wait
    banksel LATA
    movlw   (1<<LATA_LATA4_POSITION)
    xorwf   LATA,F      ; Toggle LED_D3
ISR_TIMER0_EXIT:

    retfie
```

- Variable
```
    PSECT   Isr_Data,global,class=COMMON,space=1,delta=1,noexec
LED_D2_wait:DS      1
```

### Reset vector
```
;
; Reset vector
;
    PSECT   ResetVec,class=CODE,delta=2
    global  ResetVector
ResetVector:
    pagesel Start
    goto    Start
```

## Ref
- [adc+uart+timer0](https://github.com/dsoze1138/MPLABX_pic-as_examples/tree/master/16F18877_DN164136_ADC_UART_v615.X)
