# Git&Cocoapods

## Git

**Git是目前世界上最先进的分布式版本控制系统**

1. 安装Git
   1. homebrew安装
      1. 安装homebrew `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)`
      2. `brew install git`
   2. 直接在官网下载git的安装包
      1. [下载](<https://git-scm.com/downloads>)

## Cocoapods

CocoaPods是iOS项目的**依赖管理工具**，该Cocoapods项目源码在Github上管理。在开发iOS项目的时候不可避免的要使用第三方库，Cocoapods的出现可以使我们可以节省配置和更新第三方库的时间。

1. 升级Ruby`sudo gem update --system`
2. 在终端输入`sudo gem install cocoapods`安装cocoapods
   1. 如果有权限问题`sudo gem install -n /usr/local/bin cocoapods`
3. 之后`pod setup`直到安装成功