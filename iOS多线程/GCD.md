[TOC]

# GCD

### GCD（中央调度器） 

**纯C语言，提供非常多强大的函数** 

主要有两部分组组成 

1. **任务**(block) 

2. **队列**(queue) 

使用起来就是定制任务，再添加到队列中 

* GCD会自动将队列的任务取出，放到线程中执行整体使用的是FIFO（先进先出） 

* 但是GCD无法被取消不像是NSOperation 

#### 封装任务 

``` objective-c
dispatch_sync(dispatch_queue_t _Nonnull queue, ^(void)block); //同步
dispatch_async(dispatch_queue_t _Nonnull queue, ^(void)block); //异步
```

**第一个参数**对列名 **第二个参数**block块对象 

其中同步（sync）和异步（async）的主要区别在于会不会阻塞当前进程，直到Block中任务执行完成。 

1. 同步的话，不会开启新线程，并且会**阻塞**当前的进程直到Block的执行完成。 

2. 异步的话，会开启新的线程，当前进程会直接往下执行即**不会阻塞**当前的线程。 

#### GCD中的队列 

`dispatch_queue_t queue = dispatch_queue_create("C语言的字符串 队列名不能加@", DISPATCH_QUEUE_CONCURRENT|SERIAL); `

1. 声明一个队列对象**dispatch_queue_t**

2. 队列创建dispatch_queue_create 对列名 DISPATCH_QUEUE_CONCURRENT(并行)|SERIAL（串行）可以**为NULL**则默认为**串行对列**
   1. 通过create出来的对列使用和全局对列中的默认优先级相同执行优先级的对列 

##### 两种队列 

两种对列都遵循**FIFO**原则。 

###### 并发队列 

1. 只要第一个任务取出来执行就可以取第二个任务 
   1. 两种并发对列 
      1. 自己创建的dispatch_queue_create 
      2. 全局队列`dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0);` 
         1. Global Dispatch Queue一共有四种优先级 
         2. High Priority|Default Priority|Low Priority|Background Priority 
         3. 向Global Dispatch Queue追加处理的时候，应该选择对应优先级 
3. 并发 + 异步的话会创建N条线程 线程的个数 ！= 任务数量 ⚠️ 

###### 串行队列

1. 第一个任务取出来之后必须等待任务执行完，才能执行下一个 
1. 主对列 特殊的串型对列（和主线程先关联的） 
   1. dispatch_get_main_queue() 
   2. 开启多条串行对列可以有不限量的线程生成
      1. 可以避免更新相同资源导致数据竞争 

#### 改变对列优先级 

```objective-c
dispatch_set_target_queue(<dispatch_object_t _Nonnull object>,<dispatch_queue_t _Nullable queue>);
```

**第一参数**是要改变优先级的对列 

**第二参数**是指要相同的优先级 

#### 主对列 

1. 放在主对列的任务都会放到主线程中执行 

2. 不会开启新的线程 

3. 同步函数+主对列 = 死锁 
   1. dispatch_sync会立即阻塞当前的**主线程**，然后把 Block 中的任务放到 main_queue 中，可是 main_queue 中的任务会被取出来放到主线程中执行，但主线程这个时候已经被阻塞了，所以 Block 中的任务就不能完成，由于它不完成，dispatch_sync 就会一直阻塞主线程，这就是死锁了。

4. 异步函数+主对列 = 不会开线程，所有任务都会在主线程中串行执行 

#### ~~对列中的释放机制~~ 

1. ⚠️在声明自定义对列之后要释放 
   1. dispatch_release(queue); 

2. 同理dispatch_retain(queue);同样有效 
   1. 对于主对列以及全局对列释放无效 

#### GCD中的一次性代码 

```objective-c
static dispatch_once_t onceToken; 

dispatch_once(&onceToken, ^{ 

NSLog(@"once"); 

}); 
```

* 在程序执行过程中只会执行一次 

* 本身是线程安全的 

* 应用于单例模式 

