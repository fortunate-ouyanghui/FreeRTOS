- 使用stm32cubemax生成项目文件后，在项目路径下新建FreeRTOS文件夹，如图所示：
![103-1](https://github.com/user-attachments/assets/b9514246-b316-4e66-8317-ab58762f860c)
- 将下载的FreeRTOS源码Source路径下的所有.c文件和include,portable文件夹copy到FreeRTOS文件夹下，如图所示：
![103-2](https://github.com/user-attachments/assets/a78e0bcc-794e-444b-970b-32d161f10e26)
![103-3](https://github.com/user-attachments/assets/74da5ced-9d25-48a4-b431-b56aff63f1d5)
- 删除portable文件夹下除Keil,MemMang,RVDS以外的所有文件夹，如图所示：
![103-4](https://github.com/user-attachments/assets/fe225611-348d-4e70-aba6-fbb7a49286df)
- 新建两个工程分组，并添加相应文件，如图所示：
![103-5](https://github.com/user-attachments/assets/f1c9fbca-9765-4a49-88de-5295a36d2523)
![103-6](https://github.com/user-attachments/assets/ecb52c70-3d6a-4d75-aab9-004c4ac2a39e)
- 添加头文件路径，如图所示：
![103-7](https://github.com/user-attachments/assets/32b1e4b4-8cfe-4b39-98b5-dd026f04e8ed)
![103-8](https://github.com/user-attachments/assets/95e21d17-fa10-4420-b44e-3f380b517c14)
- 添加FreeRTOSConfig.h文件（将FreeRTOS源码的FreeRTOSConfig.h移植到本项目中），如图所示：
![103-9](https://github.com/user-attachments/assets/da0d93a1-9ae9-4326-961d-1fa3c4829925)
![103-10](https://github.com/user-attachments/assets/1f74a339-3859-43b6-b20e-3e5e8df8a269)
- 运行，此时会报错，如图解决：
![103-11](https://github.com/user-attachments/assets/bb527139-0cf1-4669-9b22-3f4cc8610294)
- 然后：
![103-12](https://github.com/user-attachments/assets/e7fd9223-f3b9-4ae3-b9c2-d67c323724f0)
- 最后
![103-13](https://github.com/user-attachments/assets/d0bc11db-9bae-43ce-9ea8-77881d3bd7db)
- 参考文档
- ![参考文档](https://blog.csdn.net/sinat_58149788/article/details/126089194)

