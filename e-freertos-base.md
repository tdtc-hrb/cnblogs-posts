---
title: "freeRTOS基础"
description: "约定、基本文件、最小系统、基本task"
date: 2020-07-04T08:08:08+08:00
---
> freeRTOS v6.1.1

# 约定
## 变量名
  u: unsigned
  c: char
  s: int16_t(short)
  l: int32_t(long)  
  x: BaseType_t 和其他所有

## 函数名
> 前面的小写字母表示返回值；
> 紧随其后的是文件名。

  p: pointer
  v: void
  x: xQueueReceive() returns a variable of type BaseType_t and is defined within queue.c.


# 必须使用
- task.c
- list.c
- queue.c    
  task-to-task, task-to-interrupt, interrupt-to-task.

## 移植必须
> 实现 portable.h

- heap_1.c or heap_2.c or heap_3.c
- port.c

### header
- portmacro.h

# 最小化系统
main.c
```c
#include <FreeRTOS.h>

int main(void)
{
	vTaskStartScheduler();
	return 0;
}
```
## header
> vc    
  Configuration Properties -> c/c++ -> Additional include Directories

```bash
"$(ProjectDir)";
"$(ProjectDir)\..\..\Source\include";
"$(ProjectDir)\..\..\Source\portable\MSVC-MingW"
```

FreeRTOSConfig.h
```c
#define configUSE_PREEMPTION			1
#define configUSE_IDLE_HOOK				0
#define configUSE_TICK_HOOK				0
#define configUSE_CO_ROUTINES 			0
#define configUSE_16_BIT_TICKS			0

#define configMAX_PRIORITIES			( ( unsigned portBASE_TYPE ) 7 )
#define configMINIMAL_STACK_SIZE		( ( unsigned portSHORT ) 50 )
#define configTICK_RATE_HZ				( 50 )

/* Set the following definitions to 1 to include the API function, or zero
to exclude the API function. */
#define INCLUDE_vTaskPrioritySet			1
#define INCLUDE_uxTaskPriorityGet			1
#define INCLUDE_vTaskDelete					1
#define INCLUDE_vTaskCleanUpResources		0
#define INCLUDE_vTaskSuspend				1
#define INCLUDE_vTaskDelayUntil				1
#define INCLUDE_vTaskDelay					1

#define INCLUDE_xTaskGetSchedulerState		1
```

# add task

## task function
```c
void vTask1 (void *pvParameter)
{
	const char* pcTaskName = "Task 1\r\n";
	volatile unsigned int ul;
	for(;;) {
		printf( pcTaskName );
		vTaskDelay( 100 );
	}

}
```

## call them
> main.c

```c
int main(void)
{
  xTaskCreate( vTask1, /* Pointer to the function that implements the task. */
  "Task 1",/* Text name for the task. This is to facilitate debugging only. */
  1000, /* Stack depth - small microcontrollers will use much less stack than this. */
  NULL, /* This example does not use the task parameter. */
  1, /* This task will run at priority 1. */
  NULL ); /* This example does not use the task handle. */

	vTaskStartScheduler();

	return 0;
}
```
