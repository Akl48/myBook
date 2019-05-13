

[TOC]

## iOS中storybroad的基础使用 

### 方法的绑定 

```
-(IBAction)set{}
```

**通过IBAction** 

### 属性的绑定 

```
@property(nonamatic,strong)IBOutlet UILable * lab;
```

**通过IBOutlet** 

## UIView常见属性 

``` objc
UIView * superview； 

获取父控件 

NSArray * subviews; 

-(void)removeFromSuperview 

/* 移除控件*/ 

-(void)addSubview 

/* 添加控件*/ 

-(UIView*)viewWithTag:(NSInteger)tag; 

/* 根据一个tag标志找出对应的控件（一般是子控件）*/ 
```

### 常用UI控件 

OC规定不能直接修改OC对象结构体的属性 

* UIButton 按钮 
  * 创建 buttonWithType:(UIButtonTypeCustom)自定义 
  * 高亮 设置属性的时候都必须设置按钮状态set...forState:（normal\highlight） 
  * 监听事件通过 addTarget: 

* UILabel 文本标签 

* UITextField 文本输入框 

* UIImageView 图片显示 
  * contentMode 控制图片显示 （UIViewContentMode... scale:拉伸 Aspect:保持图片比例） 
  * 资源的虚拟以及真实文件夹 
  * UIImageView实现动画（帧动画） 
  * NSArray * animationImages 
    * startAnimating 开始 
    * stopAnimating 停止 
    * isAnimating 是否正在播放 
    * animationRepeatCount 重复次数（0为无限次） 
    * animationDuration 持续时间 

* UIScrollView 滚动的控件 

* UICollectionView 九宫格 

* UIWebView 网页显示控件 

* UIAlertView 对话警告框 

* UINavigationBar 导航栏 

1. 通过断点po NSHomeDirectory()输出虚拟机文件夹 

2. [NSBundle mainBundle]获取安装包对象 

3. NSString对象通过isEqualToString判断是否相同 

4. 在使用[UIImage imageNamed:]一定会使用缓存技术，消耗内存之后不会恢复 
   1. imageWithContentsOfFile: //有file为结尾的话一般是全路径 mainBundle （需要传入全路径图片） 
   2. Assets文件夹内在打包的时候会打包成另一个文件 
      1. 在Assets文件夹内的话只能通过图片访问一定会有缓存（适合使用频率高的图片） 

### 设置颜色半透明 

`[UIColor colorWithRed: <(CGFloat)> green:<(CGFloat)> blue:<(CGFloat)> alpha:<(CGFloat)>]//设置alpha ` 

### 拉伸图片 

` [image resizableImageWithCapInsets:UIEdgeInsetsMake(//保证不拉伸的地方) resizingMode://拉伸还是平铺]; ` 

直接配置图片（必须在asset里） 

###### 小技巧 

/** 方法注册add*/ 

