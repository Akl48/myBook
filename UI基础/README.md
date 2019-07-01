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

### 切换主控制storybroad

![image-20190513210856706](/Users/zhoutianrong/Library/Application Support/typora-user-images/image-20190513210856706.png)

[ ] Is Initial View Controller 是否是初始化控制器 再在target->General->Deployment Info->设置初始入口

![image-20190513211022775](/Users/zhoutianrong/Library/Application Support/typora-user-images/image-20190513211022775.png)

### IB

Interface Builder现在打开Xcode即打开IB即前缀IB

### 类扩展的使用

```objc
@interface ViewController()
@property(nonatomic,strong)NSString *name;
@end
```

在**声明属性**的时候，如果是声明在.h头文件中，在**其他文件**包含头文件的时候想访问属性来改变的时候是存在数据安全问题的。.m文件是不能被包含的。

### UIView

1. 所有属性都有一些公共的属性 - 位置|尺寸|背景 将这些控件共同的属性抽取出来形成父类UIView

2. 所有控件最终都继承自UIView

#### UIView常见属性 

``` objc
(UIView *) superview； 

获取父控件 

NSArray * subviews; //获取一个View所有子对象

-(void)removeFromSuperview 

/* 从父控件移除控件 但不是销毁*/ 

-(void)addSubview 

/* 添加控件*/ 

-(UIView*)viewWithTag:(NSInteger)tag;  
// 本质上是一层一层View的遍历找 找到就返回 1.最好不适用  2.存在性能问题

/* 根据一个tag标志找出对应的控件（一般是子控件）*/ 
```

### 常用UI控件 

* UIButton 按钮 
* UILabel 文本标签 
* UITextField 文本输入框 
* UIImageView 图片显示 
* UIScrollView 滚动的控件 
* UICollectionView 九宫格 
* UIWebView 网页显示控件 
* UIAlertView 对话警告框 
* UINavigationBar 导航栏 

#### UILabel
* text显示文字

* Lines == 0 代码中`numberOfLines` 能显示多少行 0表示就显示多少

* Line Break `lineBreakMode` 超出的最大之后怎么显示

  * ```objc
    NSLineBreakByWordWrapping = 0,    // Wrap at word boundaries, default
    NSLineBreakByCharWrapping,        // Wrap at character boundaries
    NSLineBreakByClipping,        		// Simply clip
    NSLineBreakByTruncatingHead,    	// Truncate at head of line: "...wxyz"
    NSLineBreakByTruncatingTail,    	// Truncate at tail of line: "abcd..."
    NSLineBreakByTruncatingMiddle    	// Truncate middle of line:  "ab...yz"
    ```

* `textAlignment` 文字显示格式

  * ```objc
    NSTextAlignmentLeft      = 0,    // Visually left aligned
    NSTextAlignmentCenter    = 1,    // Visually centered
    NSTextAlignmentRight     = 2,    // Visually right aligned
    NSTextAlignmentRight     = 1,    // Visually right aligned
    NSTextAlignmentCenter    = 2,    // Visually centered
    NSTextAlignmentJustified = 3,    // Fully-justified. The last line in a paragraph is natural-aligned.
    NSTextAlignmentNatural   = 4,    // Indicates the default alignment for script
    ```
#### UIButton
* 创建 `buttonWithType:`自定义 
* 高亮 设置属性的时候都必须设置按钮状态`set...forState:（normal\highlight）` 
  * 监听事件通过 addTarget: 
#### UIImageView
* contentMode 控制图片显示 （UIViewContentMode... scale:带scale的会拉伸 Aspect:保持图片比例）  
* `imageView.clipsToBounds = YES;`超过边框的会被裁剪
  
  * 资源的虚拟以及真实文件夹 
    
    * 添加的黄色文件夹是虚拟的只在项目结构中有|蓝色的文件夹是实在存在的
  
