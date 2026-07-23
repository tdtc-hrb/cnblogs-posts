---
title: "MPLAB Code Configurator - Classic"
description: "Based on An3049"
date: 2026-07-23T10:08:08+08:00
---
**Classic support for this device is not maintatined actively and will be deprecated in the future.**

- [MCC Classic](https://www.microchip.com/en-us/tools-resources/configure/mplab-code-configurator/classic)
```
PIC10/PIC12/PIC16/PIC18 library v1.81.7 
```
![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-type.png)

# An3049
1. In the System Module, set the clock to HFINTOSC with 32 MHz frequency.
- mcc.c
```
NOSC HFINTOSC; NDIV 1;
HFFRQ 32_MHz;
CSWHOLD may proceed; SOSCPWR Low power; 
```
![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-clock.png)

2. Set the ADCC module to Basic_mode, with FOSC/ADCLK clock source configured to FOSC/32.

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-adcc.png)

## motor
For providing drive signal to the motor, 
three peripherals are needed to be configured: CCP2, TIMER2 and CWG. 

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-ccp2.png)

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-tmr2.png)

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-cwg.png)

## digital pulses
In counting the digital pulses, 
three peripherals are utilized: CLC1, CCP1 and TIMER1. 

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-clc1.png)

- <s>Enable the CCP interrupt</s>
```
Manually comment out "CCP1_CompareISR(void)".
```
![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-ccp1.png)

### Tmr1
- not enable the TIMER1
- Clock Source: FOSC/4    
usage CLC1OUT:
```
Configure Timer 1 to operate on frequency equal to or lesser than FOSC/4 to use it as Compare source.
```
- period count : 0x7FFF
- not enable Synchronization
#### gate
- enable Gate Toggle mode
- gate signal source : CLC1OUT
- gate polarity : HIGH
- enable Gate
```
enable Timer Gate Interrupt
```
![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-tmr1.png)
## EUSART1
For displaying the data, EUSART1 is configured in Asynchronous mode with Transmit enabled, and a baud rate of 9600. 
- Redirect STDIO to USART.

## MEMORY
The MEMORY is added to store the data. From the Device Resources, find the MEMORY and
double click so that it will be added to the application.
- add DataEE Routiones

### Tmr4
- not enable the TIMER4
- external reset source : CLC1_OUT
- reset at rising TMR4_ers
- clock source : MFINTOSC_31.25 kHz
- Prescaler : 1:128
- Timer Period : 100 ms
- Enable the Timer interrupt

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_classic-tmr4.png)

## pins
pdip40:
- ra0: adcc anx input
- ra2: clcx clcin0 input
- ra4: gpio output
- ra5: gpio output
- ra6: gpio output
- ra7: gpio output
- rb0: cwg1a output
- rb1: gpio input
- rb2: gpio input
- rb4: gpio input
- rc5: gpio input
- rc6: eusart1 tx1 output
- rd1: cwg1b output
- rd2: cwg1c output
- rd3: cwg1d output
- re3: reset
