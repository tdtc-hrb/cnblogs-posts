---
title: "microchip code configurator"
description: "Get Started for MPLAB® X IDE Users New to Visual Studio Code (VS Code®)"
date: 2026-07-18T10:08:08+08:00
---
- [MPLAB Extension Pack - VS Code](https://developerhelp.microchip.com/xwiki/bin/view/software-tools/ides/extensions/get-started/)
- XC8: v4.0

### network Settings
![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_network-vs.png)

- choose
```
https://registry.npmjs.org/
https://registry.npmmirror.com
```

## device
Add the following devices from the "Device Resources" window:
- TMR2
- CCP1
- WWDT

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_config-device.png)

### configurations
- Clock Control
- TMR2
- CCP1
- WWDT

#### Clock Control
- Clock Source: LFINTOSC
- Clock Divider: 1

#### Timer2
- Hardware Settings
```
- Enable Timer: checked
- Control Mode: Roll over pulse
- Start/Reset Option: Software control
```
– Timer Clock
```
- Clock Source: FOSC/4
- Prescaler: 1:32
- Postscaler: 1:1
```
– Timer Period
```
- Timer Period: 1.057s
```
- Interrupt Settings
```
- TMR Interrupt Enable: unchecke
```
#### CCP1
– Enable CCP: checked
– CCP Mode: PWM
– Select Timer: Timer2
– Duty Cycle: 25%
– CCPR Alignment: right_aligned
![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_config-ccp1_before.png)


![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_config-ccp1_after.png)
#### WWDT
- WWDT Settings
```
- Watchdog Timer Enable
Off
```

## pin config
- SPDIP28

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_config-pack_type.png)

**The current version of MCC requires you to close and reopen VS Code in order to configure the pin grid view.**

### i/o
– Set Port B pin 4 (RB4) as output for CCP1
– Set Port A pin 4 (RA4) as GPIO input

#### RA4 pin
- enable "Weak Pullup"

click "Pins" Settings

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_config-pins.png)

## MCC Window
- mcc core : v5.9.0

![](https://github.com/tdtc-hrb/cnblogs/raw/main/images/mcc_config-vs.png)

- 1) Device Resources
- 2) Project Resources
- 3) Pin Package View
- 4) Application Builder
- 5) Easy Setup

### enter mcc
View -> Command Palette:
```
MPLAB mcc  : launch
```
