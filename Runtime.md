[TOC]

# Runtime消息传递 

[objc msgSend]; 

1. 首先通过objc的isa指针找到他的class 

2. 在class的method list去找是否有msgSend 

3. 没有就去他的super class找 

4. 找到之后执行它的IMP 

5. 找到之后将key：method_name value：method_imp作为字典存储在objc_cache中 

注：IMP 函数指针 记录了方法的地址 

## 对象 

```objc 
struct objc_object { 

Class _Nonnull isa OBJC_ISA_AVAILABILITY; 

}; 
```

## 类 

```objc 
struct objc_class { 

Class _Nonnull isa OBJC_ISA_AVAILABILITY; 

#if !__OBJC2__ 

Class _Nullable super_class OBJC2_UNAVAILABLE; 

const char * _Nonnull name OBJC2_UNAVAILABLE; 

long version OBJC2_UNAVAILABLE; 

long info OBJC2_UNAVAILABLE; 

long instance_size OBJC2_UNAVAILABLE; 

struct objc_ivar_list * _Nullable ivars OBJC2_UNAVAILABLE; 

struct objc_method_list * _Nullable * _Nullable methodLists OBJC2_UNAVAILABLE; 

struct objc_cache * _Nonnull cache OBJC2_UNAVAILABLE; 

struct objc_protocol_list * _Nullable protocols OBJC2_UNAVAILABLE; 

#endif 

} OBJC2_UNAVAILABLE; 

```

## 方法 

```objc 
struct objc_method { 

SEL _Nonnull method_name OBJC2_UNAVAILABLE; 

char * _Nullable method_types OBJC2_UNAVAILABLE; 

IMP _Nonnull method_imp OBJC2_UNAVAILABLE; 

}  
```

## 方法列表 

```objc 
struct objc_method_list { 

struct objc_method_list * _Nullable obsolete OBJC2_UNAVAILABLE; 

int method_count OBJC2_UNAVAILABLE; 

#ifdef __LP64__ 

int space OBJC2_UNAVAILABLE; 

#endif 

/* variable length structure */ 

struct objc_method method_list[1] OBJC2_UNAVAILABLE; 

} OBJC2_UNAVAILABLE; 
```