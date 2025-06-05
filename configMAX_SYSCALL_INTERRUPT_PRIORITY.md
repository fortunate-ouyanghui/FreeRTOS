# 中断管理实验
## 注意事项：
- 低于configMAX_SYSCALL_INTERRUPT_PRIORITY优先级的中断里才允许调用FreeRTOS的API函数
- 任务切换发生在Pendable request for system service中断的服务函数里
- taskDISABLE_INTERRUPTS()屏蔽部分中断 taskENABLE_INTERRUPTS()使taskDISABLE_INTERRUPTS失效
- taskENTER_CRITICAL()不允许发生任务切换 taskEXIT_CRITICAL()允许发生任务切换
- 中断优先级数值越小越优先，任务优先级数值越大越优先
## 实验目的
本实验会使用两个定时器，一个优先级为4，一个优先级为6，注意：系统所管理的优先级范围为5-15
实验现象：两个定时器每1s，打印一段字符串，当关中断时，停止打印，开中断时持续打印。
本实验将设计2个任务：start_task,task1  
2个任务的功能如下：
- start_task:用来创建task1任务
- task1：中断测试任务，任务中将调用关中断和开中断函数来体现对中断的管理作用
## 代码
```C

void task1(void *pvParameters)
{
    uint32_t task1_num = 0;
    
    while (1)
    {
        if (++task1_num == 5)
        {
            printf("FreeRTOS关闭中断\r\n");
            portDISABLE_INTERRUPTS();       /* FreeRTOS关闭中断 */
            delay_ms(5000);
            printf("FreeRTOS打开中断\r\n");
            portENABLE_INTERRUPTS();        /* FreeRTOS打开中断 */
        }
        
        vTaskDelay(1000);
    }
}
```
