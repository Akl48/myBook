# Git&Cocoapods

## Git

**Git是目前世界上最先进的分布式版本控制系统**并且它是Linux之父写的

1. 安装Git
   1. homebrew安装
      1. 安装homebrew `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
      2. 在homebrew安装遭遇`curl: (7) Failed to connect to raw.githubusercontent.com port 443: Operation timed out`
         1. 很大一部分的概率是网络的问题 这个时候将你的shodowsocks换成全局版（推荐）|换成手机热点（速度很慢 **不推荐**）
            1. 目前还是同样的问题，不好解决
      3. `brew install git`
   2. 直接在官网下载git的安装包
      1. [下载](<https://git-scm.com/downloads>)

## Cocoapods

CocoaPods是iOS项目的**依赖管理工具**，该Cocoapods项目源码在Github上管理。在开发iOS项目的时候不可避免的要使用第三方库，Cocoapods的出现可以使我们可以节省配置和更新第三方库的时间。

1. 升级Ruby下的gem`sudo gem update --system`
2. 在终端输入`sudo gem install cocoapods`安装cocoapods
   1. 如果有权限问题`sudo gem install -n /usr/local/bin cocoapods`
3. 之后`pod setup`直到安装成功[CocoaPods/Specs](https://github.com/CocoaPods/Specs)这个仓库可以直接在Github下载
   1. 安装到了~/.cocoapods
