---
title: "advanced 8-bit mcu 的 Register Definitions: Configuration Words"
description: "q10 family"
date: 2026-07-21T10:08:08+08:00
---
Ver: 0.1

### [Pin Count vs. Memory - Q10](https://www.microchip.com/en-us/products/microcontrollers/8-bit-mcus/pic-mcus/pic18-q10)
|Flash (KB)|28 Pins|40 pins}
|-|-|-|
|128|PIC18F27Q10|PIC18F47Q10|
|64|PIC18F26Q10|PIC18F46Q10|
|32|PIC18F25Q10|PIC18F45Q10|
|16|PIC18F24Q10|-|

## 4.6 Register Summary - Configuration Words
|Name| Address |Words|
|-|-|-|
|CONFIG1| 300000h|RSTOSC, FEXTOSC, FCMEN, CSWEN, CLKOUTEN |
|CONFIG2| 300002h|BOREN, LPBOREN, PWRTE, MCLRE, XINST, DEBUG, STVREN, PPS1WAY, ZCD, BORV |
|CONFIG3| 300004h|WDTE, WDTCPS, WDTCCS, WDTCWS |
|CONFIG4| 300006h|WRT3, WRT2, WRT1, WRT0, LVP, SCANE, WRTD, WRTB, WRTC |
|CONFIG5| 300008h|CPD, CP |
|CONFIG6| 30000ah|EBTR3, EBTR2, EBTR1, EBTR0, EBTRB |

## 4.7 Register Definitions: Configuration Words
Legend:
- R:   Readable bit
- W:   Writable bit
- U:   Unimplemented bit, read as '1'
- '0': Bit is cleared
- '1': Bit is set
- n:   Value when blank or after Bulk Erase

### 4.7.1 CONFIG1
- Name:   OSCILLATORS
- Address: 0x300000

|Bit |13 |12 |11 |10 |9 |8|
|-|-|-|-|-|-|-|
|Name|FCMEN|Reserved|CSWEN|Reserved|Reserved|<SPAN style="TEXT-DECORATION: overline">CLKOUTEN</SPAN>|
|Access|R/W|-|R/W|-|-|R/W|
|Reset|1|-|1|-|-|1|

|Bit | 7 | 6 ~ 4 | 3 |2 ~ 0|
|-|-|-|-|-|
|Name|Reserved|RSTOSC<2:0>|Reserved|FEXTOSC<2:0>|
|Access|-|R/W|-|R/W|
|Reset|-|1|-|1|

#### Bit 13 – FCMEN: Fall-Safe Clock Monitor Enable Bit
|Value |Description|
|-|-|
|1 = ON |FSCM timer enabled|
|0 = OFF |FSCM timer disabled|

#### Bit 11 - CSWEN: Clock Switch Enable bit
|Value |Description|
|-|-|
|1 = ON |Writing to NOSO and NDIV is allowed|
|0 = OFF |The NOSC and NDIV bits cannot be changed by user software|

#### Bit 8 – <SPAN style="TEXT-DECORATION: overline">CLKOUTEN</SPAN> Clock Out Enable bit
if FEXTOSC = HS, XT, LP, then this bit is ignored.
Otherwise:

|Value |Description|
|-|-|
|1 = OFF |CLKOUT function is disabled; I/O or oscillator function on OSC2|
|0 = ON |CLKOUT function is enabled; F<sub>OSC</sub>/4 clock appears at OSC2|

#### Bits 6:4 – RSTOSC<2:0> Power-up Default Value for COSC bits
This value is the Reset-default value for COSC, and selects the oscillator first used by user software.

|Value |Description|
|-|-|
|111 = EXT1X| EXTOSC operating per the FEXTOSC bits|
|110 = HFINT1| HFINTOSC with HFFRQ = 4 MHz and CDIV = 4:1 |
|101 = LFINT| LFINTOSC|
|100 = SOSC|SOSC|
|011|Reserved|
|010 = EXT4X|EXTOSC with 4x PLL, with EXTOSC operating per FEXTOSC bits|
|001 |Reserved |
|000| HFINTOSC(64 MHz), with HFFRQ = 64 MHz and CDIV = 1:1. Reset COSC/NOSC to b'110'|

