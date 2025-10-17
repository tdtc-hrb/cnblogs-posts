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

- Byte-Oriented File Register Operations    
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|CLRF   f   |f=0 <br>Clears the content of File Register f.</br>|Z|1|
|CLRW       |W=0 <br>Clears W.</br>|Z|1|
|SWAPF  f, d|Swaps the nibbles of f and write the result to destination d.|None|1|
|ADDWFC f, d|Add WREG and Carry bit to f. <sup>1<sup>|C, DC, Z|2|
|ASRF   f, d|Arithmetic Right Shift. <sup>1<sup>|C, Z|2|
|LSLF   f, d|Logical Left Shift. <sup>1<sup>|C, Z|2|
|LSRF   f, d|Logical Right Shift. <sup>1<sup>|C, Z|2|
|SUBWFB f, d|Subtract WREG from f with Borrow. <sup>1<sup>|C, DC, Z|2|

- Bit-Oriented File Register Operations    
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|BCF    f, b|Clears (equates to 0) the bit b of register f.|None|1|
|BSF    f, b|Sets (equates to 1) the bit b of register f.|None|1|
|BTFSC  f, b|Checks if bit b of register f is equal to 0 or not. If it is NOT zero continue with the next line of code, otherwise skips next line.|None|1 (if 1)<br>2 (if 0)</br>|
|BTFSS  f, b|Checks if bit b of register f is equal to 1 or not. If it is ZERO continue with the next line of code, otherwise skips next line.|None|1 (if 0)<br>2 (if 1)</br>|

- Literal Operations    
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|MOVLB  k   |Move literal to BSR. <sup>1<sup>|None|1|
|MOVLP  k   |Move literal to PCLATH. <sup>1<sup>|None|1|

- Control Operations    
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|BRA    k   |Relative Branch. <sup>1<sup>|None|2|
|BRW    —   |Relative Branch with WREG. <sup>1<sup>|None|2|
|CALL  LABEL|Calls subroutine LABEL.|None|2|
|CALLW  —   |Call Subroutine with WREG. <sup>1<sup>|None|2|
|GOTO  LABEL|Jumps to the code under LABEL.|None|2|
|RETFIE     |Return command for interrupt subroutines.|None|2|
|RETLW  k   |Return from a subroutine with W=N.|None|2|
|RETURN     |Return from a subroutine.|None|2|

- Inherent Operations    
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|CLRWDT     |Clears Watchdog Timer.|<SPAN style="TEXT-DECORATION: overline">TO</SPAN>, <SPAN style="TEXT-DECORATION: overline">PD</SPAN>|1|
|NOP        |No Operation <br>Does Nothing.</br>|None|1|
|RESET  —   |Software device Reset. <sup>1<sup>|None|1|
|SLEEP      |Go into Standby Mode. |<SPAN style="TEXT-DECORATION: overline">TO</SPAN>, <SPAN style="TEXT-DECORATION: overline">PD</SPAN>|1|
|TRIS   f   |Load TRIS register with WREG. <sup>1<sup>|None|1|

*1: New MCU (PIC16F188/152 etc.) series add 12（47 in total） assembly instructions.

## sing data memory and moving data
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|MOVF   f, d|f⇒W (if d=0), f⇒f (if d=1) <br>Moves the content of f to destination d.</br>|Z|1|
|MOVWF  f   |W⇒f <br>Moves W to file register f.</br>|None|1|
|MOVLW  k   |k⇒W <br>Copies the number k to W.</br>|None|1|

### MOVF
In general, f is assigned to W.
```
MOVF    f, 0
```

The following are special cases:    
- Test
```
MOVF    f, 1
```
 does not affect the content of f or W.    
MOVF with a destination 1 (F), is generally used to check whether the content of the file register is zero or not.

Equivalent Operation(s):
```
TSTF    f          ;Test File
```

- Zero
```
movf    buffer,F   ;Force Z bit
```
强制置零

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

### [SUBWF](http://www.onlinepiccompiler.com/InstructionsENG.html#section=SUBWF)
![d=0](http://www.onlinepiccompiler.com/img/subwfENG.png)
![d=1](http://www.onlinepiccompiler.com/img/subwfENG.png)

#### AFFECTED FLAGS
SUBWF affects the flags Z, DC and C.    
Note: The destination D (=0, W, 1 or F) is not effective on the flags.
- Z    
Zero Flag (Z) is set if the result of the subtraction is equal to zero.    
In the example below, because the result is 48 (different from 0) Z=0 at the end of SUBWF instruction.

Example:    
Let's say W=15 and COUNTER=63, then
```
SUBWF COUNTER, F
```
makes COUNTER=48. W remains the same. If the destination was W (or 0), W becomes 48 and COUNTER remains the same.

### logic operation
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|ANDWF  f, d|f&W⇒W (if d=0), f&W⇒f (if d=1) <br>Logically AND W with the content of file register f and write the result to destination d.</br>|Z|1|
|ANDLW  k    |k&W⇒W <br>Logically ANDs literal k with W.</br>|Z|1|
|IORWF  f, d|f &#124; W⇒W (if d=0), f &#124; W⇒f (if d=1) <br>Logically ORs f with W and write the result to destination d.</br>|Z|1|
|IORLW  k    |k &#124; W⇒W <br>Logically ORs literal k with W.</br>|Z|1|
|XORWF  f, d|f^W⇒W (if d=0), f^W⇒f (if d=1) <br>Logically XORs f with W and write the result to destination d.</br>|Z|1|
|XORLW  k    |k^W⇒W <br>Logically XORs literal k with W.</br>|Z|1|

## Code bit manipulation
|Instruction|Description|Affected Flags|Instruction Cycles|
|-|-|-|-|
|COMF   f, d|<SPAN style="TEXT-DECORATION: overline">f</SPAN>⇒W (if d=0), <SPAN style="TEXT-DECORATION: overline">f</SPAN>⇒f (if d=1) <br>Takes the complement of each bit in f and write the result to destination d.</br>|Z|1|
|RLF    f, d|f<1⇒W (if d=0), f<1⇒f (if d=1) <br>Rotates f one bit to the left through Carry Bit and write the result to destination d.</br>|C|1|
|RRF    f, d|f>1⇒W (if d=0), f>1⇒f (if d=1) <br>Rotates f one bit to the right through Carry Bit and write the result to destination d.</br>|C|1|


# Ref
- [Summary of Instructions](http://www.onlinepiccompiler.com/InstructionsENG.html#section=div0)
- [Lecture 29 - Continue with PIC example programs: Interrupt](http://player.slideplayer.com/download/78/12900510/cNyMX-VU_CZjdfqY7w9taQ/1684229591/12900510.ppt)
- ASCII code
```
&#124; - |
&#96;  - `
```
![Dot printer ASCII](https://upload.wikimedia.org/wikipedia/commons/0/0f/Dot_printer_ASCII.png)
