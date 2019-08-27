# NSThread

## 创建

1. `NSThread* thread = [[NSThread alloc]initWithTarget:self selector:@selector(run) object:nil ];`
   1. [thread start];需要通过start启动，但是可以获取线程对象
2. `[NSThread detachNewThreadSelector:@selector(run) toTarget:self withObject:nil];`
   1. 分离出一条子线程 创建并自动启动
3. `[self performSelectorInBackground:@selector(run) withObject:nil];`
   1. 开启后台线程 使用NSObject方法创建并自动启动
   2. 不是很安全

## 设置属性

1. 设置名称
   1. thread.name = @"线程01";
   2. [thread setName:@"线程02"];
2. **设置线程优先级** threadPriority 0.0~1.0
   1. 优先级越高被调用次数越高

## 进程之间通信

```objective-c
// 给主线程传值
[self performSelectorOnMainThread:(nonnull SEL)
 withObject:(nullable id) waitUntilDone:(BOOL)];
// 不推荐使用
[self performSelector:(nonnull SEL) onThread:(nonnull NSThread *)
 withObject:(nullable id) waitUntilDone:(BOOL)];
```

```objective-c
//  简单方法 就是直接用属性自带的setter方法
[self.webImage performSelector:@selector(setImage:)
 onThread:[NSThread mainThread] withObject:image waitUntilDone:YES];
/*
1. SEL 方法选择器
2. 调用函数要传递的参数
3. 是否等待该方法执行完在才继续往下只想 YES等待
*/
```

## 线程的生命周期

> 当线程内部的任务执行完毕线程自动释放
