# MJExtension

## runtime获取模型所有属性 转换成MJProperty属性表

```objc
// 1.获得所有的成员变量
    unsigned int outCount = 0;
    objc_property_t *properties = class_copyPropertyList(c, &outCount);
// 2.遍历每一个成员变量
    for (unsigned int i = 0; i<outCount; i++) {
// 缓存 通过runtime设置关联
        MJProperty *property = [MJProperty cachedPropertyWithProperty:properties[i]];
// 过滤掉Foundation框架类里面的属性
        if ([MJFoundation isClassFromFoundation:property.srcClass]) continue;
// 过滤掉`hash`, `superclass`, `description`, `debugDescription`
        if ([MJFoundation isFromNSObjectProtocolProperty:property.name]) continue;
        property.srcClass = c;
        [property setOriginKey:[self mj_propertyKey:property.name] forClass:self];
        [property setObjectClassInArray:[self mj_propertyObjectClassInArray:property.name] forClass:self];
        [cachedProperties addObject:property];
    }
// 3.释放内存
    free(properties);
```

```objc
- (void)setProperty:(objc_property_t)property
{
    _property = property;
    MJExtensionAssertParamNotNil(property);
// 1.属性名
    _name = @(property_getName(property));
// 2.成员类型
    NSString *attrs = @(property_getAttributes(property));
// 根据属性获取他的相关属性特性（属性名称、属性编码类型、原子类型/非原子类型）attributes
    NSUInteger dotLoc = [attrs rangeOfString:@","].location;
    NSString *code = nil;
    NSUInteger loc = 1;
    if (dotLoc == NSNotFound) { // 没有,
        code = [attrs substringFromIndex:loc];
    } else {
        code = [attrs substringWithRange:NSMakeRange(loc, dotLoc - loc)];
    }
    _type = [MJPropertyType cachedTypeWithCode:code];
}
```

## 根据MJProperty中的属性类型，对属性进行处理，获取属性值

