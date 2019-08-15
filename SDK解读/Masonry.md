# Masonry

## 代码案例

```objc
    [self.ImageView mas_makeConstraints:^(MASConstraintMaker *make){
        make.left.equalTo(self.view.mas_left).offset(15);
        make.right.equalTo(self.view.mas_right).offset(-15);
        make.centerY.equalTo(self.contentView.mas_centerY);
    }]
```

内部大概分为四个步骤（这里我把MAS统一省略以免误导学习）

1. makeConstraints是UIView分类中的实例方法（所有继承自UIView的都可以使用）
   1. remakeConstraints|updateConstraints共三种约束方式 constraintMaker.removeExisting|updateExisting = YES;是这三个方法之间唯一的区别
2. make是ConstraintMaker的实例对象主要负责约束的添加
3. left.top
4. equalTo()

## 解析

### mas_makeConstraints

```objc
- (NSArray *)mas_makeConstraints:(void(^)(MASConstraintMaker *))block {
    self.translatesAutoresizingMaskIntoConstraints = NO;
    MASConstraintMaker *constraintMaker = [[MASConstraintMaker alloc] initWithView:self];
    // constraintMaker.removeExisting|updateExisting = YES;是这三个方法之间唯一的区别
    block(constraintMaker);
    return [constraintMaker install];
}
// 设置约束
```

> 在这里面看的很清楚将一个MASConstraintMaker的实例传入了block中 而`[constraintMaker install]`添加了约束
> translatesAutoresizingMaskIntoConstraints为NO（默认为YES）

```text
/* By default, the autoresizing mask on a view gives rise to constraints that fully determine
 the view's position. This allows the auto layout system to track the frames of views whose
 layout is controlled manually (through -setFrame:, for example).
 When you elect to position the view using auto layout by adding your own constraints,
 you must set this property to NO. IB will do this for you.
 */
 ```

### make(MASConstraintMaker)

> 链式调用make.left.equalTo();

```objc
// step 1
- (MASConstraint *)left {
    return [self addConstraintWithLayoutAttribute:NSLayoutAttributeLeft];
}
// step 2
- (MASConstraint *)addConstraintWithLayoutAttribute:(NSLayoutAttribute)layoutAttribute {
    return [self constraint:nil addConstraintWithLayoutAttribute:layoutAttribute];
}
// step 3
- (MASConstraint *)constraint:(MASConstraint *)constraint addConstraintWithLayoutAttribute:(NSLayoutAttribute)layoutAttribute {
    MASViewAttribute *viewAttribute = [[MASViewAttribute alloc] initWithView:self.view layoutAttribute:layoutAttribute];
    MASViewConstraint *newConstraint = [[MASViewConstraint alloc] initWithFirstViewAttribute:viewAttribute];
    if ([constraint isKindOfClass:MASViewConstraint.class]) {
        //replace with composite constraint
        NSArray *children = @[constraint, newConstraint];
        MASCompositeConstraint *compositeConstraint = [[MASCompositeConstraint alloc] initWithChildren:children];
        compositeConstraint.delegate = self;
        [self constraint:constraint shouldBeReplacedWithConstraint:compositeConstraint];
        return compositeConstraint;
    }
    if (!constraint) {
        newConstraint.delegate = self;
        [self.constraints addObject:newConstraint];
    }
    return newConstraint;
}
```

step1:MASViewAttribute是一个可变元组 包含了view item NSLayoutAttribute(储存了View和他相关的约束信息)
step2:MASViewConstraint就是一个约束 包含了 MASViewAttribute *firstViewAttribute *secondViewAttribute;
step3:由于第二步的constraint传入一个nil 看到后面的判空中 添加到了一个约束的数组中

### install

### equalTo

### 三个重要的类

#### UIView+MASAdditions（分类）

分类，提供了和外界的接口

#### constraintMaker

#### constraint（抽象类）

##### viewConstraint（视图约束对象）

##### CompositeConstraint（一组约束对象）
