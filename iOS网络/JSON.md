# JSON数据的解析

通过原生的类NSJSONSerialization

JSON----->OC对象 反序列化

```objc
/*
*第一参数：JSON数据
*第二参数：解析数据时候的额外选项默认0 == kNilOptions
* NSJSONReadingMutableContainers = (1UL << 0), 得到的对象是可变的
* NSJSONReadingMutableLeaves = (1UL << 1), 得到的对象中字符串是可变的
* NSJSONReadingAllowFragments = (1UL << 2) ⚠️当返回的既不是数组也不是字典的时候(null)的时候
* [NSNull null];返回的是一个空对象，可以在字典中使用而nil则不行
*第三参数：错误信息
*/
NSDictionary *dict = [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:nil];
```

OC对象------->JSON  序列化处理

OC对象应该遵从

- 最外层必须是一个数组或者字典Top level object is an NSArray or NSDictionary
- 所有的对象都必须是NSString, NSNumber, NSArray, NSDictionary, or NSNull
- 所有的key都必须是NSString
- NSNumbers不能是NaN或者无穷大

BOOL num = [NSJSONSerialization isValidJSONObject:@"he"];  

序列化过程

```objc
/*
 *第一参数：OC对象 ⚠️部分不能转化
 *第二参数：选项
 *返回值：二进制数据
 */
NSData *MEdata = [NSJSONSerialization dataWithJSONObject:dict options:kNilOptions error:nil];  
```

## 保存本地JSON文件

```objc
/*
 *保存本地JSON文件
 *需要将OC对象转化成JSONData再写文件
 *NSJSONWritingPrettyPrinted = (1UL << 0) 排版美化结构
 */
NSData *jsonData = [NSJSONSerialization dataWithJSONObject:data options:NSJSONWritingPrettyPrinted error:nil];

// 写文件
[jsonData writeToFile:@"文件名" atomically:YES];
```

## 加载本地JSON文件

```objc
// 1. 加载JSON文件
NSData *data = [NSData dataWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"文件名.json" ofType:nil]];
// 2. JSON->OC
```

## 复杂JSON数据的解析

1. 写文件plist

2. 直接在线格式化

### 字典转模型

1. 声明模型类
2. 初始化方法中设置模型
3. 使用第三方框架
   1. MJExtension

### 在自定义框架的时候注意点

1. 侵入性
   1. 使用之后是否容易替换成另一框架
2. 易用性
   1. 是否容易使用