```objc
- (instancetype)mj_setKeyValues:(id)keyValues context:(NSManagedObjectContext *)context
{
    // 获得JSON对象
    keyValues = [keyValues mj_JSONObject];

    MJExtensionAssertError([keyValues isKindOfClass:[NSDictionary class]], self, [self class], @"keyValues参数不是一个字典");

    Class clazz = [self class];
    NSArray *allowedPropertyNames = [clazz mj_totalAllowedPropertyNames];
    NSArray *ignoredPropertyNames = [clazz mj_totalIgnoredPropertyNames];

    //通过封装的方法回调一个通过运行时编写的，用于返回属性列表的方法。
    [clazz mj_enumerateProperties:^(MJProperty *property, BOOL *stop) {
        @try {
            // 0.检测是否被忽略
            if (allowedPropertyNames.count && ![allowedPropertyNames containsObject:property.name]) return;
            if ([ignoredPropertyNames containsObject:property.name]) return;

            // 1.取出属性值
            id value;
            NSArray *propertyKeyses = [property propertyKeysForClass:clazz];
            for (NSArray *propertyKeys in propertyKeyses) {
                value = keyValues;
                for (MJPropertyKey *propertyKey in propertyKeys) {
                    value = [propertyKey valueInObject:value];
                }
                if (value) break;
            }

            // 值的过滤
            id newValue = [clazz mj_getNewValueFromObject:self oldValue:value property:property];
            if (newValue != value) { // 有过滤后的新值
                [property setValue:newValue forObject:self];
                return;
            }

            // 如果没有值，就直接返回
            if (!value || value == [NSNull null]) return;

            // 2.复杂处理
            MJPropertyType *type = property.type;
            Class propertyClass = type.typeClass;
            Class objectClass = [property objectClassInArrayForClass:[self class]];

            // 不可变 -> 可变处理
            if (propertyClass == [NSMutableArray class] && [value isKindOfClass:[NSArray class]]) {
                value = [NSMutableArray arrayWithArray:value];
            } else if (propertyClass == [NSMutableDictionary class] && [value isKindOfClass:[NSDictionary class]]) {
                value = [NSMutableDictionary dictionaryWithDictionary:value];
            } else if (propertyClass == [NSMutableString class] && [value isKindOfClass:[NSString class]]) {
                value = [NSMutableString stringWithString:value];
            } else if (propertyClass == [NSMutableData class] && [value isKindOfClass:[NSData class]]) {
                value = [NSMutableData dataWithData:value];
            }

            if (!type.isFromFoundation && propertyClass) { // 模型属性
                value = [propertyClass mj_objectWithKeyValues:value context:context];
            } else if (objectClass) {
                if (objectClass == [NSURL class] && [value isKindOfClass:[NSArray class]]) {
                    // string array -> url array
                    NSMutableArray *urlArray = [NSMutableArray array];
                    for (NSString *string in value) {
                        if (![string isKindOfClass:[NSString class]]) continue;
                        [urlArray addObject:string.mj_url];
                    }
                    value = urlArray;
                } else { // 字典数组-->模型数组
                    value = [objectClass mj_objectArrayWithKeyValuesArray:value context:context];
                }
            } else {
                if (propertyClass == [NSString class]) {
                    if ([value isKindOfClass:[NSNumber class]]) {
                        // NSNumber -> NSString
                        value = [value description];
                    } else if ([value isKindOfClass:[NSURL class]]) {
                        // NSURL -> NSString
                        value = [value absoluteString];
                    }
                } else if ([value isKindOfClass:[NSString class]]) {
                    if (propertyClass == [NSURL class]) {
                        // NSString -> NSURL
                        // 字符串转码
                        value = [value mj_url];
                    } else if (type.isNumberType) {
                        NSString *oldValue = value;

                        // NSString -> NSNumber
                        if (type.typeClass == [NSDecimalNumber class]) {
                            value = [NSDecimalNumber decimalNumberWithString:oldValue];
                        } else {
                            value = [numberFormatter_ numberFromString:oldValue];
                        }

                        // 如果是BOOL
                        if (type.isBoolType) {
                            // 字符串转BOOL（字符串没有charValue方法）
                            // 系统会调用字符串的charValue转为BOOL类型
                            NSString *lower = [oldValue lowercaseString];
                            if ([lower isEqualToString:@"yes"] || [lower isEqualToString:@"true"]) {
                                value = @YES;
                            } else if ([lower isEqualToString:@"no"] || [lower isEqualToString:@"false"]) {
                                value = @NO;
                            }
                        }
                    }
                } else if ([value isKindOfClass:[NSNumber class]] && propertyClass == [NSDecimalNumber class]){
                    // 过滤 NSDecimalNumber类型
                    if (![value isKindOfClass:[NSDecimalNumber class]]) {
                        value = [NSDecimalNumber decimalNumberWithDecimal:[((NSNumber *)value) decimalValue]];
                    }
                }

                // value和property类型不匹配
                if (propertyClass && ![value isKindOfClass:propertyClass]) {
                    value = nil;
                }
            }

            // 3.赋值
            [property setValue:value forObject:self];
        } @catch (NSException *exception) {
            MJExtensionBuildError([self class], exception.reason);
            MJExtensionLog(@"%@", exception);
        }
    }];

    // 转换完毕
    if ([self respondsToSelector:@selector(mj_keyValuesDidFinishConvertingToObject)]) {
        [self mj_keyValuesDidFinishConvertingToObject];
    }
    if ([self respondsToSelector:@selector(mj_keyValuesDidFinishConvertingToObject:)]) {
        [self mj_keyValuesDidFinishConvertingToObject:keyValues];
    }
    return self;
}
```

## 通过KVC给属性赋值

判断kvc是否可用 可用则付值

```objc
- (void)setValue:(id)value forObject:(id)object
{
    if (self.type.KVCDisabled || value == nil) return;
    [object setValue:value forKey:self.name];
}
```
