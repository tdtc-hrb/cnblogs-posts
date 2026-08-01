---
title: "Unavailable components and their substitutes"
description: "include the full bridge and MCU"
date: 2026-07-30T10:08:08+08:00
---

## H-bridge
![](https://modularcircuits.com/blog/wp-content/uploads/2011/10/image_thumb7.png)

- The switching elements (Q1..Q4) are usually bi-polar or FET transistors, in some high-voltage applications IGBTs. 
Integrated solutions also exist but whether the switching elements are integrated with their control circuits 
or not is not relevant for the most part for this discussion. 
- The diodes (D1..D4) are called catch diodes and are usually of a Schottky type.

### substitutes
- [hip4080a](https://www.renesas.cn/zh/document/dst/hip4080-datasheet)
- [hip4081a](https://www.renesas.com/en/document/dst/hip4081a-datasheet)
- [L298](https://www.st.com/en/motor-drivers/l298.html)

The biggest difference between the HIP4080A and the HIP4081A is that the HIP4081A allows separate and individual control of the 4 MOSFET gates, 
whereas the HIP4080A does not.

## Pic16F177x
- pin 28
```
pic16f1778
```
- pin 40
```
pic16f1777
pic16f1779
```

There is no alternative to the PIC16F177x; a redesign is required.

## Ref
- [H-Bridges – the Basics](https://www.modularcircuits.com/blog/articles/h-bridge-secrets/h-bridges-the-basics)