* UIImageView实现动画（帧动画） 
  * NSArray * animationImages 
  
    * 设置帧动画的图片设置图片数组
  
    * ```objc
      NSMutableArray *images = [NSMutableArray array];
      for(int i = 1 ; i <= count;++i){
        NSString *file = [NSString stringWithFormat:@"%@_%d",name,i];
        UIImage *image = [UIImage imageNamed:file];
        [images addObject:image];
      }
      self.imageView.animationImages = images;
      ```
  
  * startAnimating 开始 
  
  * stopAnimating 停止 
  
  * isAnimating 是否正在播放 
  
  * animationRepeatCount 重复次数（0为无限次） 
  
  * animationDuration 持续时间 
  
  * 声音的播放AVFoundation框架 中的AVplayer类对象
  
    * ```objc
      NSURl *url = [[NSBundle mainBundle] URLForResource:[NSString stingWithFormat:@"%@.mp3",name] withExtension:nil];
      self.player = [AVplayer playWithUrl:url];
      [self.player play];
      ```
1. 通过断点po NSHomeDirectory() 会输出沙盒路径 

2. [NSBundle mainBundle]可以获取安装包对象 

   1. NSString对象可以通过isEqualToString判断是否相同 

4. 在使用[UIImage imageNamed:]一定会使用缓存技术，消耗内存之后不会恢复 
   1. imageWithContentsOfFile:  //有file为结尾的话一般是全路径 mainBundle （需要传入全路径图片） 
   
      1. ```objc
         NSString *imageName = [NSString stringWithFormat:@"%@_%d",name,count];
         NSString *filePath = [[NSBundle mainBundle] pathForResource:imageName ofType:@"png"];
         UIImage* image = [UIImage imageWithContentsOfFile:filePath];
         ```
   
   2. Assets文件夹内在打包的时候会打包成另一个文件 
   
      1. 在Assets文件夹内的话只能通过图片访问一定会有缓存（适合使用频率高的图片） 

### 设置颜色半透明 

`[UIColor colorWithRed: <(CGFloat)> green:<(CGFloat)> blue:<(CGFloat)> alpha:<(CGFloat)>]//设置alpha ` 

### 拉伸图片 

` [image resizableImageWithCapInsets:UIEdgeInsetsMake(//保证不拉伸的地方) resizingMode://拉伸还是平铺]; ` 

直接配置图片（必须在asset里） 

#### 小技巧 

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

1. NSSArray* arr = [[NSBundle mainBundle] loadNamed: owner: option:]; 

2. UINib* nib = [UINib nibWithName:@"" bundle:nil]; 
   1. NSArray* arr = [nib instantiateWithOwner:nil options:nil]; 

如果不设置xib控件尺寸会有默认的尺寸 

最好的控制控件的方法就是拖根线 