#### Bits 2:0 – FEXTOSC<2:0> FEXTOSC External Oscillator Mode Selection
|Value |Description|
|-|-|
|111 = ECH| EC(External Clock) above 16 MHz|
|110 = ECM| EC(External Clock) for 500 kHz to 16 MHz|
|101 = ECL| EC(External Clock) below 500 kHz)|
|100 = OFF| External Oscillator is disabled. |
|011| Reserved|
|010 = HS| HS(Crystal oscillator) above 4 MHz|
|001 = XT| XT(Crystal oscillator) above 500, below 4 MHz|
|000 = LP| LP(Crystal oscillator) optimized for 32.768 kHz|

### 4.7.2 CONFIG2
- Name:   SUPERVISORS
- Address: 0x300002

|Bit |15|14|13 |12 |11 |10 |9 - 8|
|-|-|-|-|-|-|-|-|
|Name|<SPAN style="TEXT-DECORATION: overline">XINST</SPAN>|Reserved|<SPAN style="TEXT-DECORATION: overline">DEBUG</SPAN>|STVREN|PPS1WAY|ZCD|BORV<1:0>|
|Access|R/W|-|R/W|R/W|R/W|R/W|R/W|
|Reset|1|-|1|1|1|1|1|

|Bit | 7 ~ 6 |5| 4 ~ 2 | 1 |0|
|-|-|-|-|-|-|
|Name|BOREN[1:0]|<SPAN style="TEXT-DECORATION: overline">LPBOREN</SPAN>|Reserved|<SPAN style="TEXT-DECORATION: overline">PWRTE</SPAN>|MCLRE|
|Access|R/W|R/W|-|R/W|R/W|
|Reset|01|1|-|1|1|

#### Bit 15 - <SPAN style="TEXT-DECORATION: overline">XINST</SPAN> Extended Instruction Set Enable bit
|Value |Description|
|-|-|
|1|Extended Instruction Set and Indexed Addressing mode disabled(Legacy mode) |
|0|Extended Instruction Set and Indexed Addressing mode enabled|

#### Bit 13 – <SPAN style="TEXT-DECORATION: overline">DEBUG</SPAN> Debugger Enable<sup>(2)</sup>
|Value |Description|
|-|-|
|1 = OFF| Background debugger disabled|
|0 = ON| Background debugger enabled|

#### Bit 12 – STVREN Stack Overflow/Underflow Reset Enable
|Value| Description|
|-|-|
|1 = ON|Stack Overflow or Underflow will cause a Reset|
|0 = OFF|Stack Overflow or Underflow will not cause a Reset|

#### Bit 11 – PPS1WAY PPSLOCKED One-Way Set Enable
|Value |Description|
|-|-|
|1 = ON| The PPSLOCKED bit can only be set once after an unlocking sequence is executed; <br>once PPSLOCKED is set, all future changes to PPS registers are prevented</br>|
|0 = OFF| The PPSLOCKED bit can be set and cleared as needed (provided an unlocking sequence is executed)|

#### Bit 10 - ZCD Disable bit
|Value |Description|
|-|-|
|1 = ON|ZCD disabled. ZCD can be enabled by setting the ZCDSEN bit of the ZCDCON register|
|0 = OFF|ZCD always enabled, PMDx<ZCDMD> bit is ignored|

#### Bit 9:8 – BORV Brown-out Reset (BOR) Voltage Selection<sup>(1)</sup>
|Value |Description|
|-|-|
|11|Brown-out Reset Voltage (V<sub>BOR</sub>) set to 1.90V|
|10|Brown-out Reset Voltage (V<sub>BOR</sub>) set to 2.45V|
|01|Brown-out Reset Voltage (V<sub>BOR</sub>) set to 2.7V|
|00|Brown-out Reset Voltage (V<sub>BOR</sub>) set to 2.85V|

