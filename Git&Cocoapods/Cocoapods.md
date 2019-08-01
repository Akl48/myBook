# CocoaPods

[原文来自简书](<https://www.jianshu.com/p/aef862d01e86>)
CocoaPods是开发OSX和iOS应用程序的一个**第三方库的依赖管理工具**。通过CocoaPods，可以定义自己的依赖关系(pods)，并且在整个开发环境中对第三方库的版本管理非常方便。由于在工程中引入第三方框架，要配置build phases 和linker flags(**cocoapods帮你省去了这个**)过程，而CocoaPods则简化了这个过程。

## 使用

通过命令行进入在你要使用的项目中使用`pod init`自动回生成一个**Podfile**文件，通过vim进入并且更改，将你要使用的第三方库的名字按照格式输入。再退出vim，再`pod install`安装库。

## 组件

CocoaPods使用Ruby写的，并且由若干个Ruby包构成(gems)，在解析整合过程最重要的几个gems是 `CocoaPod` `Core` `Xcodeproj`

* CocoaPod
  * 面向用户的组件，没执行一个pod命令时，这个组件都会激活。
  * 包括了所有使用CocoaPods涉及到的功能
* Core
  * 提供支持和CococaPods相关文件的处理，只要是Podfile和podspec
    * Podfile定义项目使用的第三方库
    * podspec描述了一个库怎么添加到工程中
* Xcodeproj
  * 负责所有文件的整合。
  * 创建并秀给.xcodeproj和.xcworkspace文件

## pod install过程

```c
Analyzing dependencies

Inspecting targets to integrate
  Using `ARCHS` setting to build architectures of target `Pods-download`: (``)

Finding Podfile changes
  - AFNetworking
  - FMDB
  - MJExtension
  - SDWebImage

Resolving dependencies of `Podfile`

Comparing resolved specification to the sandbox manifest
  - AFNetworking
  - FMDB
  - MJExtension
  - SDWebImage

Downloading dependencies

-> Using AFNetworking (3.2.1)

-> Using FMDB (2.7.2)

-> Using MJExtension (3.0.15.1)

-> Using SDWebImage (4.4.2)
  - Running pre install hooks

Generating Pods project
  - Creating Pods project
  - Adding source files to Pods project
  - Adding frameworks to Pods project
  - Adding libraries to Pods project
  - Adding resources to Pods project
  - Linking headers
  - Installing targets
    - Installing target `AFNetworking` iOS 7.0
    - Installing target `FMDB` iOS 4.3
    - Installing target `MJExtension` iOS 6.0
    - Installing target `SDWebImage` iOS 7.0
    - Installing target `Pods-download` iOS 12.1
  - Running post install hooks
  - Writing Xcode project file to `Pods/Pods.xcodeproj`
    - Generating deterministic UUIDs
  - Writing Lockfile in `Podfile.lock`
  - Writing Manifest in `Pods/Manifest.lock`

Integrating client project

Integrating target `Pods-download` (`download.xcodeproj` project)
  - Running post install hooks
    - cocoapods-stats from
    `/Library/Ruby/Gems/2.3.0/gems/cocoapods-stats-1.0.0/lib/cocoapods_plugin.rb`

Sending stats
      - AFNetworking, 3.2.1
      - FMDB, 2.7.2
      - MJExtension, 3.0.15.1
      - SDWebImage, 4.4.2

-> Pod installation complete! There are 4 dependencies from the Podfile and 4 total pods installed.
```

1. 读取Podfile文件
2. 版本控制和冲突
   1. 两个不同的 pods 依赖于 CocoaLumberjack 的两个版本，假设一个依赖于2.3.1，另一个依赖于2.3.3，此时冲突解决系统可以使用最新的版本2.3.3，因为这个可以向后与2.3.1兼容。
   2. 总有一些冲突需要手动解决
3. 加载源文件
   1. 每个.podspec文件都包含一个源代码的索引，这些索引一般包裹一个 git 地址和 git tag。它们以 commit SHAs 的方式存储在~/Library/Caches/CocoaPods中
   2. **Podfile、.podspec和缓存文件的信息将源文件下载到Pods**
4. 生成Pods.xcodeproj
   1. 每次pod install执行，如果检测到改动时，CocoaPods 会利用 Xcodeproj gem 组件对Pods.xcodeproj进行更新。如果该文件不存在，则用默认配置生成。否则，会将已有的配置项加载至内存中。
5. 安装第三方库
   1. 当 CocoaPods 往工程中添加一个第三方库时，不仅仅是添加代码这么简单，还会添加很多内容。由于每个第三方库有不同的 target，因此对于每个库，都会有几个文件需要添加，每个 target 都需要：
      * 一个包含编译选项的.xcconfig文件
      * 一个同时包含编译设置和 CocoaPods 默认配置的私有.xcconfig文件
      * 一个编译所必须的prefix.pch文件
      * 另一个编译必须的文件dummy.m
6. 创建Target
   1. 一旦每个 pod 的 target 完成了上面的内容，整个Podstarget 就会被创建。这增加了相同文件的同时，还增加了另外几个文件。如果源码中包含有资源 bundle，将这个 bundle 添加至程序 target 的指令将被添加到Pods-Resources.sh文件中。还有一个名为Pods-environment.h的文件，文件中包含了一些宏，这些宏可以用来检查某个组件是否来自 pod。最后，将生成两个认可文件，一个是plist，另一个是markdown，这两个文件用于给最终用户查阅相关许可信息
7. 写入磁盘
   1. 直到现在，许多工作都是在内存中进行的。为了让这些成果能被重复利用，我们需要将所有的结果保存到一个文件中。所以Pods.xcodeproj文件被写入磁盘，另外两个非常重要的文件：Podfile.lock和Manifest.lock都将被写入磁盘。
      * Podfile.lock CocoaPods 创建的最重要的文件之一。它记录了需要被安装的 pod 的每个已安装的版本
      * Manifest.lock 这是每次运行pod install命令时创建的Podfile.lock文件的副本

## 原理

1. 它是将所有的依赖库都放到另一个名为 Pods 项目中
2. Pods 项目最终会编译成一个名为 libPods.a 的文件，主项目只需要依赖这个 .a 文件即可。这样，依赖库源码管理工作都从主项目移到了 Pods 项目中。
3. 对于资源文件，CocoaPods 提供了一个名为 Pods-resources.sh 的 bash 脚本，该脚本在每次项目编译的时候都会执行，将第三方库的各种资源文件复制到目标目录中。
4. CocoaPods 通过一个名为 Pods.xcconfig 的文件来在编译时设置所有的依赖和参数。

## 通过Cocoapods达成组件化管理

**组件化管理**也就是业务模块化，将一个复杂的项目根据业务，划分成不同的模块。而cocoapods，提供私有pod repo，使用时把自己的组件放在私有pod repo里，然后在Podfile里直接通过pod命令集成。一个组件对应一个私有pod，每个组件依赖自己所需要的三方库。多个组件联合开发的时候，可以在一个podspec里配置子模块，这样在每个组件自己的podspec里，只需要把子模块里的pod依赖关系拷贝过去就行了（**组件化的存在方式是以pod库的形式存在的，即通过利用cocoapods的方式添加更多的组件**）。

### 组件化开发的缺点

1. 代码耦合严重
2. 依赖严重
3. 其它app接入某条产品线难以集成
4. 项目复杂、臃肿、庞大，编译时间过长
5. 难以做集成测试
6. 对开发人员，只能使用相同的开发模式

### 组件化开发的优点

1. 项目结构清晰
2. 代码逻辑清晰
3. 拆分粒度小
4. 快速集成
5. 能做单元测试
6. 代码利用率高
7. 迭代效率高
