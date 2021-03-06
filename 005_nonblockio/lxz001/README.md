### 1.block_io为什么会阻塞？
    答：因为block_io读取文件采用的是read()函数，read()会根据文件描述符fd的阻塞标记来采用阻塞或非阻塞的方式读取文件，而open()函数缺省时返回的fd是阻塞的，所以它是阻塞的。

### 2.block_io_ex 为什么无法并发处理两个不同设备来的数据?
    答：因为在一个循环内，两个read()函数串行阻塞地读取不同设备传来的数据，如果不是两个read()函数都读取到数据，则至少阻塞在一处，不能进行下一次循环，所以不能并发处理两个不同设备来的数据。

### 3.查阅手册，fcntl 各个参数以及返回值的含义是什么？
    答：fcntl系统调用对已打开的文件描述符进行各种控制操作，来改变已打开文件的各种属性。

    int fcntl(int fd, int cmd);
    int fcntl(int fd, int cmd, long arg);
    int fcntl(int fd, int cmd, struct flock* lock);

    cmd:
    1.F_DUPFD 复制fd指向的文件描述符，调用成功返回新的文件描述符，与旧文件描述符共同指向同一文件。
    2.F_GETFD 读取文件close-on-exec标志，其意义是在进程的子进程执行exec前是否要关闭这个文件描述符。
    3.F_SETFD 将文件描述符close-on-exec标志设置为第三个参数arg的最后一位。成功返回0。
    4.F_GETFL 获取文件打开方式的标志。
    5.F_SETFL 设置文件打开方式为arg指定方式。成功返回0。
    6.F_SETLK 设置或释放锁，当flock 的 l_type 为F_RDLCK为读锁，F_WDLCK为写锁，F_UNLCK为解锁。成功返回0。
    7.F_SETLKW 与6差不多，不同是该上锁方式是阻塞方式。当希望设置的锁因其他锁而被阻止设置，它会等待相冲突的锁被释放。
    8.F_GETLK  第三个参数lock指向希望被设置的锁，如果锁可以被设置，则lock的l_type被修改成F_UNLCK，但不会设置锁，如果锁冲突，lock不被修改。fcntl调用成功返回0。
    fcntl调用失败都返回-1。
   
### 4.你觉得非阻塞IO解决了什么问题？
    答：解决从多个设备读取数据时相互影响的问题。使从多个设备读取数据可以相互独立。

### 5.nonblock_io会有什么问题？
    答：整个程序会不间断的运行，哪怕不传输数据，占用大量CPU时间，浪费。
