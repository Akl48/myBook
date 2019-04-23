[TOC]

# RunLoop 

* 保证程序的持续运行 

* 处理App中的各种时间 

* 节省CPU资源，提高程序性能 

* 相当于死循环，保持程序的持续运行 

* main函数中的RunLoop 

* UIApplicationMain函数内部启动了一个RunLoop，该函数返回一个int类型的值 

* 这个默认启动的RunLoop和主线程相关 

## RunLoop对象 

1. OC中的NSRunLoop 
   1. 基于CFRunLoopRef的封装 
   2.  线程不安全 

2. C中的CFRunLoopRef 

   1. 线程安全 

   2. 基于pthread_t来管理的 

3. 他们是等价的可以相互转化其实NSRunLoop就是对CFRunLoopRef的封装 

### **RunLoop和线程** 

1. RunLoop和线程的关系：一个RunLoop对应一条唯一的线程 
   1. 以字典存储起来的RunLoop对应一个唯一的线程，key对应的是pthread_t Value对应的是RunLoop 
   2. RunLoop的创建：主线程的RunLoop已经创建好了，子线程的RunLoop要自己创建 

   3. RunLoop的生命周期：在第一次获取是创建，在线程结束时，销毁 

```objc 
// 全局的Dictionary，key 是 pthread_t， value 是 CFRunLoopRef 

static CFMutableDictionaryRef loopsDic; 

// 访问 loopsDic 时的锁 

static CFSpinLock_t loopsLock; 

// 获取一个 pthread 对应的 RunLoop。 

CFRunLoopRef _CFRunLoopGet(pthread_t thread) { 

OSSpinLockLock(&loopsLock); 

if (!loopsDic) { 

// 第一次进入时，初始化全局Dic，并先为主线程创建一个 RunLoop。 

loopsDic = CFDictionaryCreateMutable(); 

CFRunLoopRef mainLoop = _CFRunLoopCreate(); 

CFDictionarySetValue(loopsDic, pthread_main_thread_np(), mainLoop); 

} 

// 直接从 Dictionary 里获取。 

CFRunLoopRef loop = CFDictionaryGetValue(loopsDic, thread)); 

if (!loop) { 

// 取不到时，创建一个 

loop = _CFRunLoopCreate(); 

CFDictionarySetValue(loopsDic, thread, loop); 

// 注册一个回调，当线程销毁时，顺便也销毁其对应的 RunLoop。 

_CFSetTSD(..., thread, loop, __CFFinalizeRunLoop); 

} 

OSSpinLockUnLock(&loopsLock); 

return loop; 

} 

CFRunLoopRef CFRunLoopGetMain() { 

return _CFRunLoopGet(pthread_main_thread_np()); 

} 

CFRunLoopRef CFRunLoopGetCurrent() { 

return _CFRunLoopGet(pthread_self()); 

}  
```

#### OC获取当前RunLoop对象

` NSRunLoop* runloop = [NSRunLoop currentRunLoop]; ` 

#### C获取当前RunLoop对象 

` CFRunLoopRef* currentLoop = CFRunLoopGetCurrent(); ` 

#### 创建RunLoop 

1. 开一个子线程创建RunLoop，不是通过alloc或者init创建的，而是直接通过调用currentRunLoop方法创建的。 
   1. currentRunLoop这个方法是懒加载的在调用的时候会创建并保存 
   2. 在子线程中如果不主动获取RunLoop的话，子线程内部是不会创建RunLoop的 

#### RunLoop的运行原理 

![runloop1](/Users/zhoutianrong/MyDocument/myBook/photo/runloop1.png)

#### **5个相关的类** 

##### CFRunLoopRef 

通过currentRunLoop懒加载RunLoop之后[RunLoop run];来开启RunLoop但是开启之后会选择一个运行模式并且检查里面的timer|source是否为空若**为空**直接**退出** 

##### CFRunLoopModeRef（运行模式） 

一个RunLoop要想运行起来，它的内部必须要有一个非空mode，里面有source\observer\timer至少有一个 

RunLoop中的Mode 

1. kCFRunLoopDefaultMode：App默认的Mode，主线程通常在这个mode下运行 

2. UITrackingRunLoopMode：界面跟踪Mode，用于SrollView在追踪触摸动作，保证界面滑动不被其他Mode影响 

3. alizationRunLoopMode：启动App是进入的第一个Mode，之后不再使用 

4. ReceiveRunLoopMode：接受系统内部Mode 

5. oopCommonModes：占位Mode，不是真正的mode，同时表示默认Mode和追踪Mode 

##### CFRunLoopSourceRef （要处理的事件源） 

按照函数调用栈来划分 

1. source0 

2. source1 

##### CFRunloopTimerRef （Timer事件） 

监听RunLoop的状态需要添加到RunLoop中才有效果，会受到mode的影响 

##### CFRunLoopObserverRef （RunLoop的观察者） 

可以监听当前的RunLoop的状态的改变 

只能使用C语言的函数创建观察者 

```objc 
CFRunLoopObserverRef observer = CFRunLoopObserverCreateWithHandler(CFAllocatorGetDefault(), 0, YES, 0, ^(CFRunLoopObserverRef observer, CFRunLoopActivity activity) { 
/* 
typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) { 
kCFRunLoopEntry = (1UL << 0), 
kCFRunLoopBeforeTimers = (1UL << 1), 
kCFRunLoopBeforeSources = (1UL << 2), 
kCFRunLoopBeforeWaiting = (1UL << 5), 
kCFRunLoopAfterWaiting = (1UL << 6), 
kCFRunLoopExit = (1UL << 7), 
kCFRunLoopAllActivities = 0x0FFFFFFFU 
}; 
*/ 
}); 
```

创建监听 

第一个参数：分配存储空间-默认 

第二个参数：要监听的状态 

第三个参数：是否要持续监听 

第四个参数：0 

第五个参数：block回调 RunLoop状态改变的时候调用，传入 

`CFRunLoopAddObserver(CFRunLoopGetCurrent(), observer, kCFRunLoopDefaultMode); ` 

监听 

第一个参数：RunLoop对象 

第二个参数：观察者对象 

第三个参数：CFRunLoopModeRef对象 RunLoop的模式✂️只能使用c中的定义 

#### RunLoop的逻辑 

![runloop2](/Users/zhoutianrong/MyDocument/myBook/photo/runloop2.png)

#### 自动释放池 

在RunLoop启动的时候创建 

在RunLoop结束的时候销毁 

并且每次在RunLoop要休眠的时候就要清空一次 