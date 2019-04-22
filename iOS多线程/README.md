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

