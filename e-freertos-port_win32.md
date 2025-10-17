---
title: "freeRTOS移植"
description: "win32"
date: 2020-07-06T08:08:08+08:00
---
> freeRTOS v6.1.1

# portable.h
## memory
### TCB
  Ln167~Ln187(port.c)
```c
pxPortInitialiseStack
```

### memory management
  在heap_n.c中实现
```c
void *pvPortMalloc( size_t xSize ) PRIVILEGED_FUNCTION;
void vPortFree( void *pv ) PRIVILEGED_FUNCTION;
```
  如果MPU可用，则使用Ln105~106(mpu_wrappers.h)		
```c
void vPortInitialiseBlocks( void ) PRIVILEGED_FUNCTION;
size_t xPortGetFreeHeapSize( void ) PRIVILEGED_FUNCTION;
```

## task
  此实现在Ln189~Ln262、Ln401~Ln406(port.c)
```c
portBASE_TYPE xPortStartScheduler( void ) PRIVILEGED_FUNCTION;
void vPortEndScheduler( void ) PRIVILEGED_FUNCTION;
```
> 注意：在windows系统下end是调用了TerminateProcess（WinBase.h Ln3398）

# portmacro.h

## Critical
  此实现在Ln452~Ln513(port.c)
```c
void vPortEnterCritical( void );
void vPortExitCritical( void );

#define portENTER_CRITICAL()		vPortEnterCritical()
#define portEXIT_CRITICAL()			vPortExitCritical()
```

## Interrupt
```c
#define portDISABLE_INTERRUPTS()
#define portENABLE_INTERRUPTS()
```
  此实现在Ln408~Ln450(port.c)
```c
void vPortGenerateSimulatedInterrupt( unsigned long ulInterruptNumber );
void vPortSetInterruptHandler( unsigned long ulInterruptNumber, unsigned long (*pvHandler)( void ) );

#define portYIELD()					vPortGenerateSimulatedInterrupt( portINTERRUPT_YIELD )
```

# other

## thread
  在portmacro.h定义，在Ln377~Ln399(port.c)实现
```c
void vPortDeleteThread( void *pvThreadToDelete );
```

## Simulated
  在port.c中定义并针对具体平台（比如windows）实现

  总开关：
```c
static void prvProcessSimulatedInterrupts( void );
```
  具体操作：
```c
static unsigned long prvProcessDeleteThreadInterrupt( void );
static unsigned long prvProcessYieldInterrupt( void );
static unsigned long prvProcessTickInterrupt( void );
```
  模拟tick
```c
static DWORD WINAPI prvSimulatedPeripheralTimer( LPVOID lpParameter );
```
