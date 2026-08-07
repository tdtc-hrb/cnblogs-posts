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
|Name|FCMEN|Reserved|CSWEN|Reserved|Reserved|CLKOUTEN|
|Access|R/W|-|R/W|-|-|R/W|
|Reset|1|-|1|-|-|1|

|Bit | 7 | 6 ~ 4 | 3 |2 ~ 0|
|-|-|-|-|-|
|Name|Reserved|RSTOSC<2:0>|Reserved|FEXTOSC<2:0>|
|Access|-|R/W|-|R/W|
|Reset|-|1|-|1|

- FCMEN: Fall-Safe Clock Monitor Enable Bit
- CSWEN: Clock Switch Enable bit
- <SPAN style="TEXT-DECORATION: overline">CLKOUTEN</SPAN>: Clock Out Enable bit
- RSTOSC: Power-up Default Value for COSC bits
- FEXTOSC: FEXTOSC External Oscillator Mode Selection


### 4.7.2 CONFIG2
|Bit |15|14|13 |12 |11 |10 |9 - 8|
|-|-|-|-|-|-|-|-|
|Name|XINST</SPAN>|Reserved|DEBUG|STVREN|PPS1WAY|ZCD|BORV<1:0>|
|Access|R/W|-|R/W|R/W|R/W|R/W|R/W|
|Reset|1|-|1|1|1|1|1|

|Bit | 7 ~ 6 |5| 4 ~ 2 | 1 |0|
|-|-|-|-|-|-|
|Name|BOREN[1:0]|LPBOREN|Reserved|PWRTE|MCLRE|
|Access|R/W|R/W|-|R/W|R/W|
|Reset|01|1|-|1|1|

- <SPAN style="TEXT-DECORATION: overline">XINST</SPAN>: Extended Instruction Set Enable bit
- <SPAN style="TEXT-DECORATION: overline">DEBUG</SPAN>: Debugger Enable<sup>(2)</sup>
- STVREN: Stack Overflow/Underflow Reset Enable
- PPS1WAY: PPSLOCKED One-Way Set Enable
- ZCD: Disable bit
- BORV: Brown-out Reset (BOR) Voltage Selection<sup>(1)</sup>
- BOREN: Brown-out Reset (BOR) Enable
- <SPAN style="TEXT-DECORATION: overline">LPBOREN</SPAN>: Low-Power BOR Enable
- <SPAN style="TEXT-DECORATION: overline">PWRTE<SPAN>: Power-Up Timer Enable
- MCLRE: Master Clear (<SPAN style="TEXT-DECORATION: overline">MCLR</SPAN>) Enable

### 4.7.3 CONFIG3
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

- WDTCCS: WDT Input Clock Selector bits
- WDTCWS: WDT Window Select bits

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

- WDTE: WDT Operating mode
- WDTCPS: WDT Period Select bits    
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

- LVP: Low-Voltage Programming Enable
- SCANE: Scanner Enable bit
- WRTD: Data EEPROM Write Protection bit
- WRTB: Boot Block Write Protection bit
- WRTC: Configuration Register Write Protection bit
- WRTn: User NVM Self-Write Protection

### 4.7.5 CONFIG5
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

- <SPAN style="TEXT-DECORATION: overline">CPD</SPAN>: Data NVM (EEPROM) Memory Code Protection bit
- <SPAN style="TEXT-DECORATION: overline">CP</SPAN>: User Program Flash Memory (PFM) Code Protection


### 4.7.6 CONFIG6
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

- EBTRB: Table Read Protection
- EBTRn: Table Read Protection
