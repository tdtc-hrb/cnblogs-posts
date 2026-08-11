---
title: "freeRTOS移植-sam"
description: "sam4s_ek2"
date: 2020-07-08T08:08:08+08:00
---
> freeRTOS v7.0.0

  移植编译器（IAR）,以及头文件和hook。

# 头文件
> asf.h

```c
// From module: FreeRTOS mini Real-Time Kernel
#include <FreeRTOS.h>
#include <task.h>
```

## freeRTOS config
> FreeRTOSConfig.h
```c
#ifndef FREERTOS_CONFIG_H
#define FREERTOS_CONFIG_H

//#define configUSE_PREEMPTION                     1
#define configUSE_PREEMPTION                       0
#define configUSE_IDLE_HOOK                        1
#define configUSE_TICK_HOOK                        1
#define configCPU_CLOCK_HZ                         (sysclk_get_cpu_hz())
#define configTICK_RATE_HZ                         ((portTickType) 1000)
#define configMINIMAL_STACK_SIZE                   ((unsigned short) 70)
//#define configTOTAL_HEAP_SIZE                    ((size_t) ( 0x2C00-600 ))
#define configMAX_TASK_NAME_LEN                    ( 16 )
#define configUSE_TRACE_FACILITY                   1
#define configUSE_16_BIT_TICKS                     0
#define configIDLE_SHOULD_YIELD                    0
#define configUSE_CO_ROUTINES                      0
#define configUSE_MUTEXES                          1
#define configUSE_RECURSIVE_MUTEXES                0
#define configCHECK_FOR_STACK_OVERFLOW             2
#define configGENERATE_RUN_TIME_STATS              0
#define configENABLE_BACKWARD_COMPATIBILITY        1

#define configMAX_PRIORITIES                       ((unsigned portBASE_TYPE) 5)
#define configMAX_CO_ROUTINE_PRIORITIES            (2)
#define configQUEUE_REGISTRY_SIZE                  10

#define INCLUDE_vTaskPrioritySet                   1
#define INCLUDE_uxTaskPriorityGet                  0
#define INCLUDE_vTaskDelete                        0
#define INCLUDE_vTaskCleanUpResources              0
#define INCLUDE_vTaskSuspend                       1
#define INCLUDE_vTaskDelayUntil                    0
#define INCLUDE_vTaskDelay                         1
#define INCLUDE_uxTaskGetStackHighWaterMark        1

#define configKERNEL_INTERRUPT_PRIORITY            (0x0f << 4)
#define configMAX_SYSCALL_INTERRUPT_PRIORITY       (0x05 << 4)
#endif
```

# hook
> 必须实现3个callback函数

## stack overflow
```c
extern void vApplicationStackOverflowHook(xTaskHandle *pxTask,
		signed char *pcTaskName)
{
	printf("stack overflow %x %s\r\n", pxTask, (portCHAR*)pcTaskName);
	for (;;) {
	}
}
```

## idle
```c
extern void vApplicationIdleHook(void)
{
}
```

## tick
```c
extern void vApplicationTickHook(void)
{
}
```

# IDE
## linker
```bash
--config_def __ICFEDIT_size_heap__=0x1000
```