![55c7220a11d646e0c15608551bfa1ddf.png](evernotecid://855EEE5C-EBC3-428D-871C-8987FF9E56B6/appyinxiangcom/22726474/ENResource/p6) 

如果一个控件是从xib或者storyboard创建的，初始化一定会调用initWithCoder:这个方法 

如果一个控件从xib或者storybroad创建出来的，加载完毕的时候一定会调用awakeFromNib这个方法 

> xib类型文件是不会主动加载的需要特殊编译 

UIView可以隐藏通过hidden隐藏也可以通过透明实现alpha 

## 渐变动画 

### 头尾式 

```objc 

[UIView beginAnimation:nil context:nil]; 

[UIView setAnimationDuration:2];//设置动画时间 

//实现动画 

[UIView commitAnimations]; 

```

### block式 

```objc 

[UIView animateWithDuration:time animation:^{ 

//实现动画 

}]; 

[UIView animateWithDuration:(NSTimeInterval) animations:^{ 

//实现动画 

} completion:^(BOOL finished) { 

//完成后实现效果 

}]; 

```

## 工具类 

1. 一般继承于NSObject 

2. 方法一般都是类方法 

3. 专门用来处理问题 

## KVO键值编程 

### 赋值 

可以直接修改成员变量，就算是私有的 

```objc 

[setValue:(nullable id) forKey:(nonnull NSString * )]; 

[setValue:(nullable id) forKeyPath:(nonnull NSString * )]; 

[setValuesForKeysWithDictionary:(nonnull NSDictionary<NSString * ,id> * )];//会遍历字典，自动赋值字典模型。局限是找不到字典的属性会报错，实际可能不用 字典转模型 

[setValue:(nullable id) forUndefinedKey:(nonnull NSString * )];//系统找不到key的时候会自定调用 

```

### 取值 

```objc 
[valueForKey:(nonnull NSString *)]; 

[valueForKeyPath:(nonnull NSString *)];//最好用KeyPath 

[valueForUndefinedKey:(nonnull NSString *)]; 

[dictionaryWithValuesForKeys:(nonnull NSArray<NSString *> *)];//会返回一个字典 模型转字典 如果是数组的话，会抽取数组中每个数组的值，并返回一个数组 
```

## KVO键值监听 

可以监听某个对象值的改变 

```objc 

[self addObserver:self forKeyPath:(nonnull NSString *) options:kNilOptions context:nil];//给对象添加一个监听器 

- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context{ 

// keyPath 那个属性改了 

// object 那个对象 

// change 改成什么样了 

// context 当初传的context是什么就是什么 

}//监听方法当通KVO监听到之后会自动调用 

//在监听对象销毁之前要移除KVO 

[self removeObserver:(nonnull NSObject *) forKeyPath:(nonnull NSString *)]; 

```

## UIScrollView 

1. 讲要展示的内容添加到里面 

2. 设定内容尺寸contentSize 可滚动的尺寸 contentSize - scrollView尺寸 

3. 不能通过索引subviews访问ScrollView 

### 不能滚动原因 

1. scrollEnable属性设置为NO 

2. userInteractionEnabled是UIView的属性，设置之后用户不能交互 

### 没有设置contentSize怎么实现滚动效果 

scrollView.alwaysBounceHorizontal = YES;(默认NO) 

### 滚动条 

scrollView.showHorizontalScrollIndicator = NO;(默认是YES) 

### contentOffset 

偏移量 

### delegate代理 

声明他的代理为一个对象即本控制器即可（任何对象都可以但是一般都是控制器） 

scrollView.delegate = self; 

还需要满足协议，遵守协议需要在interface中声明，类扩展中遵守也可以 

代理方法不需要主动使用，会自动调用 

代理属性是weak 

#### 缩放 

viewForZoomingInScrollView:(UIScrollView *)scrollView 

在设置最大最小缩放比例 

maximumZoomScale 

minimumZoomScale 

## 屏幕适配 

```objc 

typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing) { 

UIViewAutoresizingNone = 0, 

UIViewAutoresizingFlexibleLeftMargin = 1 << 0, 

UIViewAutoresizingFlexibleWidth = 1 << 1, 

UIViewAutoresizingFlexibleRightMargin = 1 << 2, 

UIViewAutoresizingFlexibleTopMargin = 1 << 3, 

UIViewAutoresizingFlexibleHeight = 1 << 4, 

UIViewAutoresizingFlexibleBottomMargin = 1 << 5 

}; 

```

### UILable实现包裹内容 

1. 实现位子约束 

2. 设置宽度约束<= 

3. 不用设置高度约束 

用代码实现约束非常复杂 

### VFL添加约束 

```objc 

NSString* vfl_h = @"H:|-space-[blueView(==redView)]-space-[redView]-space-|";//水平 

NSString* vfl_V = @"V:[blueView]-space-|";//垂直 

NSDictionary* dict = @{@"redView":redView,@"blueView":blueView}; 

NSDictionary* space = @{@"space":@20}; 

NSArray* arr = [NSLayoutConstraint constraintsWithVisualFormat:vfl_h options:kNilOptions metrics:space views:dict]; 

[self addConstraints:arr]; 

```

## UITableView 

继承于UIScrollView因此支持纵向滑动性能好 

* 需要一组数据源（DataSource）遵守UITableViewDataSource协议 

* 以及需要满足UITableViewDelegate 

* 重要就是字典转模型 

```objc 
//告诉tableView每个一共有多少块 

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView{ 

return (NSInteger); 

} 

//告诉TableView每个section中的Row的个数 

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{ 

return (NSInteger); 

} 

//告诉TableView每行的cell 

- (UITableViewCell*)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{ 

// indexPath.section; 哪一块 

// indexPath.row 哪一行 

UITableViewCell* cell = [[UITableViewCell alloc]init]; 

cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;//设置cell右边指示样子 

return [[UITableViewCell alloc]init]; 

} 

- (NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section{ 

//设置分组头部标题 

return (NSString*); 

} 

- (NSString *)tableView:(UITableView *)tableView titleForFooterInSection:(NSInteger)section{ 

//设置分组尾部标题 

return (NSString*); 

} 

```

### UITableView常见属性 

#### 分割线 


\* separtorColor 设置颜色 

\* separtorStyle 设置样式 

#### 高度 

\* rowHeight 每行cell的高度 

\* sectionHeaderHeight 每组头部高度 

\* sectionFooterHeight 每组尾部高度 

#### 表头表位 

\* tableHeaderView 

\* tableFooterView 

#### 自定义tableView中section header/footerView的View的复用

- (instancetype)initWithReuseIdentifier:(NSString *)reuseIdentifier{
   
    self = [super initWithReuseIdentifier:reuseIdentifier];
   
    if (self) {
       
        [self _init];//_init表示初始化方法
    }
   
    return self;
}

#### 右边索引 

\* sectionIndexColor 

\* sectionIndexBackgroundColor 

### UITableViewCell常见属性 

\* accessoryType 指示样子 

\* accessoryView 设置右边的知识控件 

\* selectionStyle 设置cell选中样式 

#### contentView 

Cell中的image Text等都是contentView的子控件，所以在实现删除的时候移动contentView即可 

### 索引 

```objc
-(nullable NSArray<NSString * >* )sectionIndexTitlesForTableView:(UITableView* )TableView;
```

### 自定义不等高cell

#### 数据刷新

1.  全局数据刷新
  
   1. `[self.tableView reload];`
   
2. 部分数据刷新

   1. > `NSArray *indexpaths = @[NSIndexPath indexPathForRow:0 inSection:0];`
      >
      > `[self.tableView reloadRowsAtIndexPaths:indexpaths withAnimation:UITableViewRowAnimationLeft];`
      >
      > 适用于数据数组个数不变

   2.  > `[self.tableView insertRowsAtIndexPaths:indexpaths withRowAnimation:UITableViewRowAnimationTop];`

3. 左滑删除

   1. 设置代理UITableViewDelegate

   2. 代理方法
     
      1. ```objc
        - (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(nonnull NSIndexPath *)indexPath{
            //只要有这个方法就能删除 在这里删除数据 刷新数据
            [self.wineArray removeObjectAtIndex:indexPath.row];
            [self.tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationTop];
        }
        ```
        
      2. 修改文字为中文 `- (NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath{ return @"删除";}`|在Target中的Localizations添加简体中文
      
   3. 实现左滑多个按钮
   
      1. ```objc
         - (NSArray<UITableViewRowAction *> *)tableView:(UITableView *)tableView editActionsForRowAtIndexPath:(NSIndexPath *)indexPath{
             self.tableView.editing = YES;
             UITableViewRowAction *rowAction = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleNormal title:@"删除" handler:^(UITableViewRowAction * _Nonnull action, NSIndexPath * _Nonnull indexPath) {
                 //点击调用 而且默认的删除不会被调用
             }];
             rowAction.backgroundColor = [UIColor redColor];
             UITableViewRowAction *rowAction2 = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleNormal title:@"关注" handler:^(UITableViewRowAction * _Nonnull action, NSIndexPath * _Nonnull indexPath) {
                 //点击调用block
                 //退出编辑模式(在左滑之后进入编辑模式)
                 self.tableView.editing = NO;
             }];
             return @[rowAction,rowAction2];
         }
         ```
   
   4. 批量删除
      1. 进入编辑模式`[self.table setEditing:!self.tableView.isEditing animated:YES];`
      2. 自定义批量删除
         1. 自定义cell控件在contentView添加一个ImageView

### 通知
1. 通知的声明 name（通知名称） object（通知发布者） NSDictionary *userInfo（一些额外的信息）
    * `NSNotification *note = [NSNotification notificationWithName:@"军事信息" object:nil userInfo:nil];`
2. 通知的发布
    * `[[NSNotificationCenter defaultCenter]postNotification:note];`
3. 通知的监听 Observer（监听者）object（监听的对象）
    * `[[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(haveNote:) name:@"军事信息" object:nil];`
4. 移除监听（通常在dealloc中，否则会出现野指针问题）
    * `[[NSNotificationCenter defaultCenter]removeObserver:self];`(移除self监听的所有通知)

