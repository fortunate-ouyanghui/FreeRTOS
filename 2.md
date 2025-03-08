# 任务创建和删除（静态方法）
## 实验目的：
设计四个任务：start_task,task1,task2,task3
四个任务的功能如下：
- start_task：用来创建其他三个任务
- task1：实现LED0每500ms闪烁一次
- task2：实现LED1每500ms闪烁一次
- task3：判断按键KEY0是否按下，按下则删掉task1
---
函数：
- 静态创建任务函数
```C
 TaskHandle_t xTaskCreateStatic( TaskFunction_t pxTaskCode,
                                 const char * const pcName,
                                 const uint32_t ulStackDepth,
                                 void * const pvParameters,
                                 UBaseType_t uxPriority,
                                 StackType_t * const puxStackBuffer,//任务堆栈，一般为数组，由用户分配
                                 StaticTask_t * const pxTaskBuffer );//任务控制块指针，由用户分配

```
---
 流程：
1. 在/freertos_dem.c/FreeRTOSConfig.h/中将configSUPPORT_STATIC_ALLOCATION配置为1
2. 实现两个接口函数vApplicationGetIdleTaskMemory和xTimerCreateTimerTask
```C
StaticTask_t idle_task_tcb;
StackType_t  idle_task_stack[configMINIMAL_STACK_SIZE];
//空闲任务内存分配
void vApplicationGetIdleTaskMemory( StaticTask_t **   ppxIdleTaskTCBBuffer,
                                    StackType_t **    ppxIdleTaskStackBuffer,
                                    uint32_t *        pulIdleTaskStackSize )
{
	*ppxIdleTaskTCBBuffer=&idle_task_tcb;
	*ppxIdleTaskStackBuffer=idle_task_stack;
	*pulIdleTaskStackSize=configMINIMAL_STACK_SIZE;
}
```
```C
StaticTask_t   timer_task_tcb;
StackType_t    timer_task_stack[configTIMER_TASK_STACK_DEPTH];
//软件内存分配
void vApplicationGetTimerTaskMemory( StaticTask_t ** ppxTimerTaskTCBBuffer,
                                     StackType_t ** ppxTimerTaskStackBuffer,
                                     uint32_t * pulTimerTaskStackSize )
{
	* ppxTimerTaskTCBBuffer=&timer_task_tcb;
	*ppxTimerTaskStackBuffer=timer_task_stack;
	*pulTimerTaskStackSize=configTIMER_TASK_STACK_DEPTH;
}
```
3. 编写代码
```C
#include "freertos_demo.h"
#include "./BSP/LCD/lcd.h"
#include "./BSP/KEY/key.h"
#include "./BSP/LED/led.h"
/*FreeRTOS*********************************************************************************************/
#include "FreeRTOS.h"
#include "task.h"


//start_task任务配置
#define START_TASK_STACK_SIZE      128
#define START_TASK_PRIO						 1
StackType_t                        start_task_stack[configMINIMAL_STACK_SIZE];
StaticTask_t 											 start_task_tcb;
void start_task(void *pvParameters);


//task1任务配置
#define TASK1_STACK_SIZE      128
#define TASK1_PRIO						2
StackType_t                   task1_stack[configMINIMAL_STACK_SIZE];
StaticTask_t 									task1_tcb;
TaskHandle_t									task1_handler;
void task1(void *pvParameters);


//task2任务配置
#define TASK2_STACK_SIZE      128
#define TASK2_PRIO						3
StackType_t                   task2_stack[configMINIMAL_STACK_SIZE];
StaticTask_t 								  task2_tcb;
TaskHandle_t									task2_handler;
void task2(void *pvParameters);


//task3任务配置
#define TASK3_STACK_SIZE      128
#define TASK3_PRIO						4
StackType_t                   task3_stack[configMINIMAL_STACK_SIZE];
StaticTask_t 								  task3_tcb;
TaskHandle_t									task3_handler;
void task3(void *pvParameters);


StaticTask_t idle_task_tcb;
StackType_t  idle_task_stack[configMINIMAL_STACK_SIZE];
//空闲任务内存分配
void vApplicationGetIdleTaskMemory( StaticTask_t **   ppxIdleTaskTCBBuffer,
                                    StackType_t **    ppxIdleTaskStackBuffer,
                                    uint32_t *        pulIdleTaskStackSize )
{
	*ppxIdleTaskTCBBuffer=&idle_task_tcb;
	*ppxIdleTaskStackBuffer=idle_task_stack;
	*pulIdleTaskStackSize=configMINIMAL_STACK_SIZE;
}


StaticTask_t   timer_task_tcb;
StackType_t    timer_task_stack[configTIMER_TASK_STACK_DEPTH];
//软件内存分配
void vApplicationGetTimerTaskMemory( StaticTask_t ** ppxTimerTaskTCBBuffer,
                                     StackType_t ** ppxTimerTaskStackBuffer,
                                     uint32_t * pulTimerTaskStackSize )
{
	* ppxTimerTaskTCBBuffer=&timer_task_tcb;
	*ppxTimerTaskStackBuffer=timer_task_stack;
	*pulTimerTaskStackSize=configTIMER_TASK_STACK_DEPTH;
}




//入口函数
void freertos_demo(void)
{
			xTaskCreateStatic((TaskFunction_t)   start_task,
                        (char *        )   "start_task",
                        (uint32_t      )   START_TASK_STACK_SIZE,
                        (void *        )   NULL,
                        (UBaseType_t   )   START_TASK_PRIO,
                        (StackType_t * )   start_task_stack,
                        (StaticTask_t *)   &start_task_tcb );
								
		//开启任务调度
		vTaskStartScheduler();
}


//创建三个任务
void start_task(void *pvParameters)
{
	//进入临界区
	taskENTER_CRITICAL();
	
	//task1
	task1_handler=xTaskCreateStatic((TaskFunction_t)   task1,
                    (char *        )   "task1",
                    (uint32_t      )   TASK1_STACK_SIZE,
                    (void *        )   NULL,
                    (UBaseType_t   )   TASK1_PRIO,
                    (StackType_t * )   task1_stack,
                    (StaticTask_t *)   &task1_tcb );
												
	//task2
	task2_handler=xTaskCreateStatic((TaskFunction_t)   task2,
                    (char *        )   "task2",
                    (uint32_t      )   TASK2_STACK_SIZE,
                    (void *        )   NULL,
                    (UBaseType_t   )   TASK2_PRIO,
                    (StackType_t * )   task2_stack,
                    (StaticTask_t *)   &task2_tcb );
												
	//task3											
	task3_handler=xTaskCreateStatic((TaskFunction_t)   task3,
                    (char *        )   "task3",
                    (uint32_t      )   TASK3_STACK_SIZE,
                    (void *        )   NULL,
                    (UBaseType_t   )   TASK3_PRIO,
                    (StackType_t * )   task3_stack,
                    (StaticTask_t *)   &task3_tcb );
								
	//退出临界区
	taskEXIT_CRITICAL();
	vTaskDelete(NULL);
}


//任务一：实现LED0每500ms翻转一次
void task1(void *pvParameters)
{
   while(1)
	 {
		 printf("task1正在运行\r\n");
		 LED0_TOGGLE();
		 vTaskDelay(500);
	 }
}


//任务二：实现LED1每500ms翻转一次
void task2(void *pvParameters)
{
   while(1)
	 {
		 printf("task2正在运行\r\n");
		 LED1_TOGGLE();
		 vTaskDelay(500);
	 }
}


//任务三：按下按键0，删除任务一
void task3(void *pvParameters)
{
	while(1)
	{
		  
		if(HAL_GPIO_ReadPin(GPIOE,GPIO_PIN_4)==0)
		{
			if(HAL_GPIO_ReadPin(GPIOE,GPIO_PIN_4)==0)
			{
				if(task1_handler!=NULL)
				{
					printf("task1被删除\r\n");
					vTaskDelete(task1_handler);
					task1_handler=NULL;
				}
			}
		}
		vTaskDelay(10);
	}
}
```

