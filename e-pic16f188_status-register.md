---
title: "PIC的 STATUS Register"
description: "PIC16(L)F18857/77"
date: 2023-05-18T12:08:08+08:00
---
Ver 0.1

# STATUS Register
|name|Bit|access|reset|
|-|-|-|-|
|<SPAN style="TEXT-DECORATION: overline">TO</SPAN>|4|R|1|
|<SPAN style="TEXT-DECORATION: overline">PD</SPAN>|3|R|1|
|Z|2|R/W|0|
|DC|1|R/W|0|
|C|0|R/W|0|

## Time-Out
Watchdog overflow.    
Bit is set after turning on the supply and execution of CLRWDT and SLEEP instructions.    
Bit is reset when watchdog gets to the end signaling that overflow took place.

1 = overflow did not occur    
0 = overflow did occur
### datasheet
Reset States: POR/BOR = 1    
              All Other Reset = q

|Value|Description|
|-|-|
|1|Set at power-up or by execution of CLRWDT or SLEEP instruction|
|0|A WDT time-out occurred|

## Power-Down
This bit is set whenever power supply is brought to a microcontroller : as it starts running, after each regular reset and after execution of instruction CLRWDT.    
Instruction SLEEP resets it when microcontroller falls into low consumption mode.    
Its repeated setting is possible via reset or by turning the supply off/on .    
Setting can be triggered also by a signal on RB0/INT pin, change on RB port, upon writing to internal DATA EEPROM, and by a Watchdog.

1 = after supply has been turned on    
0 = executing SLEEP instruction
### datasheet
Reset States: POR/BOR = 1    
              All Other Resets = q

|Value|Description|
|-|-|
|1|Set at power-up or by execution of CLRWDT instruction|
|0|Cleared by execution of the SLEEP instruction|

## Zero
Indication of a zero result

This bit is set when the result of an executed arithmetic or  logic operation is zero.

1 = result equals zero    
0 = result does not equal zero
### datasheet
Reset States: POR/BOR = 0    
              All Other Resets = u

|Value|Description|
|-|-|
|1|The result of an arithmetic or logic operation is zero|
|0|The result of an arithmetic or logic operation is not zero|

## Digit Carry/<sup>Borrow</sup>
DC Transfer

Bit affected by operations of addition, subtraction. Unlike C bit, this bit represents transfer from the fourth resulting place.    
It is set in case of subtracting smaller from greater number and is reset in the other case.

1 = transfer occurred on the fourth bit according to the order of the result    
0 = transfer did not occur

DC bit is affected by ADDWF, ADDLW, SUBLW, SUBWF instructions.
### datasheet
ADDWF, ADDLW, SUBLW, SUBWF instructions    
Reset States: POR/BOR = 0    
              All Other Resets = u    

|Value|Description|
|-|-|
|1|A carry-out from the 4th low-order bit of the result occurred|
|0|No carry-out from the 4th low-order bit of the result|

## Carry/<sup>Borrow</sup>
Bit that is affected by operations of addition, subtraction and shifting.

1 = transfer occurred from the highest resulting bit    
0 = transfer did not occur

C bit is affected by ADDWF, ADDLW, SUBLW, SUBWF instructions.
### datasheet
ADDWF, ADDLW, SUBLW, SUBWF instructions    
Reset States: POR/BOR = 0    
              All Other Resets = u    

|Value|Description|
|-|-|
|1|A carry-out from the Most Significant bit of the result occurred|
|0|No carry-out from the Most Significant bit of the result occurred|


# Ref
- [STATUS Register](http://www.matidavid.com/pic/picbook_site/2_04chapter.htm)
