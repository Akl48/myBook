# OC基本语法

## 协议

协议的存在相当于C++中抽象类的存在。在协议中只声明方法，而在遵守他的协议的类中实现它的方法。

### 声明一个协议

```objc
@protocol watch <NSObject>
//@optional 可选实现
//@required 必须实现
- (void) watchdoor;

@end
```

### 遵守协议

```objc
@interface dog : NSObject <watch>
  
@end
  
@implementation dog

- (void)watchdoor{
    NSLog(@"dog watchdoor");
}

@end
```

### 代理

驾校的代理是学生 驾校是被代理方 学生是代理

```objc
@protocol buything <NSObject>
-(void)buy;
@end
  
@interface Boss : NSObject 
@property(nonatomic,weak)id <buything> delegate;//必须为weak 避免循环引用
@end
```

#### Foundation

1. `NSString(NSMutableString)`
   1. 字符串常量@""通常经常使用NSString
2. `NSArray(NSMutableArray)`
   1. 数组类型 只能存放对象
3. `NSDictionary(NSMutableDictionary)`
   1. 不能放nil
4. NSURL
   1. 专门用来保存资源路径
      * http:// 开头的是网页路径的写法.
      * file:// 开头的是本地磁盘的路径
      * ftp:// 开头的是ftp文件资源的路径
