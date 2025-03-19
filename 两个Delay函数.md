# 两个Delay函数
- vTaskDelay(15)：等待指定个数的Tick(这里为15), Interrupt才能变为就绪状态。该函数无法保证任务周期性的执行
- vTaskDelayUntil(&preTime,15):等待 到 指定的绝对时刻，才能变为就绪态。该函数可以使得任务周期性的执行
BaseType_t preTime=vTaskGetTickCount() preTime可由vTaskGetTickCount()这个函数获得
