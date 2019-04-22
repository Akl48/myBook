# NSOperation

[TOC]

### NSOperation（操作队列） 

NSOperation是个抽象类只能使用它的子类**NSInvocationOperation**&**NSBlockOperation**

操作 + 对列 

① 创建对列 

② 封装操作 

③ 将操作添加到对列 

#### NSInvcationOperation 

```objc 
NSInvocationOperation* op1 = [[NSInvocationOperation alloc]initWithTarget:self selector:@selector(run) object:nil]; 
[start op1]; 
```
开启操作是不会开启新的线程的 

#### **NSBlockOperation** 

```objc 
NSBlockOperation* block = [NSBlockOperation blockOperationWithBlock:^{ 

NSLog(@"%@",[NSThread currentThread]); 

}]; 

// 追加任务 当一个操作中的任务数>1的时候，就会开启子线程和当前线程一起执行任务 

// 追加任务中的block都是任务 其他是操作 

[block addExecutionBlock:^{ 

}]; 
```

#### 操作对列之间的通信 

找到主对列 在主队列中显示图片 

`[[NSOperationQueue mainQueue]addOperationWithBlock:^{}]; ` 

剩下的操作直接在block块中实现 

#### 操作队列 

##### 操作依赖 

* 设置操作依赖之后可以有顺序关系 

* 但是不能形成循环依赖 

* 还可以设置成跨对列依赖 

* `op2.addDependency(op1);`任务2添加任务1的依赖 

##### 操作监听 

`.completionBlock = ^{}; `

在block块里实现监听操作 

##### 自定义的队列 

并发队列，但是可以控制让他变成串行队列 

`[[NSOperationQueue alloc]init]; `

设置最大并发数来控制线程数量 

> maxConcurrentOperationCount //设置并发数 

**对于操作中的任务大于1的时候，控制最大并发数是没用的** 

##### 主对列 

串行队列，和主线程相关（凡是放在主对列的任务都在主线程中执行） 

`[NSOperationQueue mainQueue]; ` 

#### 添加队列 

```objc 
[[NSOperationQueue mainQueue]addOperation:block]; 

[[NSOperationQueue mainQueue]addOperationWithBlock:^{ 

NSLog(@"简单方法"); 

}];  
```

在添加之后在里面调用[block start]方法; 

#### 暂停对列 

`- (void)setSuspended:(BOOL);`只能暂停当前操作之后的操作 是不能停下的 

#### 取消对列 

`-(void)cancelAllOperations;`只能去向处于等待的操作 正在进行的是不能取消的 

会调用当前对列的所有cancel方法取消 

#### 自定义操作 

* 重写自定义操作中的main方法 addOperation->start->main 

主要好处 

1. 代码的服用 

2. 封装性更好 

3. 建议每次耗时操作之后判断是否取消 

##### 重写NSThread 

也是自定义main方法 