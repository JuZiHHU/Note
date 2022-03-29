# 第3章 Linux多线程开发

- [第3章 Linux多线程开发](#第3章-linux多线程开发)
  - [3.1  线程概述](#31--线程概述)
    - [进程与线程](#进程与线程)
    - [线程之间共享和非共享资源](#线程之间共享和非共享资源)
  - [3.2 pthread_create()线程创建](#32-pthread_create线程创建)
  - [3.3 终止线程](#33-终止线程)

## 3.1  线程概述

- *与进程（process）类似，线程（thread）是允许应用程序并发执行多个任务的一种机制。一个进程可以包含多个线程。同一个程序中的所有线程均会独立执行相同程序，且共享同一份全局内存区域，其中包括初始化数据段、未初始化数据段，以及堆内存段。（传统意义上的 UNIX 进程只是多线程程序的一个特例，该进程只包含一个线程*
  
### 进程与线程

  1. *根本区别：进程是操作系统资源分配的基本单位，而线程是处理器任务调度和执行的基本单位；*
  2. *资源开销：每个进程都有独立的代码和数据空间（程序 ），程序之间的切换会有较大的开销；线程可以看做轻量级的进程，同一类线程共享代码和数据空间，每个线程都有自己独立的运行栈和程序计数器（PC），线程之间切换的开销小*
  3. *内存分配：同一进程的线程共享本进程的地址空间和资源，而进程之间的地址空间和资源是相互独立的；*
  4. *影响关系：一个进程崩溃后，在保护模式下不会对其他进程产生影响，但是一个线程崩溃整个进程都死掉。所以多进程要比多线程健壮；*


- *线程是轻量级的进程（LWP： Light Weight Process），在 Linux 环境下线程的本质仍是进程。*
- *查看指定进程的 LWP 号： ps –Lf pid*

- 进*程间的信息难以共享。由于除去只读代码段外，父子进程并未共享内存，因此必须采用一些进程间通信方式，在进程间进行信息交换。*
- *调用 fork() 来创建进程的代价相对较高，即便利用写时复制技术，仍然需要复制诸如内存页表和文件描述符表之类的多种进程属性，这意味着 fork() 调用在时间上的开销依然不菲。*
  
- *线程之间能够方便、快速地共享信息。只需将数据复制到共享（全局或堆）变量中即可。*
- *创建线程比创建进程通常要快 10 倍甚至更多。线程间是共享虚拟地址空间的，无需采用写时复制来复制内存，也无需复制页表*
  
### 线程之间共享和非共享资源

|共享资源| 非共享资源 |
| :----:| :---:|
|进程 ID 和父进程 ID|线程 ID|
| 进程组 ID 和会话 ID|信号掩码|
| 用户 ID 和 用户组 ID|线程特有数据|
|文件描述符表|线程特有数据|
| 信号处置|实时调度策略和优先级|
| 文件系统的相关信息：文件权限掩码（umask）、当前工作目录|栈，本地变量和函数的调用链接信息|
|虚拟地址空间（除栈、 .text）| |

*NPTL，或称为 Native POSIX Thread Library，是 Linux 线程的一个新实现
它克服了LinuxThreads 的缺点，同时也符合 POSIX 的需求。与 LinuxThreads相比,
它在性能和稳定性方面都提供了重大的改进。
查看当前 pthread 库版本： getconf GNU_LIBPTHREAD_VERSION*

## 3.2 pthread_create()线程创建

```c++
/*
    一般情况下,main函数所在的线程我们称之为主线程（main线程），其余创建的线程
    称之为子线程。
    程序中默认只有一个进程，fork()函数调用，2进程
    程序中默认只有一个线程，pthread_create()函数调用，2个线程。

    #include <pthread.h>
    int pthread_create(pthread_t *thread, const pthread_attr_t *attr, 
    void *(*start_routine) (void *), void *arg);

        - 功能：创建一个子线程
        - 参数：
            - thread：传出参数，线程创建成功后，子线程的线程ID被写到该变量中。
            - attr : 设置线程的属性，一般使用默认值，NULL
            - start_routine : 函数指针，这个函数是子线程需要处理的逻辑代码
            - arg : 给第三个参数使用，传参
        - 返回值：
            成功：0
            失败：返回错误号。这个错误号和之前errno不太一样。
            不能用perror("")输出错误号信息
            获取错误号的信息：  char * strerror(int errnum);
        - Compile and link with -pthread 
            编译时需要指定静态库
            gcc pthread_create.c -o create -l pthread
            或
            gcc pthread_create.c -o create -pthread

*/
#include <stdio.h>
#include <pthread.h>
#include <string.h>
#include <unistd.h>
void * callback(void * arg) {
    printf("child thread...\n");
    printf("arg value: %d\n", *(int *)arg);
    return NULL;
}
int main() {
    pthread_t tid;
    int num = 10;
    // 创建一个子线程
    int ret = pthread_create(&tid, NULL, callback, (void *)&num);
    if(ret != 0) {
        // 传入错误号获取错误信息
        char * errstr = strerror(ret);
        printf("error : %s\n", errstr);
    } 
    for(int i = 0; i < 5; i++) {
        printf("%d\n", i);
    }
    sleep(1);
    return 0;   // exit(0);
}
```

## 3.3 终止线程pthread_exit()

```c++
/*
    #include <pthread.h>
    void pthread_exit(void *retval);
        功能：终止一个线程，在哪个线程中调用，就表示终止哪个线程
        参数：
            retval:需要传递一个指针，作为一个返回值，可以在pthread_join()中获取到。s

    pthread_t pthread_self(void);
        功能：获取当前的线程的线程ID

    int pthread_equal(pthread_t t1, pthread_t t2);
        功能：比较两个线程ID是否相等
        不同的操作系统，pthread_t类型的实现不一样，有的是无符号的长整型，有的
        是使用结构体去实现的。
        RETURN VALUE
        If the two thread IDs are equal, pthread_equal() returns a nonzero value; otherwise, it returns 0.
*/
#include <stdio.h>
#include <pthread.h>
#include <string.h>

void * callback(void * arg) {
    printf("child thread id : %ld\n", pthread_self());
    return NULL;    // pthread_exit(NULL);
} 

int main() {

    // 创建一个子线程
    pthread_t tid;
    int ret = pthread_create(&tid, NULL, callback, NULL);

    if(ret != 0) {
        char * errstr = strerror(ret);
        printf("error : %s\n", errstr);
    }

    // 主线程
    for(int i = 0; i < 5; i++) {
        printf("%d\n", i);
    }

    printf("tid : %ld, main thread id : %ld\n", tid ,pthread_self());

    // 让主线程退出,当主线程退出时，不会影响其他正常运行的线程。
    pthread_exit(NULL);

    printf("main thread exit\n");

    return 0;   // exit(0);
}
```


