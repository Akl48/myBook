[TOC]

## Git的使用

**Git跟踪并管理的是修改**

[廖雪峰老师官网](<https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000>)

[简单易懂版](https://rogerdudler.github.io/git-guide/index.zh.html)

### 新建仓库

1. 通过`git init`将这个目录变成**Git**可以管理的仓库
   1. 新建好了就会提示试一个空的仓库`Initialized empty Git repository in`
2. 新建之后虽然是空的但是有一个**.git** 的隐藏目录来跟踪管理版本库

### 从Git上下载东西

通过`git clone + git的链接`将远程仓库clone下来

### 将本地的仓库同步到git上

GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。

* 关联Git仓库`git remote add origin`

### 上传到Git

1. 添加要更改的文件到暂存区 `git add 文件名`

2. 提交改动到HEAD但是还没有到你的远程仓库 `git commit -m "message"`
3. (第一次)关联到git上`git remote add origin https://github.com/Akl48/mybook.git`
4. push到远程仓库上`git push -u origin master`

#### 寻找git的commit id

`git reflog`会记录你的每一次命令，通过这个可以从过去回到未来

### 删除Git文件

通过`git rm`可以删除文件也可以通过`-r`删除全部文件夹

### 查看Git的修改

> commit d1635c35b894ed4f48dc04c29ae0bcf7f93d0f46
>
> Author: 周天荣 <zhoutianrong@JOJO.local>
>
> Date:   Mon Apr 22 15:34:22 2019 +0800

可以通过`git log`查看最近的改动记录 可以加上参数选择`--pretty=oneline`也可以加上`--author=bob`查看作者

* 里面的commit id是通过SHA1计算的16进制的数
* `git log --graph `可以查看改动的图形状态

### 查看文件的修改

`git status`命令用于显示工作目录和暂存区的状态。使用此命令能看到那些修改被暂存到了, 哪些没有, 哪些文件没有被Git tracked到。

* 通过`git diff`可以查看更改的内容

### 撤销文件修改

1. `git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销。可以将这个文件回到最近一次**git commit**或者**git add**的状态
   1. `git checkout`用版本库中的版本替换工作区的版本，什么都可以一键**还原**

2. 用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区 后面的是版本号
   1. 通过`git reset --hard HEAD^`将git的版本返回上一个版本，^代表了一次回退次数如果变多则为~**100** 可以回到过去

### 更新本地仓库

`git pull`更新本地仓库到最新

### 分支管理

git上的分支类似于平行宇宙，对现在没有影响，但是在某个时间点分支汇总了，两部分工作都完成了。创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作

* 除非将分支推送到远程仓库，不然分支都是不为别人所见的

#### 创建以及合并分支

每次提交Git都将他们串成一串时间线，这条时间线就是一条**分支**，截止目前只有一条时间线，在Git中这个分支叫主分支，即**master**分，而HEAD严格的说也不是指向提交。而是指向master，**master**才是**指向提交**，**HEAD是指向当前分支**。

* 每次提交master都向前移动，**master分支**也越来越长。

1. 创建新的分支，例如dev。
   1. Git**新建一个dev指针**，指向和master相同的commit，再将**HEAD指向dev**就表示分支在dev上
   2. 在更改HEAD之后对工作区的修改和提交就是针对dev分支之后只有**dev指针**移动，**master不动**
   3. 当我们的dev完成之后，通过将master指向dev当前提交就完成了合并
   4. 合并完之后删除dev分支，之后就只剩一条分支了
2. 使用过程
   1. `git checkout -b dev` `git checkout`加上`-b`表示创建并且切换
      1. 等价于`git branch dev`+`git checkout dev` 创建+切换
   2. 查看当前分支`git branch`会在当前分支前面+`*`
   3. 之后就是正常commit再讲dev分支的工作合并到master
      1. `git merge dev` 通过`git merge`合并指定分支到当前分支
   4. 合并之后删除dev分支`git branch -d dev`

#### 解决分支冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

#### 分支管理策略



### 标签

⚠️**标签**一般都和**commit相关**

#### 新建标签

- 命令`git tag <tagname>`用于新建一个标签，默认为`HEAD`，也可以指定一个commit id；
- 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；
- 命令`git tag`可以查看所有标签。

#### 操作标签

- 命令`git push origin <tagname>`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tagname>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

## GitBook的使用

### GitBook的安装

`npm install -g gitbook-cli`通过**npm**指令来安装GitBook

* npm是Node.js自带的

### 新建Gitbook

`gitbook init`

1. 在指定的目录新建gitbook

2. 新建之后会有两个文件
   1. README.md 
   2. SUMMARY.md gitbook的目录

### 浏览书

 `gitbook serve`通过gitbook来在网页上显示书籍--[默认的IP地址http://localhost:4000](http://localhost:4000) 

执行命令之后会对Markdown格式文件进行转化为html格式 

###  构建书籍

`gitbook build ([数据路径][输出路径])`构建新的gitbook