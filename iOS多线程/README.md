[TOC]
# iOS多线程

## 主线程 

1. 耗时操作不能放在主线程中 

2. **UI相关的操作必须在主线程中** 

3. 几乎所有方法都在主线程中 

### 判断是否是主线程 

1. 打印出来的 number == 1 就是主线程 

2. 类方法 判断当前线程是否是主线程 
   * [NSThread isMainThread] 

3. 对象方法 判断给定线程是否是主线程 
   * [childThread isMainThread]; 

## 多线程的实现 

1. pThread C 由程序员管理 

2. NSThread OC 由程序员管理 

3. **GCD** C 自动管理 **经常使用** 

4. **NSOperation** OC 对于GCD的封装 经常使用 

## 线程的状态 

1. 启动 
   1. `- (void)start;` 

2. 睡眠/暂停 
   1. `+ (void)sleepForTimeInterval:; `

   2. `+(void)sleepUntilDate:[NSDate ..]; `休眠一个NSDate对象 
3.结束 

   1. `+ (void)exit;` 
## 多线程的安全隐患 

多个线程访问同一块资源会发生数据安全问题 

### 解决方法 加互斥（同步）锁 

```objc 
@synchronized( 锁对象 ){ 

要加锁的代码段 

} 
```

* 锁对象建议直接使用self 

* 锁对象是全局唯一变量 

* 加多把锁是无效的 

* 注意加锁的位置 

* 注意加锁的时期（资源抢夺的时候） 

* 加锁会影响性能消耗大量CPU资源 

#### 线程同步|线程异步（进程并行） 

1. **多条线程按顺序执行**有一个排队的过程 

2. **多条线程并行执行** 

#### nonatomic|atomic 

1. atomic是线程安全的（不写默认就是atomic） 

   1. 内部会对setter方法加锁 

   2. 会消耗CPU的资源 

   3. 不一定是完全安全的 

      1. 不能保证数据的可靠性 仅仅实在读和写加上了同步锁，但是如果a写入之后b再写入，a再读取数据---得到的数据是被b修改过的数据不可靠 

2. nonatomic是线程不安全的 

   1. 由于性能好多半使用nonatomic 

   2. 尽量避免多线程抢夺资源 

#### 下载图片 

```objc 

// 1.确认图片地址URL 

NSURL* url = [[NSURL alloc]initWithString:@"https://img1.gamersky.com/image2019/02/20190223_ddw_red_459_4/gamersky_02origin_03_201922315321A4.jpg"]; 

// 2.下载图片二进制数据 

NSData* imageData = [NSData dataWithContentsOfURL:url]; 

// 3.转化图片格式 

UIImage* image = [UIImage imageWithData:imageData]; 

// 4.显示图片 

self.webImage.image = image; 

```


##### 图片内存缓存 

1. cell中首先先检查字典中有没有图片 

2. 如果有就直接使用 

3. 如果没有就缓存下来再放到字典中（内存） 

> 内存缓存->磁盘缓存 

##### 图片的磁盘缓存 

1. 在显示图片之前先检查是否有内存缓存 

   1. 如果有则直接使用 

   2. 如果没有内存缓存，再检查磁盘缓存 

   3. 如果有磁盘缓存，直接使用+缓存到内存中（方便下次使用） 

2. 如果没有磁盘缓存，下载+保存到内存+保存到磁盘缓存 

   1. 如果保存到磁盘中的，不能直接存image要存NSData 

iOS中的磁盘文件 

* Lib 

* cache：一般来存图片 

* 偏好设置 

* Doc 

* tmp（临时路径容易删除） 

###### 保存到cache中 

1. 找到cache的路径 

1. `NSString* cache = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)lastObject]; `

2. 找到文件名 

   1. 不能直接使用URL 

   2. NSString* name = [path lastPathComponent];获取最后一个路径名 

3. 拼接成一个NSString 
   1. `NSString* fullname = [cache stringByAppendingPathComponent:name]; `

4. 磁盘写入 
   1. `[imageData writeToFile:fullname atomically:YES]; `

##### 时间比较 

```objc 

NSDate* start = [NSDate date]; 

NSData* imageData = [NSData dataWithContentsOfURL:url]; 

// 3.转化图片格式 

NSDate* end = [NSDate date]; 

NSLog(@"%f",[end timeIntervalSinceDate:start]); 

```

#### 合成图片 

1. 开启上下文 

	1. UIGraphicsBeginImageContext(CGSizeMake(300,300)); 

	2. 画图1、2 

		1. [image1 drawInRectCG:RectMake(0,0,150,300)]; 

		2. [image2 drawInRectCG:RectMake(150,0,150,300)]; 

3. 根据上下文得到图片 

	1. UIImage *image = UIGraphicsGetImageFromCurrentImageContext(); 

4. 关闭上下文 

1. UIGraphicsEndImageContext(); 

## 单例模式 

创建出来的对象一直是同一个，尝试重写alloc方法，保证永远只有一个对象控件 

```objc 

static NSObject* instance; 

+(instancetype)allocWithZone:(struct NSZone*)zone{ 

@synchronized(self){ 

if(_instance == nil) 

{ 

_instance = [super allocWithZone]; 

} 

}//保证线程安全 

return _instance; 

} 

```

此方法是alloc实际会调用的分配空间的方法 

```objc 

static dispatch_once_t onceToken; 

dispatch_once(&onceToken,^{ 

_instance = [super allocWithZone]; 

}); 

```

更简单，instance只会执行一次 