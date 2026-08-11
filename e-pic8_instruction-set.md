---
title: "8位单片机PIC汇编语言"
description: "Instruction Set"
date: 2023-05-17T10:08:08+08:00
---
TABLE 1-1: OPCODE FIELD DESCRIPTIONS

|Field| Description|
|-|-|
| f | Register file address (0x00 to 0x7F) |
| W | Working register (accumulator) |
| d | Destination select; |
| b | Bit address within an 8-bit file register |
| k | Literal field, constant data or label |

|d value|where the results are placed|
|-|-|
| d = 0 | W(orking) register |
| d = 1 | file register; Default is d = 1.|

TABLE 1-2: ABBREVIATION DESCRIPTIONS

|Field| Description|
| PC | Program Counter|
| TO | Time-Out bit|
| C  | Carry bit|
| DC | Digit Carry bit|
| Z  | Zero bit|
| PD | Power-Down bit|

## Byte Manipulation for I/O Control
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|CLRF   f   |f=0 <br>Clears the content of File Register f.</br>|Z|1|

### example
```
; Subroutine to initialize PORTA
Init_PORTA:
BANKSEL TRISA
CLRF TRISA ; Set PORTA as output
RETURN;
```

## Bit Manipulation for I/O Control
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|BCF    f, b|Clears (equates to 0) the bit b of register f.|None|1|
|BSF    f, b|Sets (equates to 1) the bit b of register f.|None|1|
|BTFSC  f, b|Checks if bit b of register f is equal to 0 or not. If it is NOT zero continue with the next line of code, otherwise skips next line.|None|1 (if 1)<br>2 (if 0)</br>|
|BTFSS  f, b|Checks if bit b of register f is equal to 1 or not. If it is ZERO continue with the next line of code, otherwise skips next line.|None|1 (if 0)<br>2 (if 1)</br>|

### example
Use bit-oriented instructions for efficient I/O control:
```
BSF LATA, 2 ; Set RA2 high (turn on LED)
BCF LATA, 2 ; Clear RA2 low (turn off LED)
BTFSC PORTA, 1 ; Check if RA1 is low (button pressed)
GOTO BUTTON_PRESSED
```

## Data Movement
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|MOVF   f, d|f⇒W (if d=0), f⇒f (if d=1) <br>Moves the content of f to destination d.</br>|Z|1|
|MOVWF  f   |W⇒f <br>Moves W to file register f.</br>|None|1|
|MOVLW  k   |k⇒W <br>Copies the number k to W.</br>|None|1|

### Bank Selection
```
BANKSEL TRISA ; Select bank containing TRISA
MOVLW 0xFF
MOVWF TRISA ; Set PORTA as all inputs
```

## Program Control
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|CALL  LABEL|Calls subroutine LABEL.|None|2|
|GOTO  LABEL|Jumps to the code under LABEL.|None|2|
|RETFIE     |Return command for interrupt subroutines.|None|2|
|RETURN     |Return from a subroutine.|None|2|

### Interrupt Handling
Interrupts allow the microcontroller to respond to external events promptly. 
Proper handling of interrupts is crucial for real-time applications.
```
ORG 0x0004 ; Interrupt vector location
GOTO ISR ; Jump to ISRISR:
ISR:     ; Handle interrupt
BCF INTCON, INTF ; Clear interrupt flag
; Perform interrupt-specific tasks
RETFIE ; Return from interrupt
```

## Math
- Skip if Zero

|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|INCFSZ f, d|f+1⇒W (if d=0), f+1⇒f (if d=1) <br>Increments f and write the result to destination d. The next instruction is skipped if the result is 0 (f=255).</br>|None|1 (f≠255) <br>2 (f=255)</br>|
|DECFSZ f, d|f-1⇒W (if d=0), f-1⇒f (if d=1) <br>Decrements f and write the result to destination d. The next instruction is skipped if the result is 0.</br>|None|1 (f≠1) <br>2 (f=1)</br>|

- Add & Subtracts

|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|ADDWF  f, d|f+W⇒W (if d=0), f+W⇒f (if d=1) <br>Add W with the content of file register f and write the result to destination d.</br>|Z, DC, C|1|
|ADDLW  k   |k+W⇒W <br>Add literal k and W.</br>|Z, DC, C|1|
|INCF   f, d|f+1⇒W (if d=0), f+1⇒f (if d=1) <br>Increments f and write the result to destination d.</br>|Z|1|
|SUBWF  f, d|f-W⇒W (if d=0), f-W⇒f (if d=1) <br>Subtracts W from f and write the result to destination d.</br>|C, DC, Z|1|
|SUBLW  k   |k-W⇒W <br>Subtract W from literal k and write the result to W.</br>|Z, DC, C|1|
|DECF   f, d|f-1⇒W (if d=0), f-1⇒f (if d=1) <br>Decrements f and write the result to destination d.</br>|Z|1|

### logic operation
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|ANDWF  f, d|f&W⇒W (if d=0), f&W⇒f (if d=1) <br>Logically AND W with the content of file register f and write the result to destination d.</br>|Z|1|
|ANDLW  k    |k&W⇒W <br>Logically ANDs literal k with W.</br>|Z|1|
|IORWF  f, d|f &#124; W⇒W (if d=0), f &#124; W⇒f (if d=1) <br>Logically ORs f with W and write the result to destination d.</br>|Z|1|
|IORLW  k    |k &#124; W⇒W <br>Logically ORs literal k with W.</br>|Z|1|
|XORWF  f, d|f^W⇒W (if d=0), f^W⇒f (if d=1) <br>Logically XORs f with W and write the result to destination d.</br>|Z|1|
|XORLW  k    |k^W⇒W <br>Logically XORs literal k with W.</br>|Z|1|

# Ref
- [Summary of Instructions](https://web.archive.org/web/20220523013104/http://www.onlinepiccompiler.com:80/InstructionsENG.html)
- [Introduction to PIC16F18875 Instruction Set](https://openwa.pressbooks.pub/nehakardam10/chapter/part-1-introduction-to-pic16f18875-instruction-set/)
- [PIC16F18875 Instruction Set - 2](https://openwa.pressbooks.pub/nehakardam10/chapter/part-2-detailed-pic16f18875-instruction-set-and-programming-techniques/)
- [PIC16F18875 Instruction Set - 3](https://openwa.pressbooks.pub/nehakardam10/chapter/155/)
