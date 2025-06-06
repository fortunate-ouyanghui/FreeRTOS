- 向队列xQueue中写入一个数据pvItemToQueue，若队列满了最多等待xTicksToWait，超过了这个时间，就会返回BaseType_t，返回值表示该函数执行的状态如：成功，队列满了执行失败
```C
BaseType_t xQueueSendToBack(
    QueueHandle_t xQueue,         // 队列句柄
    const void *pvItemToQueue,    // 待发送数据的指针
    TickType_t xTicksToWait       // 等待时间（超时值）
);
```
- 清空队列xQueue所有数据，始终返回pdTRUE
```C
BaseType_t xQueueReset( QueueHandle_t xQueue );
```
- 查看队列的名称xQueue,返回值为队列的名称
```C
const char *pcQueueGetName( QueueHandle_t xQueue );
```
- 查看队列xQueue可用的空间,返回值为队列可用的空间
```C
UBaseType_t uxQueueSpacesAvailable( QueueHandle_t xQueue );
```
- 从队列xQueue读取一个数据pvBuffer，成功返回pdTRUE，若队列为空且超时返回pdFALSE
```C
BaseType_t xQueueReceive(
    QueueHandle_t xQueue,         // 队列句柄
    void *pvBuffer,               // 接收数据的缓冲区指针
    TickType_t xTicksToWait       // 等待时间（超时值）
);
```
