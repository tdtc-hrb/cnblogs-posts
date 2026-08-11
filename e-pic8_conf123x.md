---
title: "Register Definitions: Configuration Words"
description: "PIC16(L)F18857/77"
date: 2026-08-08T10:08:08+08:00
---
Legend:
- R:   Readable bit
- P:   Programmable bit
- U:   Unimplemented bit, read as '1'
- '0': Bit is cleared
- '1': Bit is set
- n:   Value when blank or after Bulk Erase

### CONFIG1 - OSCILLATORS
|Bit |13 |12 |11 |10 |9 |8|
|-|-|-|-|-|-|-|
|Name|FCMEN|Reserved|CSWEN|Reserved|Reserved|CLKOUTEN|
|Access|R/P|-|R/P|-|-|R/P|
|Reset|1|-|1|-|-|1|

|Bit | 7 | 6 ~ 4 | 3 |2 ~ 0|
|-|-|-|-|-|
|Name|Reserved|RSTOSC<2:0>|Reserved|FEXTOSC<2:0>|
|Access|-|R/P|-|R/P|
|Reset|-|1|-|1|

### CONFIG2 -  SUPERVISORS
|Bit |13 |12 |11 |10 |9 | 8|
|-|-|-|-|-|-|-|
|Name|DEBUG|STVREN|PPS1WAY|ZCDDIS|BORV|Reserved|
|Access|R/P|R/P|R/P|R/P|R/P|-|
|Reset|1|-|1|1|1|1|-|

|Bit | 7 ~ 6 |5| 4 ~ 2 | 1 |0|
|-|-|-|-|-|-|
|Name|BOREN<1:0>|LPBOREN|Reserved|PWRTE|MCLRE|
|Access|R/P|R/P|-|R/P|R/P|
|Reset|1|1|-|1|1|

### CONFIG3 - WINDOWED WATCHDOG
|Bit | 13 ~ 11 |10 ~ 8|
|-|-|-|
|Name|WDTCCS<2:0>|WDTCWS<2:0>|
|Access|R/P|R/P|
|Reset|1|1|

|Bit | 7 | 6 ~ 5 | 4 ~ 0 |
|-|-|-|-|
|Name|Reserved|WDTE<1:0>|WDTCPS<4:0>|
|Access|-|R/P|R/P|
|Reset|-|1|1|

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

### CONFIG4 - MEMORY
|Bit |13 |12 |11 ~ 8|
|-|-|-|-|
|Name|LVP|SCANE|Reserved|
|Access|R/P|R/P|-|
|Reset|1|1|-|

|Bit | 7 ~ 2| 1 ~ 0 |
|-|-|-|
|Name|Reserved|WRT<1:0>|
|Access|-|R/P|
|Reset|-|1|

### CONFIG5 - CODE PROTECTION
|Bit |13 ~ 8|
|-|-|
|Name|Reserved|
|Access|-|
|Reset|-|

|Bit |7 ~ 2 |1 | 0|
|-|-|-|-|
|Name|Reserved|CPD|CP|
|Access|-|R/P|R/P|
|Reset|-|1|1|
