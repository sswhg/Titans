- #### block_io 为什么会阻塞？

  打开文件时没有指定`O_NONBLOCK`, 文件默认以阻塞的方式打开,当`read`从标准输入读数据的时候就会卡住.

- #### block_io_ex 为什么无法并发处理两个不同设备来的数据?

  假如设备1一直没有数据到来，那么`read`就一直卡在 读取设备1的数据，即使设备2有数据到来，也将得不到处理。

- #### 查阅手册，`fcntl` 各个参数以及返回值的含义是什么？

  `int fcntl(int fd, int cmd, ... /* arg */ );`该函数操作文件描述符

  - fd: 打开的文件描述符

  - cmd: 对文件描述符的操作,有如下选项

    | 操作                     | 功能                                                         |
    | ------------------------ | ------------------------------------------------------------ |
    | F_DUPFD (int)            | 复制文件描述符,返回新的文件描述符                            |
    | F_DUPFD_CLOEXEC (int)    | 复制文件描述符,设置close-on-exec标志                         |
    | F_GETFD (void)           | 获取文件描述符的标志                                         |
    | F_SETFD (int)            | 设置文件描述符的标志                                         |
    | F_GETFL (void)           | 获取文件的访问权限                                           |
    | F_SETFL (int)            | 设置文件的访问权限                                           |
    | F_SETLK (struct flock *) | 给文件加上进程锁                                             |
    | F_SETLKW(struct flock *) | 给文件加上进程锁，如果此文件之前已经被加了锁，则一直等待锁被释放 |
    | F_GETLK(struct flock *)  | 如果已经被加锁，返回该锁的数据结构。如果没有被加锁，将l_type设置为F_UNLCK |

  - 第三个参数是可选的,是否需要第三个参数取决于cmd

  - 返回值:成功的返回值取决于对文件描述符的操作, 出错返回-1

    |         | 返回值                   |
    | ------- | ------------------------ |
    | F_DUPFD | 返回新的文件描述符       |
    | F_GETFD | 返回文件描述符的标志的值 |
    | F_GETFL | 返回文件的状态标志       |

    其他操作返回0

- #### 你觉得非阻塞 IO 解决了什么问题？

  非阻塞 IO 解决了阻塞 IO从不同设备读取数据,不能及时响应的问题.

- #### nonblock_io 会有什么问题？

  如果设备没有数据到达,while循环将不断空转,这是对CPU的极大浪费.