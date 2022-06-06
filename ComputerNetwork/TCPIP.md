# 应用层协议

OSI 7层参考模型

TCP/IP 协议

![image-20220330193156365](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220330193156365.png)

netstat -natp 查看网络连接

协议双方约定

创建连接 解析协议



# 传输控制层协议

TCP/UDP

## TCP面向连接的可靠的传输协议

### 三次握手

之后开辟对应的资源 这个资源就是连接

![img](https://img-blog.csdn.net/20170104214009596?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2h1c2xlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

server死了 client不知道 不存在真正的物理上的连接 可以开启心跳机制来确认

![image-20220330195446780](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220330195446780.png)

port 总量 65535 连接有唯一性排他性

listen状态和established状态都是完整的连接

两边都是同一IP 同一端口可以用不同协议监听 如TCP/UDP

一边同一IP 同一端口号可以连接不同的地址(setReuseAddress(true))

TCP/IP是内核协议 三次握手之后产生资源 之后分配给程序

资源 socket receive-queue send-queue 都在kernel里

所谓的网络IO是读写的 本机  内核当中的queue

IO模型 ：在读取不到的情况下，程序的处理过程就是BIO、NIO、AIO

### 四次分手

![image-20220330203241812](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220330203241812.png)

释放资源

![image-20220330204211932](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220330204211932.png)



# 网络层协议

IP 网络号+主机号

子网掩码 网络号

Gateway网关(下一跳)

![image-20220331215601814](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220331215601814.png)

route

在路由表中与genmask进行按位与 得到下一跳的IP地址

之后交给链路层 获取mac地址 

每一次跳跃IP地址不变 但是MAC地址变化(类似链表 )

![image-20220331215915175](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220331215915175.png)



# 面试题目

![image-20220331221046324](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220331221046324.png)

1.RPC时候可以用1-3个socket 尽量压榨资源 但同时也没有必要开辟过多的资源

2.CAP理论指的是一个分布式系统最多只能同时满足一致性（Consistency）、可用性（Availability）和分区容错性（Partition tolerance）这三项中的两项 网络中P是必然存在

3.socket是一个四元组

4.

5.TCP不走完3次握手应该不行
