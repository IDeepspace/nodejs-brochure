## 事件循环机制

### 一、概念介绍

#### 进程和线程

[进程](https://zh.wikipedia.org/zh-cn/进程)（`process`）和[线程](https://zh.wikipedia.org/zh-cn/线程)（`thread`）是操作系统的基本概念。先看看下面这个形象的比喻：

```
- 进程是一个工厂，工厂有它的独立资源
- 工厂之间相互独立
- 线程是工厂中的工人，多个工人协作完成任务
- 工厂内有一个或多个工人
- 工人之间共享空间
```

完善一下概念：

```
- 工厂的资源 -> 系统分配的内存（独立的一块内存）
- 工厂之间的相互独立 -> 进程之间相互独立
- 多个工人协作完成任务 -> 多个线程在进程中协作完成任务
- 工厂内有一个或多个工人 -> 一个进程由一个或多个线程组成
- 工人之间共享空间 -> 同一进程下的各个线程之间共享程序的内存空间（包括代码段、数据集、堆等）
```



#### 同步

后一个任务需要等待前一个任务执行结束之后，才可以执行，程序的执行顺序与任务的排列顺序是一致的、同步的。也就是必须一件一件做事，等前一件做完了才能做下一件事。

就像早上起床后，先洗涮，然后才能吃饭，不能在洗涮没有完成时，就开始吃饭。

按照这个定义，其实绝大多数函数都是同步调用。但是一般而言，我们在说同步、异步的时候，特指那些需要其他部件协作或者需要一定时间完成的任务。最常见的例子就是聊天的时候发送消息，相应的函数发送一个消息给某个窗口，在对方处理完消息之前，这个函数不返回；当对方处理完毕以后，该函数才把消息处理函数所返回的值返回给调用者。



#### 异步

异步的概念和同步相对。当一个异步过程调用发出后，调用者不能立刻得到结果，进程不需要一直等下去，而是继续执行下面的操作，不管其他进程的状态。当有消息返回时系统会通知进程进行处理，这样可以提高执行的效率。