# AFNetworking

## get&post

```objc
NSDictionary *dict = @{@"name":@"ztr"};

AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
[manager (GET|POST):(nonnull NSString *) parameters:dict progress:^(NSProgress * _Nonnull downloadProgress) {
//  get的为请求路径 但是不包含参数  parameters 为字典 存放参数 progress是下载进度
    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
//        成功回调 responseObject是响应体信息 已经是一个被解析好的JSON 通过task get到响应头信息
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
//        失败回调
    }];
```

## 下载

```objective-c
//    创建请求对象
    NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"https://www.baidu.com"]];
//    下载
    NSURLSessionDownloadTask *task = [manager downloadTaskWithRequest:request progress:^(NSProgress * _Nonnull downloadProgress) {
//        进度回调
        NSLog(@"%f",1.0*downloadProgress.completedUnitCount/downloadProgress.totalUnitCount);
    } destination:^NSURL * _Nonnull(NSURL * _Nonnull targetPath, NSURLResponse * _Nonnull response) {
//        在回调中告诉方法应该讲下载的文件保存到哪里
//        targetPath为临时储存的路径（tmp）response 响应头信息
        NSString *cache = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)lastObject];
        NSString *fullpath = [cache stringByAppendingPathComponent:response.suggestedFilename];
        return [NSURL fileURLWithPath:fullpath];
    } completionHandler:^(NSURLResponse * _Nonnull response, NSURL * _Nullable filePath, NSError * _Nullable error) {
//        filepath == fullpath
    }];
//    启动下载
    [task resume];
```

## 上传

```objc
//    通过POST上传
/*
 *第一参数 请求路径不带参数
 *第二参数 非文件参数可以为nil name……也是dictionary类型
 */
    [manager POST:@"https://www.baidu.com" parameters:nil constructingBodyWithBlock:^(id<AFMultipartFormData>  _Nonnull formData) {
//        处理要上传文件参数的
        NSData *data = [NSData data];
/*
 *第一参数 要上传的二进制数据
 *第二参数 具体参数 file
 *第三参数 文件名称
 *第四参数 文件二进制数据类型mimeType
 */
        [formData appendPartWithFileData:data name:@"file" fileName:@"123.png" mimeType:@"image/png"];
    } progress:^(NSProgress * _Nonnull uploadProgress) {
//        进度回调
        NSLog(@"%f",1.0*uploadProgress.completedUnitCount/uploadProgress.totalUnitCount);
    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
//        成功回调
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
//        失败回调
    }];
```

## 序列化

### JSON

默认就是JSON会自动序列化

### XML

设置manager的**responseSerializer**来达到解析成别的数据格式

`manager.responseSerializer = [AFXMLParserResponseSerializer serializer];`

并在成功回调中解析XML

```objective-c
NSXMLParser *parser = (NSXMLParser *)responseObject;

parser.delegate = self;

[parser parser];
```

### httpData

既不是XML也不是JSON就是这个

`manager.responseSerializer = [AFHTTPResponseSerializer serializer];`

#### 网络状态监听

```objc
    AFNetworkReachabilityManager *manager = [AFNetworkReachabilityManager sharedManager];
    [manager setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
//       网络状态改变的时候调用block块 并且当前的网络状态为参数
        /*
         typedef NS_ENUM(NSInteger, AFNetworkReachabilityStatus) {
            AFNetworkReachabilityStatusUnknown          = -1,
            AFNetworkReachabilityStatusNotReachable     = 0,没网
            AFNetworkReachabilityStatusReachableViaWWAN = 1,3G|4G
            AFNetworkReachabilityStatusReachableViaWiFi = 2,WIFi
        };
        */
    }];
//    开始监听
    [manager startMonitoring];
```
