[TOC]
# Runtime
OC之所以能成为一个优秀的动态特性语言全是因为Runtime
* OC的动态性体现
  1. 动态类型 id类型
  2. 动态绑定 SEL的动态绑定
  3. 动态加载 动态加载资源例如：runtime中添加一个类

## Runtime中的类
### 对象
```objc
//  对象的结构体
struct objc_object {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;
};
```
### 类
```objc
//  类的结构体 
struct objc_class {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;
#if !__OBJC2__
    Class _Nullable super_class                              OBJC2_UNAVAILABLE;     // super_class指向父类
    const char * _Nonnull name                               OBJC2_UNAVAILABLE;     // 类的名字
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list * _Nullable ivars                  OBJC2_UNAVAILABLE;     // 存放着所有的成员变量和属性信息
    struct objc_method_list * _Nullable * _Nullable methodLists OBJC2_UNAVAILABLE;  // 方法列表 存放着这个类所有的方法
    struct objc_cache * _Nonnull cache                       OBJC2_UNAVAILABLE;     // 记录每次使用类或者实例对象调用的方法（提升调用的速度）
    struct objc_protocol_list * _Nullable protocols          OBJC2_UNAVAILABLE;
#endif
} OBJC2_UNAVAILABLE;

// SEL指向objc_selector结构体
typedef struct objc_selector *SEL;
/*
1.OC中，使用@selector(“方法名字符串”)
2.OC中，使用NSSelectorFromString(“方法名字符串”)
3.Runtime方法，使用sel_registerName(“方法名字符串”)
*/

// ivar的结构体
struct objc_ivar {
    char * _Nullable ivar_name                               OBJC2_UNAVAILABLE;     // 名字
    char * _Nullable ivar_type                               OBJC2_UNAVAILABLE;     // 类型
    int ivar_offset                                          OBJC2_UNAVAILABLE;
#ifdef __LP64__
    int space                                                OBJC2_UNAVAILABLE;
#endif
} 

// 方法的结构体
struct objc_method {
    SEL _Nonnull method_name                                 OBJC2_UNAVAILABLE;     // 名字类型
    char * _Nullable method_types                            OBJC2_UNAVAILABLE;     // 指向存储方法的参数类型和返回值类型
    IMP _Nonnull method_imp                                  OBJC2_UNAVAILABLE;     // 指向的方法imp指针
} 
```

### NSObject中的运行时
由于根类NSObject的存在，在这里面也有很多地方运用了runtime。
```objc
-description：//返回当前类的描述信息
-class //方法返回对象的类；
-isKindOfClass: 和 -isMemberOfClass:  //检查对象是否存在于指定的类的继承体系中
-respondsToSelector:    //检查对象能否响应指定的消息；
-conformsToProtocol:    //检查对象是否实现了指定协议类的方法；
-methodForSelector:     //返回指定方法实现的地址。
```

## Runtime中的消息传递 
在OC中都是这样的[]表示的方法`[objc msgSend];`,每次都是一次运行时方法发送的过程。
1. 编译器将这句话转化成`objc_msgSend(reseiver,selector)`
2. 首先通过objc的isa指针找到他的class 
3. 在class的method list去找是否有msgSend 
4. 没有就去他的super class找 
5. 找到之后执行它的IMP 
6. 找到之后将key：method_name value：method_imp作为字典存储在objc_cache中 
注：IMP 函数指针 记录了方法的地址 