#### Bits 7:6 – BOREN<1:0> Brown-out Reset (BOR) Enable
When enabled, Brown-out Reset Voltage(V<sub>BOR</sub>) is set by the BORV bit

|Value |Description|
|-|-|
|11 = ON| Brown-out Reset enabled, the SBOREN bit is ignored|
|10 = SLEEP| Brown-out Reset enabled while running, disabled in Sleep; the SBOREN bit is ignored|
|01 = SBOREN| Brown-out Reset enabled according to SBOREN|
|00 = OFF| Brown-out Reset disabled|

#### Bit 5 – <SPAN style="TEXT-DECORATION: overline">LPBOREN</SPAN> Low-Power BOR Enable
|Value |Description|
|-|-|
|1| Low-Power Brown-out Reset is disabled|
|0| Low-Power Brown-out Reset is enabled|

#### Bit 1 – <SPAN style="TEXT-DECORATION: overline">PWRTE<SPAN> Power-Up Timer Enable
|Value| Description|
|-|-|
|1 = OFF| PWRT is disabled|
|0 = ON| PWRT is enabled|

#### Bit 0 – MCLRE Master Clear (<SPAN style="TEXT-DECORATION: overline">MCLR</SPAN>) Enable
|Value |Condition| Description|
|-|-|-|
|x| If LVP = 1 |<SPAN style="TEXT-DECORATION: overline">MCLR</SPAN> pin is <SPAN style="TEXT-DECORATION: overline">MCLR</SPAN>(it will reset the device when driven low)|
|1| If LVP = 0 |<SPAN style="TEXT-DECORATION: overline">MCLR</SPAN> pin is <SPAN style="TEXT-DECORATION: overline">MCLR</SPAN>|
|0| If LVP = 0 |<SPAN style="TEXT-DECORATION: overline">MCLR</SPAN> pin function is port defined function|

Notes:
1. See V<sub>BOR</sub> parameter for specific trip point voltages.   
2. The DEBUG bit is managed automatically by device development tools including debuggers and
programmers. For normal device operation, this bit needs to be maintained as a ‘1’.    

### 4.7.3 CONFIG3
- Name:   WINDOWED WATCHDOG
- Address: 0x300004

|Bit | 13 ~ 11 |10 ~ 8|
|-|-|-|
|Name|WDTCCS[2:0]|WDTCWS[2:0]|
|Access|R/W|R/W|
|Reset|1|1|

|Bit | 7 | 6 ~ 5 | 4 ~ 0 |
|-|-|-|-|
|Name|Reserved|WDTE<1:0>|WDTCPS<4:0>|
|Access|-|R/W|R/W|
|Reset|-|1|1|

#### Bits 13:11 - WDTCCS<2:0>: WDT Input Clock Selector bits
|Value| Description|
|-|-|
|111|Software Control|
|110|Reserved|
|101|Reserved|
|100|Reserved|
|011|Reserved|
|010|Reserved|
|001|WDT reference clock is the 31.25 kHz HFINTOSC(MFINTOSC)output|
|000|WDT reference clock is the 31.0 kHz LFINTOSC(default value)|

#### Bits 10:8 - WDTCWS<2:0>: WDT Window Select bits
n1: WDTCWS    
n2: Value - WDTWS at POR    
n3: Window delay Percent of time - WDTWS at POR    
n4: Window opening Percent of time - WDTWS at POR    
n5: Software control of WDTWS?    
n6: Keyed access required?

