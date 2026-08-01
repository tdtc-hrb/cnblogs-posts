---
title: "PIC blinked - m2"
description: "pic16f18877"
date: 2026-07-14T08:08:08+08:00
---

blink188m2.asm:
```
    title "Blink V. 1.1"
    subtitle "The uC Equivalent of Hello world"
; This program uses Assembly language to blink a small LED, using the PIC16F18877's onboard timers
; and interrupts.  Use and modify this code however you wish

; ***** Program Header *******************************************************************************
    list P=PIC16F18877, ST=OFF, MM=OFF, N=42, B=4, W=1 ; <-- List file directives
    include <p16f18877.inc> ; <--Use the name of your PIC here

; ***** Device Configuration *************************************************************************
    __CONFIG _CONFIG1, _FEXTOSC_OFF & _RSTOSC_HFINT32 & _CLKOUTEN_OFF & _CSWEN_ON & _FCMEN_ON
    __CONFIG _CONFIG2, _MCLRE_ON & _PWRTE_OFF & _LPBOREN_OFF & _BOREN_OFF & _BORV_LO & _ZCD_OFF & _PPS1WAY_OFF & _STVREN_ON
    __CONFIG _CONFIG3, _WDTCPS_WDTCPS_31 & _WDTE_OFF & _WDTCWS_WDTCWS_7 & _WDTCCS_SC
    __CONFIG _CONFIG4, _WRT_OFF & _SCANE_available & _LVP_ON
    __CONFIG _CONFIG5, _CP_OFF & _CPD_OFF

; ******* Main Program ********************************************************************************
; Org 0 tells the compiler that this is literally the origin of the actual program, IE line 0
; From hereon out, the compiler assumes that each instruction is to be executed sequentially.
    org 0

Start:
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

    banksel OSCCON1
    movlw   0x62        ; System oscillator set to 8MHz
    movwf   OSCCON1

    banksel T0CON1
    movlw   0x48        ; Set TIMER0 clock source to FOSC/4, scale 1:256 TIMER0 overflows every 32.768 milliseconds
    movwf   T0CON1
    banksel T0CON0
    movlw   0x80        ; Start TIMER0
    movwf   T0CON0

    banksel ANSELB
    clrf    ANSELB
    banksel TRISB
    movlw   0xF0
    movwf   TRISB
    banksel LATB
    movwf   LATB

    banksel TMR0L
    clrf    TMR0L
    movlw   0xFF
    movwf   TMR0H
    banksel PIR0
    bcf     PIR0, TMR0IF

    clrf    FSR1L
    movlw   10
    subwf   FSR1L,F
    goto    AppLoop
;
; Application loop
;
AppLoop:
    banksel LATB
    movlw   1
    xorwf   LATB,F      ; Toggle LED1
    call    Delay
    goto    AppLoop     ;loop forever
;
; Spin wait
;
Delay:
    clrf    FSR0L
    clrf    FSR0H
    bsf     FSR0H,7
Delay_1:
    call    Delay_2
    incfsz  FSR0L,F
    goto    Delay_1
    incfsz  FSR0H,F
    goto    Delay_1
    return
;
; Toggle LED2 when TIMER0 overflows
;
; Note: This delay always takes the same number
;       of instruction cycles for each of
;       three possible paths.
;
Delay_2:
    banksel PIR0
    btfss   PIR0, TMR0IF
    goto    Delay_3
    bcf     PIR0, TMR0IF
    banksel FSR1L
    movlw   10
    incfsz  FSR1L,F
    goto    Delay_4
    subwf   FSR1L,F
    movlw   2
    xorwf   LATB,F      ; Toggle LED2
    return
Delay_3:
    nop
    nop
    nop
    nop
    nop
Delay_4:
    nop
    nop
    return

    end
```