## 消息转发
经过消息传递之后，在cache缓存中以及方法列表中找不到方法的时候，启动消息转发。
### 动态方法解析
```objc
//类方法未找到时调起，可于此添加类方法实现
+ (BOOL)resolveClassMethod:(SEL)sel;
//实例方法未找到时调起，可于此添加实例方法实现
+ (BOOL)resolveInstanceMethod:(SEL)sel;

//Runtime方法：
/**
 运行时方法：向指定类中添加特定方法实现的操作
 @param cls 被添加方法的类
 @param name selector方法名
 @param imp 指向实现方法的函数指针
 @param types imp函数实现的返回值与参数类型
 @return 添加方法是否成功
 */
BOOL class_addMethod(Class _Nullable cls,
                     SEL _Nonnull name,
                     IMP _Nonnull imp,
                     const char * _Nullable types)
//例如：
+ (BOOL)resolveInstanceMethod:(SEL)sel{
    if(sel == @selector(singSong:)){
        class_addMethod([self class], sel, class_getMethodImplementation([self class], @selector(zs_singSong:)), "v@");
        return YES;
    }
    return [super resolveInstanceMethod:sel];
}
```
### 消息接受者重定向
```objc
//重定向(类方法|实例方法)的消息接收者，返回一个(类对象|实例对象)
+|- (id)forwardingTargetForSelector:(SEL)aSelector;

//例如：在viewDidLoad中有调用一个在当前viewController中没有的SEL，但是其中的一个类中恰好又实现了这个SEL，这个时候可以使用消息重定向
//重定向一个类方法（+takeExam:)(-learnKnowledge:)
+ (id)forwardingTargetForSelector:(SEL)aSelector{
    if (aSelector == @selector(takeExam:)) {
        return [Student class];// self.student
    }
    return [super forwardingTargetForSelector:aSelector];
}
```
如果这个法子ok的话是成功返回，不OK就去父类中找
### 消息重定向
当上面两个方法不行，没有定义……会来到这最后一步->Runtime系统会通知该对象，给予此次消息最后一次寻找IMP的机会
`- (void)forwardInvocation:(NSInvocation *)anInvocation；`
原理：其实每个对象都从NSObject类中继承了forwardInvocation：方法，但是NSObject中的这个方法只是简单的调用了doesNotRecongnizeSelector:方法，提示我们错误。所以我们可以重写这个方法：对不能处理的消息做一些默认处理，也可以将消息转发给其他对象来处理，而不抛出错误。而**anInvocation**是**forwardInvocation**唯一参数，它封装了原始的消息和消息参数。正是因为它，我们还不得不重写另一个函数：`methodSignatureForSelector`。这是因为在forwardInvocation: 消息发送前，Runtime系统会向对象发送methodSignatureForSelector消息，并取到返回的方法签名用于生成NSInvocation对象。

```objc
- (void)forwardInvocation:(NSInvocation *)anInvocation{
    //1.从anInvocation中获取消息
    SEL sel = anInvocation.selector;
    //2.判断Student方法是否可以响应应sel
    if ([self.student respondsToSelector:sel]) {
        //2.1若可以响应，则将消息转发给其他对象处理
        [anInvocation invokeWithTarget:self.student];
    }else{
        //2.2若仍然无法响应，则报错：找不到响应方法
        [self doesNotRecognizeSelector:sel];
    }
}
//需要从这个方法中获取的信息来创建NSInvocation对象，因此我们必须重写这个方法，为给定的selector提供一个合适的方法签名。
- (NSMethodSignature*)methodSignatureForSelector:(SEL)aSelector{
    NSMethodSignature *methodSignature = [super methodSignatureForSelector:aSelector];
    if (!methodSignature) {
        methodSignature = [NSMethodSignature signatureWithObjCTypes:"v@:*"];
    }
    return methodSignature;
}
```
## Runtime应用
### 拦截替换系统方法
先建立UIFont的一个分类，在分类里自定义你的方法，再在`+(void)load;`中替换系统方法和你的方法(在类初始加载时自动被调用,load方法按照父类到子类,类自身到Category的顺序被调用)
```objc
+ (void)load{
    //获取系统方法地址
    Method sytemMethod = class_getClassMethod([UIFont class], @selector(systemFontOfSize:));
    //获取自定义方法地址
    Method customMethod = class_getClassMethod([UIFont class], @selector(zs_systemFontOfSize:));
    //交换两个方法的实现
    method_exchangeImplementations(sytemMethod, customMethod);
}
```
### 类添加新属性
```objc
/**
 1.给对象设置关联属性
 @param object 需要设置关联属性的对象，即给哪个对象关联属性
 @param key 关联属性对应的key，可通过key获取这个属性，
 @param value 给关联属性设置的值
 @param policy 关联属性的存储策略(对应Property属性中的assign,copy，retain等)
 OBJC_ASSOCIATION_ASSIGN             @property(assign)。
 OBJC_ASSOCIATION_RETAIN_NONATOMIC   @property(strong, nonatomic)。
 OBJC_ASSOCIATION_COPY_NONATOMIC     @property(copy, nonatomic)。
 OBJC_ASSOCIATION_RETAIN             @property(strong,atomic)。
 OBJC_ASSOCIATION_COPY               @property(copy, atomic)。
 */
void objc_setAssociatedObject(id _Nonnull object,
                              const void * _Nonnull key,
                              id _Nullable value,
                              objc_AssociationPolicy policy)
/**
 2.通过key获取关联的属性
 @param object 从哪个对象中获取关联属性
 @param key 关联属性对应的key
 @return 返回关联属性的值
 */
id _Nullable objc_getAssociatedObject(id _Nonnull object,
                                      const void * _Nonnull key)
/**
 3.移除对象所关联的属性
 @param object 移除某个对象的所有关联属性
 */
void objc_removeAssociatedObjects(id _Nonnull object)
```