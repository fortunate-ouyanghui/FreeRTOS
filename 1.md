# 任务创建和删除（动态方法）
## 实验目的：
设计四个任务：start_task,task1,task2,task3
四个任务的功能如下：
- start_task：用来创建其他三个任务
- task1：实现LED0每500ms闪烁一次
- task2：实现LED1每500ms闪烁一次
- task3：判断按键KEY0是否按下，按下则删掉task1
---
函数：
- 动态创建任务函数
```C
BaseType_t xTaskCreate( TaskFunction_t pvTaskCode,//指向任务函数的指针

                         const char * const pcName,//任务名字

                         const configSTACK_DEPTH_TYPE uxStackDepth,//任务堆栈大小，单位为字

                         void *pvParameters,//传递给任务函数的参数

                         UBaseType_t uxPriority,//任务优先级

                         TaskHandle_t *pxCreatedTask//任务句柄

                       );
```
- 任务删除函数
```C
void vTaskDelete( TaskHandle_t xTask )//xTask为待删除的任务，当传入NULL时代表删除任务自身（当前正在运行的任务）
```
---
 流程：
1. 在/freertos_dem.c/FreeRTOSConfig.h/中将configSUPPORT_DYNAMIC_ALLOCATION配置为1
2. 编写代码
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
TaskHandle_t											 start_task_handler;
void start_task(void *pvParameters);


//task1任务配置
#define TASK1_STACK_SIZE      128
#define TASK1_PRIO						2
TaskHandle_t									task1_handler;
void task1(void *pvParameters);


//task2任务配置
#define TASK2_STACK_SIZE      128
#define TASK2_PRIO						3
TaskHandle_t									task2_handler;
void task2(void *pvParameters);


//task3任务配置
#define TASK3_STACK_SIZE      128
#define TASK3_PRIO						4
TaskHandle_t									task3_handler;
void task3(void *pvParameters);



//入口函数
void freertos_demo(void)
{
   xTaskCreate( (TaskFunction_t    				 )	start_task,   											/* 任务函数 */
                (char* 					   				 )	"start_task", 											/* 任务名称 */
                (configSTACK_DEPTH_TYPE    )	START_TASK_STACK_SIZE,  						/* 任务堆栈大小 */
                ( void * 									 )	NULL,  													    /* 传给任务函数的参数 */
                (UBaseType_t							 )	START_TASK_PRIO, 									  /* 任务优先级 */
                (TaskHandle_t *					   )	&start_task_handler);               /* 任务句柄 */
								
		//开启任务调度
		vTaskStartScheduler();
}


//创建三个任务
void start_task(void *pvParameters)
{
	//进入临界区
	taskENTER_CRITICAL();
	//创建任务一
   xTaskCreate( (TaskFunction_t    				 )	task1,   											/* 任务函数 */
                (char* 					   				 )	"task1", 											/* 任务名称 */
                (configSTACK_DEPTH_TYPE    )	TASK1_STACK_SIZE,  						/* 任务堆栈大小 */
                ( void * 									 )	NULL,  												/* 传给任务函数的参数 */
                (UBaseType_t							 )	TASK1_PRIO, 									/* 任务优先级 */
                (TaskHandle_t *					   )	&task1_handler);              /* 任务句柄 */
	
	//创建任务二
		xTaskCreate( (TaskFunction_t    				 )	task2,   										/* 任务函数 */
                (char* 					   				 )	"task2", 											/* 任务名称 */
                (configSTACK_DEPTH_TYPE    )	TASK2_STACK_SIZE,  						/* 任务堆栈大小 */
                ( void * 									 )	NULL,  											  /* 传给任务函数的参数 */
                (UBaseType_t							 )	TASK2_PRIO, 									/* 任务优先级 */
                (TaskHandle_t *					   )	&task2_handler);              /* 任务句柄 */
		
	//创建任务三
		xTaskCreate( (TaskFunction_t    				 )	task3,   										/* 任务函数 */
                (char* 					   				 )	"task3", 											/* 任务名称 */
                (configSTACK_DEPTH_TYPE    )	TASK3_STACK_SIZE,  						/* 任务堆栈大小 */
                ( void * 									 )	NULL,  												/* 传给任务函数的参数 */
                (UBaseType_t							 )	TASK3_PRIO, 								  /* 任务优先级 */
                (TaskHandle_t *					   )	&task3_handler);              /* 任务句柄 */
								
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