> 通过判断onceToken的值==0来判断是否执行 =-1不执行 

#### GCD中的通信 

在一个GCD子线程中声明另一个异步子线程 

```objc 
dispatch_async(dispatch_get_main_queue(), ^{ 

self.webImage.image = image; 

});  
```

获取主线程并在主线程中修改 

#### GCD中的延迟执行 

```objc 
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{ 
NSLog(@"GCD延迟执行"); 
}); 
```

> 先等待延迟的时间再调整到对列 

#### GCD中的迭代（遍历） 

开启多条子线程和主线程执行任务 

```objc 
dispatch_apply(10,queue,^(size_t i) { 
NSLog(@"%zd",i); 
}); 
```

1. 参数1循环次数 

2. 对列（并发）如果是 **主对列会发生死锁** 

3. 参数名 

#### GCD中的栅栏函数 

##### async

` dispatch_barrier_async(queue,^{//栅栏函数}); ` 

* 会拦住前面的线程等待执行完之后再执行后面的（会阻塞queue） 

* 只能在自定义的并发队列中使用栅栏函数 

##### sync

`dispatch_barrier_sync(queue,^{}); ` 

* 会阻塞queue和当前的线程 

#### GCD中的队列组 

```objc 
dispatch_group_t group = dispatch_group_create(); 

dispatch_group_async(group,queue,^{ 

//声明异步队列组线程 

}); 

dispatch_group_notify(group,queue,^{ 

//group监听的队列组 

//queue执行block的对列 

}); 
```

#### GCD中的定时器 

相比起NSTimer会更精确一些，因为NSTimer会受到运行模式的改变的影响 

```objc
dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, dispatch_get_main_queue()); 
```

参数说明 

* 第一个参数 DISPATCH_SOURCE_TYPE_TIMER source的类型dispatch_source_type_timer 定时器 

* 第二个参数 0 对第一个参数的描述 

* 第三个参数 0 对第一个参数更详细的描述 

* 第四个参数 dispatch_get_main_queue 对列（GCD-4）决定代码块在那个线程中执行 

```objc 
dispatch_source_set_timer(timer, DISPATCH_TIME_NOW, 2 * NSEC_PER_SEC, 0 * NSEC_PER_SEC); 
```

参数说明 

* 第一个参数 定时器对象 

* 第二个参数 开始计时的时间 dispatch_time_now 从现在开始 

* 第三个参数 间隔时间 NSEC_PER_SEC是10^9 

* 第四个参数 精准度（允许的误差） 

```objc 
dispatch_source_set_event_handler(timer, ^{ 
NSLog(@"%@",[NSThread currentThread]); 
}); 
dispatch_resume(timer); 
```

事件回调「定时器内执行的任务」 

```objc 
//启动定时器 
despatch_resume(timer); 

//添加引用 
self.timer = timer; 
```

##### GCD中的dispatch_time_t 

用于计算相对时间 

```objc 
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW,3ull*NSEC_PER_SEC); 
```

dispatch_time_t 对象 

DISTPATCH_TIME_NOW从现在开始 

ull是C中的数值字面量 

NSEC_PER_SEC|MSEC 秒|毫秒 

#### dispatch_suspend/dispatch_resume 

对列的挂起|恢复 

挂起之后追加到对列中的但是没有执行的会挂起 

```objc 
dispatch_suspend(queue); 

dispatch_resume(queue); 
```

#### Dispatch Semaphore 

dispatch_semaphore 是信号量，但当信号总量设为 1 时也可以当作锁来。在没有等待情况出现时，它的性能比 pthread_mutex 还要高，但一旦有等待情况出现时，性能就会下降许多。相对于 OSSpinLock 来说，它的优势在于等待时不会消耗 CPU 资源。 

```objc 
dispatch_semaphore_t semaphore = dispatch_semaphore_create(1); 

// 注意dispatch_semaphore_create(num) num必须大于等于0 否则会返回一个NULL 

dispatch_semaphore_signal(semaphore); 

// 使传入的信号量 + 1 
```

