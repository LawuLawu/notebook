# 程序运行的过程

cpu会保护内核程序中的方法、数据

调用分为：方法调用(FC)，系统调用(SC)

CPU不断处理中断，包括软件中断(实现SC)、硬件中断

![image-20220405162112540](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220405162112540.png)

# BIO/NIO/OIO/EPOLL/AIO

为什么:

减少触发系统调用 系统调用消耗过大