![8bf2a55cec809eb8bf837d994f32cf08.png](evernotecid://855EEE5C-EBC3-428D-871C-8987FF9E56B6/appyinxiangcom/22726474/ENResource/p5) 

隐藏状态栏 

- (BOOL)prefersStatusBarHidden; 

\#program mark-注释方法 

#### 模型的使用 

专门用来存放数据的对象 

> 一般继承NSObject，在.h文件中声明一些用来存放数据的属性 

用模型取代字典的好处 

1. 编写的时候编译器会有提示 

2. 访问属性会有提示 

在使用的时候要将字典转为**模型**，通过赋值给可变数组再直接把可变数组拷贝进不可变数组达到目的 

#### 类前缀 

通过自定义类前缀区别系统类 

\> ZZCButton; 

#### instancetype 

苹果通过instancetype来取代**返回值**中的id,会检测返回值类型。 

### 自定义控件 

封装控件内部的细节 

1. -(void)layoutSubviews;布局子控件，控制子控件尺寸 

2. -(instancetype)init;只做初始化控件 

#### initWithFrame 

* init会自动调用initWithFrame 

* [super initWithFrame:frame]; 

* 通过这个方法可以保证创建哪个都是ok的 

### 数据传递 

1. 直接通过属性传递（暴露了属性） 

2. 通过set方法设置 

3. 提供一个模型属性传入参数 （常用方法） 
   1. @class 模型类 
   2. 声明属性 
   3. 重新模型的set方法 

### 自定义按钮 

```objc 
- (CGRect)imageRectForContentRect:(CGRect)contentRect{ 

// 返回图片显示位子 contentRect是按钮的bounds 

} 

- (CGRect)titleRectForContentRect:(CGRect)contentRect{ 

// 返回标题显示位子 

} 

//或者直接使用layoutSubviews 
```

#### 按钮内边距 

在storybroad中内边距在Edge中设置代码可通过 

```objc 
imageEdgeInsets设置  
```

## xib文件 

相对于storybroad是轻量级的 

用于描述局部的一小部分的界面 

可以用来自定义控件 

\### 加载xib文件 

在编译之后为nib文件 

\1. NSSArray* arr = [[NSBundle mainBundle] loadNamed: owner: option:]; 

\2. UINib* nib = [UINib nibWithName:@"" bundle:nil]; 

\1. NSArray* arr = [nib instantiateWithOwner:nil options:nil]; 

如果不设置xib控件尺寸会有默认的尺寸 

最好的控制控件的方法就是拖根线 

![55c7220a11d646e0c15608551bfa1ddf.png](evernotecid://855EEE5C-EBC3-428D-871C-8987FF9E56B6/appyinxiangcom/22726474/ENResource/p6) 

如果一个控件是从xib或者storyboard创建的，初始化一定会调用initWithCoder:这个方法 

如果一个控件从xib或者storybroad创建出来的，加载完毕的时候一定会调用awakeFromNib这个方法 

\> xib类型文件是不会主动加载的需要特殊编译 

UIView可以隐藏通过hidden隐藏也可以通过透明实现alpha 

\## 渐变动画 

\### 头尾式 

\```objc 

[UIview beginAnimation:nil context:nil]; 

[UIView setAnimationDuration:2];//设置动画时间 

//实现动画 

[UIView commitAnimations]; 

\``` 

\### block式 

\```objc 

[UIView animateWithDuration:time animation:^{ 

//实现动画 

}]; 

[UIView animateWithDuration:(NSTimeInterval) animations:^{ 

//实现动画 

} completion:^(BOOL finished) { 

//完成后实现效果 

}]; 

\``` 

\## 工具类 

\1. 一般继承于NSObject 

\2. 方法一般都是类方法 

\3. 专门用来处理问题 

\## KVO键值编程 

\### 赋值 

可以直接修改成员变量，就算是私有的 

\```objc 

[setValue:(nullable id) forKey:(nonnull NSString * )]; 

[setValue:(nullable id) forKeyPath:(nonnull NSString * )]; 

[setValuesForKeysWithDictionary:(nonnull NSDictionary<NSString * ,id> * )];//会遍历字典，自动赋值字典模型。局限是找不到字典的属性会报错，实际可能不用 字典转模型 

[setValue:(nullable id) forUndefinedKey:(nonnull NSString * )];//系统找不到key的时候会自定调用 

\``` 

\### 取值 

\```objc 

[valueForKey:(nonnull NSString *)]; 

[valueForKeyPath:(nonnull NSString *)];//最好用KeyPath 

[valueForUndefinedKey:(nonnull NSString *)]; 

[dictionaryWithValuesForKeys:(nonnull NSArray<NSString *> *)];//会返回一个字典 模型转字典 如果是数组的话，会抽取数组中每个数组的值，并返回一个数组 

\``` 

\## KVO键值监听 

可以监听某个对象值的改变 

\```objc 

[self addObserver:self forKeyPath:(nonnull NSString *) options:kNilOptions context:nil];//给对象添加一个监听器 

\- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context{ 

keyPath 那个属性改了 

object 那个对象 

change 改成什么样了 

context 当初传的context是什么就是什么 

}//监听方法当通KVO监听到之后会自动调用 

//在监听对象销毁之前要移除KVO 

[self removeObserver:(nonnull NSObject *) forKeyPath:(nonnull NSString *)]; 

\``` 

\## UIScrollView 

\1. 讲要展示的内容添加到里面 

\2. 设定内容尺寸contentSize 可滚动的尺寸 contentSize - scrollView尺寸 

\3. 不能通过索引subviews访问ScrollView 

\### 不能滚动原因 

scrollEnable属性设置为NO 

userInteractionEnabled是UIView的属性，设置之后用户不能交互 

\### 没有设置contentSize怎么实现滚动效果 

scrollView.alwaysBounceHorizontal = YES;(默认NO) 

\### 滚动条 

scrollView.showHorizontalScrollIndicator = NO;(默认是YES) 

\### contentOffset 

偏移量 

\### delegate代理 

声明他的代理为一个对象即本控制器即可（任何对象都可以但是一般都是控制器） 

scrollView.delegate = self; 

还需要满足协议，遵守协议需要在interface中声明，类扩展中遵守也可以 

代理方法不需要主动使用，会自动调用 

代理属性是weak 

\#### 缩放 

viewForZoomingInScrollView:(UIScrollView *)scrollView 

在设置最大最小缩放比例 

maximumZoomScale 

minimumZoomScale 

\## 屏幕适配 

\```objc 

typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing) { 

UIViewAutoresizingNone = 0, 

UIViewAutoresizingFlexibleLeftMargin = 1 << 0, 

UIViewAutoresizingFlexibleWidth = 1 << 1, 

UIViewAutoresizingFlexibleRightMargin = 1 << 2, 

UIViewAutoresizingFlexibleTopMargin = 1 << 3, 

UIViewAutoresizingFlexibleHeight = 1 << 4, 

UIViewAutoresizingFlexibleBottomMargin = 1 << 5 

}; 

\``` 

\### UILable实现包裹内容 

\1. 实现位子约束 

\2. 设置宽度约束<= 

\3. 不用设置高度约束 

用代码实现约束非常复杂 

\### VFL添加约束 

\```objc 

NSString* vfl_h = @"H:|-space-[blueView(==redView)]-space-[redView]-space-|";//水平 

NSString* vfl_V = @"V:[blueView]-space-|";//垂直 

NSDictionary* dict = @{@"redView":redView,@"blueView":blueView}; 

NSDictionary* space = @{@"space":@20}; 

NSArray* arr = [NSLayoutConstraint constraintsWithVisualFormat:vfl_h options:kNilOptions metrics:space views:dict]; 

[self addConstraints:arr]; 

\``` 

\## UITableView 

继承于UIScrollView因此支持纵向滑动性能好 

\* 需要一组数据源（DataSource）遵守UITableViewDataSource协议 

\* 以及需要满足UITableViewDelegate 

\* 重要就是字典转模型 

\```objc 

//告诉tableView每个一共有多少块 

\- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView{ 

return (NSInteger); 

} 

//告诉TableView每个section中的Row的个数 

\- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{ 

return (NSInteger); 

} 

//告诉TableView每行的cell 

\- (UITableViewCell*)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{ 

// indexPath.section; 哪一块 

// indexPath.row 哪一行 

UITableViewCell* cell = [[UITableViewCell alloc]init]; 

cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;//设置cell右边指示样子 

return [[UITableViewCell alloc]init]; 

} 

\- (NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section{ 

//设置分组头部标题 

return (NSString*); 

} 

\- (NSString *)tableView:(UITableView *)tableView titleForFooterInSection:(NSInteger)section{ 

//设置分组尾部标题 

return (NSString*); 

} 

\``` 

\### UITableView常见属性 

\#### 分割线 

\* separtorColor 设置颜色 

\* separtorStyle 设置样式 

\#### 高度 

\* rowHeight 每行cell的高度 

\* sectionHeaderHeight 每组头部高度 

\* sectionFooterHeight 每组尾部高度 

\#### 表头表位 

\* tableHeaderView 

\* tableFooterView 

\#### 右边索引 

\* sectionIndexColor 

\* sectionIndexBackgroundColor 

\### UITableViewCell常见属性 

\* accessoryType 指示样子 

\* accessoryView 设置右边的知识控件 

\* selectionStyle 设置cell选中样式 

\#### contentView 

Cell中的image Text等都是contentView的子控件，所以在实现删除的时候移动contentView即可 

\### 索引 

```
-(nullable NSArray<NSString * >* )sectionIndexTitlesForTableView:(UITableView* )TableView;
```