下面这个函数会使传入的信号量的值减1；这个函数的作用是这样的，如果信号量的值大于0，该函数所处线程就继续执行下面的语句，并且将信号量的值减1；如果信号量的值为0，那么这个函数就阻塞当前线程等待timeout（注意timeout的类型为dispatch_time_t），如果等待的期间信号量的值被dispatch_semaphore_signal函数加1了，且该函数（即dispatch_semaphore_wait）所处线程获得了信号量，那么就继续向下执行并将信号量减1。如果等待期间没有获取到信号量或者信号量的值一直为0，那么等到timeout时，其所处线程自动执行其后语句。 

`dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER); `

##### NSLock 

```objc 
NSLock *lock = [[NSLock alloc]init]; 

[lock tryLock]; //尝试获取锁，如果获取不到的话返回NO不会阻塞当前线程 

[lock lockBeforeDate:[NSDate date]]; 

//尝试在NSdate时间内获取锁对象，并阻塞当前线程，如果规定时间内找不到锁，解锁线程返回NO; 

[lock unlock]; // 解锁 
```

# Dispatch Queue 

> Dispatch Queue的本质是添加Block 

## Dispatch Queue的组成 

Dispatch Queue 是通过**结构体**和**链表**实现的FIFO(先进先出)**队列**，FIFO队列主要负责**管理**通过dispatch_async等函数添加的block。可以理解成在程序从上到下追加了一组的Blocks，排除延迟dispatch_after其内部过程是个FIFO的过程。 

### Block的添加 

Block不是直接添加进FIFO对列的。Block先加入Dispatch Continuation（dispatch_continuation_t 类结构体）中，再加入FIFO中。Dispatch Continuation用于记忆Block所属的信息，即执行上下文。 



## Dispatch Queue执行Block的过程 

在这之前要介绍一下Dispatch Queue实现的软件组件 

|    组件名称     |     提供技术      |
| :-------------: | :---------------: |
|   libdispatch   |  Dispatch Queue   |
| Libc（pthread） | pthread_workqueue |
|     XNU内核     |     workqueue     |

1. XNU内核 
   1. XNU内核是Mac和iOS的核心，有三个主要部分组成的一个分层体系结构；内核XNU是Darwin的核心，也是整个OS X的核心。 
2. Libc 
   1. libc是Linux下的ANSI C的函数库。 
3. libdispatch 
   1. 包含GCD的C语言的库 
### 通知XNU过程 
Global Dispatch Queue中执行Block时，libdispatch会从FIFO中抽取出Dispatch Continuation，调用**pthread_workqueue_additem_np**函数，将该Global Dispatch Queue自身以及符合优先级的workqueue信息等传递给参数。 

**pthread_workqueue_additem_up**函数使用workq_kernreturn系统调用。通知workqueue增加应当执行的项目，而XNU内核根据系统状态判断是否要生成线程。如果是OverCommit优先级的Global Dispatch Queue则始终生成线程 

#### Global Dispatch Queue的种类（8） 

1. Global Dispatch Queue（High （Overcommit）Priority） 

2. Global Dispatch Queue（Default （Overcommit）Priority） 

3. Global Dispatch Queue（Low （Overcommit）Priority） 

4. Global Dispatch Queue（Background（Overcommit） Priority） 

优先级中加入了**Overcommit**的会**强制生成线程**。 

相对的XUN中也有四中优先级的workqueue 

* WORKQUEUE_HIGH_PRIORITY 

* WORKQUEUE_DEFAULT_PRIORITY 

* WORKQUEUE_LOW_PRIORITY 

* WORKQUEUE_BG_PRIORITY 

### 执行Block过程 

XNU中的workqueue的线程执行pthread_workqueue函数，调用libdispatch的回调函数，在该回调函数中**执行Block**。 

### 结束 

Block执行结束之后，通知Dispatch Group结束、释放Dispatch Continuation等，开始准备执行加入到Global Dispatch Queue中的下一个Block。 