![wdt multi-head](https://github.com/tdtc-hrb/cnblogs/raw/main/images/wdt-head6_1.png)

|n1|n2|n3|n4|n5|n6|
|-|-|-|-|-|-|
|111|111|n/a|100|Yes|No|
|110|111|n/a|100|No|Yes|
|101|101|25|75|No|Yes|
|100|100|37.5|62.5|No|Yes|
|011|011|50|50|No|Yes|
|010|010|62.5|37.5|No|Yes|
|001|001|75|25|No|Yes|
|000|000|87.5|12.5|No|Yes|

#### Bits 6:5 - WDTE<1:0> WDT Operating mode
|Value| Description|
|-|-|
|11|WDT enabled regardless of Sleep; the SEN bit in WDTCON0 is ignored|
|10|WDT enabled while Sleep = 0, suspended when Sleep = 1; the SEN bit in WDTCON0 is ignored|
|01|WDT enable/disabled by SEN bit in WDTCON0|
|00|WDT disabled; the SEN bit in WDTCON0 is ignored|

#### Bits 4:0 - WDTCPS<4:0> WDT Period Select bits
n1: WDTCPS
n2: Value - WDTPS at POR    
n3: Divider Ratio - - WDTPS at POR    
n4: Divider Ratio(max) - WDTPS at POR    
n5: Typical Time Out(F<sub>IN</sub> = 31 kHz) - WDTPS at POR    
n6: Software Control of WDTPS?

![wdt multi-head](https://github.com/tdtc-hrb/cnblogs/raw/main/images/wdt-head6_2.png)

|n1|n2|n3|n4|n5|n6|
|-|-|-|-|-|-|
|11111|01011|1:65536|2<sup>16</sup>|2 s|Yes|
|11110<br>...</br><br>10011</br>|11110<br>...</br><br>10011</br>|1:32|2<sup>5</sup>|1 ms|No|
|10010|10010|1:8388608|2<sup>32</sup>|256 s|No|
|10001|10001|1:4194304|2<sup>22</sup>|128 s|No|
|10000|10000|1:2097152|2<sup>21</sup>|64 s|No|
|01111|01111|1:1048576|2<sup>20</sup>|32 s|No|
|01110|01110|1:524299|2<sup>19</sup>|16 s|No|
|01101|01101|1:262144|2<sup>18</sup>|8 s|No|
|01100|01100|1:131072|2<sup>17</sup>|4 s|No|
|01011|01011|1:65536|2<sup>16</sup>|2 s|No|
|01010|01010|1:32768|2<sup>15</sup>|1 s|No|
|01001|01001|1:16384|2<sup>14</sup>|512 ms|No|
|01000|01000|1:8192|2<sup>13</sup>|256 ms|No|
|00111|00111|1:4096|2<sup>12</sup>|128 ms|No|
|00110|00110|1:2048|2<sup>11</sup>|64 ms|No|
|00101|00101|1:1024|2<sup>10</sup>|32 ms|No|
|00100|00100|1:512|2<sup>9</sup>|16 ms|No|
|00011|00011|1:256|2<sup>8</sup>|8 ms|No|
|00010|00010|1:128|2<sup>7</sup>|4 ms|No|
|00001|00001|1:64|2<sup>6</sup>|2 ms|No|
|00000|00000|1:32|2<sup>5</sup>|1 ms|No|

### 4.7.4 CONFIG4
- Name:   MEMORY
- Address: 0x300006

|Bit |13 |12 |11 |10 |9 |8|
|-|-|-|-|-|-|-|
|Name|LVP|SCANE|Reserved|WRTD|WRTB|WRTC|
|Access|R/W|R/W|-|R/W|R/W|R/W|
|Reset|1|1|-|1|1|1|

|Bit | 7 ~ 4| 3 |2| 1 | 0 |
|-|-|-|-|-|-|
|Name|Reserved|WRT3|WRT2|WRT1|WRT0|
|Access|-|R/W|R/W|R/W|R/W|
|Reset|-|1|1|1|1|

#### Bit 13 – LVP Low-Voltage Programming Enable
|Value| Description|
|-|-|
|1| Low-Voltage Programming is enabled. <SPAN style="TEXT-DECORATION: overline">MCLR</SPAN>/V<sub>PP</sub> pin function is <SPAN style="TEXT-DECORATION: overline">MCLR</SPAN>. The MCLRE bit is ignored.|
|0| High voltage (meeting V<sub>IHH</sub> level) on <SPAN style="TEXT-DECORATION: overline">MCLR</SPAN>/V<sub>PP</sub> must be used for programming.|

The LVP bit cannot be written (to zero) while using low-voltage programming. High voltage programming is always availale, regardless of the LVP Configuration bit value.

#### Bit 12 – SCANE Scanner Enable bit
|Value| Description|
|-|-|
|1| Scanner module is available for use, SCANMD bit enables the module.|
|0| Scanner module is NOT available for use, SCANMD bit is ignored.|

#### Bit 10 - WRTD Data EEPROM Write Protection bit
|Value| Description|
|-|-|
|1|Data EEPROM NOT write-protected|
|0|Data EEPROM write-protected|

#### Bit 9 WRTB Boot Block Write Protection bit
|Value| Description|
|-|-|
|1|Boot Block NOT write-protected|
|0|Boot Block write-protected|

#### Bit 8 WRTC Configuration Register Write Protection bit
|Value| Description|
|-|-|
|1|Configuration Registers NOT write-protected|
|0|Configuration Registers write-protected|

#### Bits 0,1,2,3 – WRTn User NVM Self-Write Protection
|Value| Description|
|-|-|
|1| Corresponding Memory Block NOT write-protected|
|0| Corresponding Memory Block write-protected|

### 4.7.5 CONFIG5
- Name:   CODE PROTECTION
- Address: 0x300008

|Bit |15 ~ 13 |12 |11 |10 |9 |8|
|-|-|-|-|-|-|-|
|Name|Reserved|Reserved|Reserved|Reserved|Reserved|Reserved|
|Access|-|-|-|-|-|-|
|Reset|-|-|-|-|-|-|

|Bit |7 |6 |5 | 4 |3 |2 |1 | 0|
|-|-|-|-|-|-|-|-|-|
|Name|Reserved|Reserved|Reserved|Reserved|Reserved|Reserved|<SPAN style="TEXT-DECORATION: overline">CPD</SPAN>|<SPAN style="TEXT-DECORATION: overline">CP</SPAN>|
|Access|-|-|-|-|-|-|R/W|R/W|
|Reset|-|-|-|-|-|-|1|1|

#### bit 1 <SPAN style="TEXT-DECORATION: overline">CPD</SPAN>: Data NVM (EEPROM) Memory Code Protection bit
|Value |Description|
|-|-|
|1| EEPROM code protection disabled
|0| EEPROM code protection enabled

#### Bit 0 – <SPAN style="TEXT-DECORATION: overline">CP</SPAN>  User Program Flash Memory (PFM) Code Protection
|Value |Description|
|-|-|
|1| User PFM code protection is disabled|
|0| User PFM code protection is enabled|

### 4.7.6 CONFIG6
- Name:   Memory Read Protection
- Address: 0x30000a

|Bit |15 ~ 13 |12 |11 |10 |9 |8|
|-|-|-|-|-|-|-|
|Name|Reserved|Reserved|Reserved|Reserved|EBTRB|Reserved|
|Access|-|-|-|-|R/W|-|
|Reset|-|-|-|-|1|-|

|Bit |7 |6 |5 | 4 |3 |2 |1 | 0|
|-|-|-|-|-|-|-|-|-|
|Name|Reserved|Reserved|Reserved|Reserved|EBTR3|EBTR2|EBTR1|EBTR0|
|Access|-|-|-|-|R/W|R/W|R/W|R/W|
|Reset|-|-|-|-|1|1|1|1|

#### Bit 9 - EBTRB Table Read Protection
|Value |Description|
|-|-|
|1|Memory Boot Block not protected from table reads executed in other blocks |
|0|Memory Boot Block protected from table reads executed in other blocks |

#### Bit 0,1,2,3 - EBTRn Table Read Protection
|Value |Description|
|-|-|
|1|Corresponding Memory Block not protected from table reads executed in other blocks |
|0|Corresponding Memory Block protected from table reads executed in other blocks |
