# 数据持久化

iOS中的沙盒，每个APP下面都有类似的结构通过`NSHomeDirectory()`可以得到应用程序目录的路径,底下有

1. Documents 这个文件夹底下储存的是关键数据，在内存不够的情况下也会被保留，并且会被icloud备份
   1. 如何启用itunes分享在info.plist中添加"Application support iTunes file sharing" "YES"
   2. ![itunes](photo/itunes&#32;sharing.png)
2. Library 用来存储不想共享给用户的文件。需要时你可以创建自己的子目录。
   1. Preference目录：包含应用程序的偏好设置文件，通过NSUserDefault设置 **iTunes也会备份**
   2. Caches目录：用于存放应用程序专用的支持文件，保存引用程序再次启动中需要的信息
3. tmp目录：这个目录用于存放临时文件，保存下次启动不需要的信息

## plist

通常叫做plist文件，用于存储在程序中不经常修改、数据量小的数据，不支持自定义对象存储，支持数据存储的类型为：Array，Dictionary，String，Number，Data，Date，Boolean，通常用来存放接口名、城市名、银行名称、表情名等极少修改的数据

```objc
NSString *path = [[NSBundle mainBundle] pathForResource:@"ztrTest" ofType:@"plist"];
NSArray *array = [[NSArray alloc] initWithContentsOfFile:path];
NSLog(@"%@",array);
```

## NSUserDefaults

用于存储用户的偏好设置，同样适合于存储轻量级的用户数据，数据会自动保存在沙盒的Libarary/Preferences目录下，本质上就是一个**plist**文件，所以同样的不支持自定义对象存储。并且是个单例

```objc
[[NSUserDefaults standardUserDefaults] setObject:@"ztr" forKey:@"name"];
[[NSUserDefaults standardUserDefaults] synchronize];//is Planned to be Deprecated
NSLog(@"%@",[[NSUserDefaults standardUserDefaults] stringForKey:@"name"]);
```

## 归档序列化储存

归档可以直接将对象存储为文件，也可将文件直接解归档为对象，相对于plist文件与偏好设置数据的存储更加多样，支持自定义的对象存储，归档后的文件是加密的，也更加的安全，文件存储的位置可以自定义。

### 如果想自定义对象能归档序列化储存需要实现NSCoding或者NSSecureCoding(继承自NSCoding)协议方法

#### NSCoding

实现协议的方法

```objc
- (void)encodeWithCoder:(nonnull NSCoder *)aCoder {
    [aCoder encodeObject:_name forKey:@"name"];
    [aCoder encodeInteger:_age forKey:@"age"];
}

- (nullable instancetype)initWithCoder:(nonnull NSCoder *)aDecoder {
    if (self = [super init]) {
        self.name = [aDecoder decodeObjectForKey:@"name"];
        self.age = [aDecoder decodeIntegerForKey:@"age"];
    }
    return self;
}
```

#### NSSecureCoding

还需要设定重要的属性`+ (BOOL)supportsSecureCoding{return YES;}`

### 归档接档工具

```objc
+ (BOOL)archiveObject:(id)object prefix:(NSString *)prefix {
    if (object) {
        return NO;
    }
    NSError *error;
    // NSKeyedArchiver A coder that stores an object's data to an archive referenced by keys.
    // 会调用对象的encodeWithCoder 对象 -> NSData
    NSData *data = [NSKeyedArchiver archivedDataWithRootObject:object requiringSecureCoding:YES error:&error];
    if (error) {
        return NO;
    }
    [data writeToFile:[self getPathWithPrefix:prefix] atomically:YES];
    return YES;
}

+ (id)unarchiveClass:(Class)class prefix:(NSString *)prefix {
    NSError *error;
    // 从文件中读取二进制数据
    NSData *data = [[NSData alloc] initWithContentsOfFile:[self getPathWithPrefix:prefix]];
    // NSKeyedUnarchiver A decoder that restores data from an archive referenced by keys.
    // 会调用对象的initWithCoder方法 NSData -> 对象
    id content = [NSKeyedUnarchiver unarchivedObjectOfClass:class fromData:data error:&error];
    if (error) {
        return nil;
    }
    return content;
}

+ (NSString *)getPathWithPrefix:(NSString *)prefix {
    NSArray *pathArray = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    // NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory directory, NSSearchPathDomainMask domainMask, BOOL expandTilde);
    NSString *documentPath =pathArray.firstObject;
    NSString *filePathFolder = [documentPath stringsByAppendingPaths:@[@"archiveTemp"]].firstObject;
    if (![[NSFileManager defaultManager] fileExistsAtPath:filePathFolder]) { // 文件不存在 则创建文件夹
        [[NSFileManager defaultManager] createDirectoryAtPath:filePathFolder withIntermediateDirectories:YES attributes:nil error:nil];
    }
    NSString *path = [NSString stringWithFormat:@"%@/%@.archive",filePathFolder,prefix];
    return path;
}
```

## Core Data

## SQLite3

## FMDB

## realm
