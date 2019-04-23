[TOC]

# CocoaPods

CocoaPods是开发OSX和iOS应用程序的一个**第三方库的依赖管理工具**。通过CocoaPods，可以定义自己的依赖关系(pods)，并且在整个开发环境中对第三方库的版本管理非常方便。由于在工程中引入第三方框架，要配置build phases 和linker flags过程，而CocoaPods则简化了这个过程。

### 使用

通过命令行进入在你要使用的项目中使用`pod init`自动回生成一个**Podfile**文件，通过vim进入并且更改，将你要使用的第三方库的名字按照格式输入。再退出vim，再`pod install`安装库。

### 组件

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

### pod install过程

```
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
3. 加载源文件
4. 生成Pods.xcodeproj
5. 安装第三方库
6. 写入磁盘
7. 

### 原理

1. 它是将所有的依赖库都放到另一个名为 Pods 项目中 

2. Pods 项目最终会编译成一个名为 libPods.a 的文件，主项目只需要依赖这个 .a 文件即可。这样，依赖库源码管理工作都从主项目移到了 Pods 项目中。 

3. 对于资源文件，CocoaPods 提供了一个名为 Pods-resources.sh 的 bash 脚本，该脚本在每次项目编译的时候都会执行，将第三方库的各种资源文件复制到目标目录中。 

4. CocoaPods 通过一个名为 Pods.xcconfig 的文件来在编译时设置所有的依赖和参数。 

