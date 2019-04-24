[TOC]

### NSURLSession 

相对于NSURLConnection性能更好能开启多条线程 

#### Session 

Session和Connection的区别 

1. Session支持HTTP2.0 

2. 可以直接将数据下载到磁盘而Connection只能下载到内存 

3. 支持后台下载|上传 

4. 同一个Session发送多个请求，只需要一次连接（复用了TCP） 

5. 提供了全局的Session可以统一调度 

6. 下载的时候是多线程异步的效率更高 

##### 发送get请求 

```objc 
NSURL *url = [NSURL URLWithString:@"https://www.baidu.com"]; 

// 可省略 
NSURLRequest *request = [NSURLRequest requestWithURL:url]; 

// 通过单例模式创建session 
NSURLSession *session = [NSURLSession sharedSession]; 

// 根据会话对象创建Task(处于挂起的状态) 
NSURLSessionDataTask *task = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) { 

	NSLog(@"%@",[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding]); 
  
}]; 

// 执行请求任务 
[task resume]; 
```

##### 发送post请求 

```objc 
// 声明一个可变的请求 
NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:url]; 

// 更改默认GET方法为POST方法 
request.HTTPMethod = @"POST"; 

// 设置请求体 
request.HTTPBody = [@"&wd=hello" dataUsingEncoding:NSUTF8StringEncoding]; 

// 声明一个单例的会话 
NSURLSession *session = [NSURLSession sharedSession]; 

// 声明会话的Task 
NSURLSessionTask *task = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) { 

if(error){ 
	NSLog(@"%@",error); 
	return; 
} else { 
	NSLog(@"%@",[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding]); 
} 
}]; 

// 恢复挂起的task 
[task resume]; 
```

⚠️默认是在子线程中调用的completionHandler:^(){}; 

#### 通过NSURLSession代理NSURLSessionDataDelegate设置 

还需声明一个NSMutableData对象来接受片段数据通过[append:data];方法 

```objc 
/* 
自定义会话对象 设置代理 
	第一参数：配置信息(设置请求的功能类似于NSURLRequest) defaultSessionConfiguration 默认配置 
	第二参数：设置代理 
	第三参数：代理的对列决定在那个线程中使用(自定义的对列是子线程，默认也是子线程) 
*/ 
NSURLSession *session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[NSOperationQueue mainQueue]];  
```

* 三个主要的代理 

```objc 
//接收到服务器消息的时候会调用，可能会调用多次 
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data{ 

	[Mutabledata append:data];//拼接片段数据 
} 

//接收到服务器响应的时候会调用 
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionConfiguration *)dataTask didReceiveResponse:(nonnull NSURLResponse *)response completionHandler:(nonnull void (^)(NSURLSessionResponseDisposition))completionHandler{ 

// 通过调用block块来告诉如何处理系统任务 
	completionHandler(NSURLSessionResponseAllow); //允许接受服务器消息 

} 

//请求完成或者是失败的时候调用 
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error{ 

} 
```