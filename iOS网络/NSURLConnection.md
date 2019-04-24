[TOC]

## NSURLConnection

1. NSURL 请求地址 

2. NSURLRequest 
   1. 对象就包含一个请求 
   2. NSMutableURLRequest 
      1. 继承于NSURLRequest的子类 
      2. 用于可变的请求----一般用于改写POST请求 

3. NSURLConnection 
   1. 负责发送请求，简历客户端和服务器的链接 

#### 发送get请求 

> 步骤 
>
> （1）设置请求路径 
>
> （2）创建请求对象（默认是GET请求，且已经默认包含了请求头） 
>
> （3）使用NSURLSession sendsync方法发送网络请求 
>
> （4）接收到服务器的响应后，解析响应体 

```objc 
// 1. 确定请求路径(get:基础路径+参数) 

NSURL* url = [NSURL URLWithString:@"https://www.baidu.com"]; 

// 2. 创建请求对象 请求对象默认生成请求头 

NSURLRequest* request = [NSURLRequest requestWithURL:url]; 

// 3. 使用NSURLConnection发送同步请求 

/* 
 *第一参数：请求对象 
 *第二参数：NSURLResponse 响应头地址需要带一个--& 
 *第三参数：错误信息 
 *返回值：返回响应体NSData 
 */ 
// 响应头可用NSLog打印出来 
NSURLResponse* response = nil; 

NSData* data = [NSURLConnection sendSynchronousRequest:request returningResponse:&response error:nil]; 

// 4. 解析数据 二进制->字符串 

NSLog(@"%@",[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding]); 

// 3. 使用NSURLConnection发送异步请求 

/* 
 *第一参数：请求对象 
 *第二参数：执行对列（主对列|自定义对列） 
 *				主对列：请求在主线程完成 
 *				自定义对列：请求在子线程完成 
 *第三参数：completionHandler完成回调（成功|失败）block块 
 *        参数一：响应头 
 *        参数二：返回二进制数据 
 *        参数三：错误信息 
*/ 
[NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse * _Nullable response, NSData * _Nullable data, NSError * _Nullable connectionError) { 
// 解析数据 
NSLog(@"%@",[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding]); 
}]; 
```

##### 通过代理实现异步请求 

<NSURLConnectionDataDelegate> 

*  系统自动发送请求 ` [[NSURLConnection alloc]initWithRequest:request delegate:self]; `

* 手动通过start启动发送请求 ` [[NSURLConnection alloc]initWithRequest:request delegate:self startImmediately:NO];`

```objc 
/* 
1.当接收到服务器响应的时候调用 
第一个参数connection：监听的是哪个NSURLConnection对象 
第二个参数response：接收到的服务器返回的响应头信息 
*/ 
- (void)connection:(nonnull NSURLConnection *)connection didReceiveResponse:(nonnull NSURLResponse *)response 

/* 
2.当接收到数据的时候调用，该方法会被调用多次 
第一个参数connection：监听的是哪个NSURLConnection对象 
第二个参数data：本次接收到的服务端返回的二进制数据（可能是片段） 
⚠️需要拼接[对象 append:data]; 
*/ 
- (void)connection:(nonnull NSURLConnection *)connection didReceiveData:(nonnull NSData *)data 

/* 
3.当服务端返回的数据接收完毕之后会调用 
通常在该方法中解析服务器返回的数据 
*/ 
-(void)connectionDidFinishLoading:(nonnull NSURLConnection *)connection 
  
/*4.当请求错误的时候调用（比如请求超时） 
第一个参数connection：NSURLConnection对象 
第二个参数：网络请求的错误信息，如果请求失败，则error有值 
*/ 
- (void)connection:(nonnull NSURLConnection *)connection didFailWithError:(nonnull NSError *)error 
```

#### 发送Post请求 

```objc 
// 创建Post请求对象:设置为可变请求 
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url]; 

// 设置请求头信息(key-value) 
[request setValue:<(nullable NSString *)> forHTTPHeaderField:<(nonnull NSString *)>]; 

// 设置请求超时时间 
request.timeoutInterval = 3.0; 

// 设置HTTP的POST方法 
request.HTTPMethod = @"POST"; 

// 设置请求体参数(二进制的数据) 
request.HTTPBody = [@"wd=hello" dataUsingEncoding:NSUTF8StringEncoding]; 

/* 
发送异步请求 
根据对列的位子的不同，回调completionHandler执行的位子不同 
*/ 

[NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse * _Nullable response, NSData * _Nullable data, NSError * _Nullable connectionError) { 

// 先判断请求是否失败 
if(connectionError){ 
// 失败了 
} else { 
NSLog(@"%@",[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding]); 
} 
}]; 
```

##### 在字符串中有中文需要转码 

只有在get方法中需要转码 

``` objc
NSString *str = @"ztr"; 
str = [str stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]; 
```

