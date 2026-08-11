---
title: "PIC blinked - init"
description: "osc, timer0, led"
date: 2026-08-10T08:08:08+08:00
---
- Osciliator
- timer0
- led

### Osciliator
```
    banksel OSCCON1
    movlw   0x63        ; System oscillator set to 4MHz
    movwf   OSCCON1
```

### Start timer0
```
    banksel T0CON1
    movlw   0x48        ; Set TIMER0 clock source to FOSC/4, scale 1:256 TIMER0 overflows every 32.768 milliseconds
    movwf   T0CON1
    banksel T0CON0
    movlw   0x80        ; Start TIMER0
    movwf   T0CON0
```

### Seup TIMER0
```
;
; Seup TIMER0
;
    banksel TMR0L
    clrf    TMR0L
    movlw   0xFF
    movwf   TMR0H
    banksel PIR0
    bcf     PIR0,PIR0_TMR0IF_POSITION
    bsf     PIE0,PIE0_TMR0IE_POSITION
    movlw   TIMER0_TICKS_TO_LED_TOGGLE
    movwf   LED_D2_wait
```
- Constant
```
#define TIMER0_TICKS_TO_LED_TOGGLE 10
```
- Variable
```
    PSECT   Isr_Data,global,class=COMMON,space=1,delta=1,noexec
LED_D2_wait:DS      1
```

### led
```
    banksel TRISA
    bcf     TRISA,TRISA_TRISA4_POSITION
    bcf     TRISA,TRISA_TRISA5_POSITION
    banksel LATA
    bcf     LATA,LATA_LATA4_POSITION
    bcf     LATA,LATA_LATA5_POSITION
```

## Ref
- [adc+uart+timer0](https://github.com/dsoze1138/MPLABX_pic-as_examples/tree/master/16F18877_DN164136_ADC_UART_v615.X)
