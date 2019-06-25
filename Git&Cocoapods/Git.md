[TOC]

## Git的使用

[廖雪峰老师官网](<https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000>)

[简单易懂版](https://rogerdudler.github.io/git-guide/index.zh.html)

[pro git](https://gitee.com/progit/)

**Git跟踪并管理的是修改**

### Git和SVN的差别

1. 速度git的速度远比SVN快
2. SVN是集中式管理，git是分布式
3. SVN必须联网工作，git可以本地作业

### 新建仓库

1. 通过`git init`将这个目录变成**Git**可以管理的仓库
   1. 新建好了就会提示试一个空的仓库`Initialized empty Git repository in`
2. 新建之后虽然是空的但是有一个.**git** 的隐藏目录来跟踪管理版本库

### Git的配置

全局配置git用户名和邮箱

`git config --global user.name "ZzzzT_R"`

`git config --global user.email "z1057075812@outlook.com"`

### Git中的区域

1. 工作区 除了.git的都是工作区
2. 版本库(.git) 
   1. HEAD 指向当前分支
   2. 缓存区 
   3. 分支(master) 最初的分支

### 从Git上下载东西

通过`git clone + git的链接`将远程仓库clone下来

### 将本地的仓库同步到git上

GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。

* 关联Git仓库`git remote add origin`

### 上传到Git

1. 添加要更改的文件到暂存区 `git add 文件名`
   1. `git add .`添加所有
   2. 如何跳过暂存区`git commit -a`通过-a可以跳过
2. 提交改动到HEAD但是还没有到你的远程仓库 `git commit -m "message"`
3. (第一次)关联到git上`git remote add origin https://github.com/Akl48/mybook.git`
4. push到远程仓库上`git push -u origin master`

#### 寻找git的commit id

`git reflog`会记录你的每一次命令

回退的记录也可以看到比log强，通过这个可以从过去回到未来

### 删除Git文件

通过`git rm `可以删除文件也可以通过`-r`删除全部文件夹

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

* 通过`git diff`可以查看更改的内容 ta**会使用文件补丁的格式显示具体添加和删除的行**

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

每次提交Git都将他们串成一串时间线，这条时间线就是一条**分支**，截止目前只有一条时间线，在Git中这个分支叫主分支，即**master**分支，而HEAD严格的说也不是指向提交，而是指向master，**master**才是**指向提交**，**HEAD是指向当前分支**。当我新建一个分支dev时`git branch dev`，这时会在当前commit对象上新建一个分支指针。而Head 在Git中，它是一个指向你正在工作中的本地分支的指针（译注：将 HEAD 想象为当前分支的别名）所以想要将当前的分支切换到我新建的dev上`git checkout dev`，此时Head指向dev。

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
      1. 切换当前到master线`git checkout master`
      2. `git merge dev` 通过`git merge`合并指定分支到当前分支
   4. 合并之后删除dev分支`git branch -d dev`
4. 
#### 解决分支冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

#### 分支管理策略

在`git merge`的时候通常会使用`Fast forward`模式，删除分支之后，会自动删除分支信息。所以有时候需要禁用merge模式，会在`merge`的时候生成一个全新的`commit`，可以看到分支的历史了

* 可以通过`--no-ff`的方式`merge`
  * `git merge --no-ff -m "merge no-ff" dev`
  * 由于是一个全新的`commit`所以需要带一个`message`
* 在实际开发中`merge`分支是相当稳定的，仅仅是用来发布新版本的，平时不能再上面干活，而平时都在`dev`上干活

#### bug分支

在软件开发中bug突如其来，但是在Git中的分支过于强大，可以通过一个新的临时分支来修复，修复之后合并分支，再删除。

* 但是有时候当前的分支任务没有完成的时候，需要暂时将现场保存起来
  * `git stash`
  * 在用`git status`查看工作区，就是干净的
* 假设在master上修复分支，先切换回master`git branch master`
* 再新建临时分支—修改bug—整合到当前master—删除分支
* 再切换回dev `git checkout dev`
* 在恢复dev分支
  1. `git stash apply`恢复之后stash的内容需要手动删除`git stash drop`
  2. `git stash pop`恢复的时候讲stach内容也同时删除了

#### 未来分支

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。(大写的D)

#### 多人合作时候

远程仓库默认的名字是origin

1. 首先，可以试图用`git push origin <branch-name>`推送自己的分支；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

#### rebase

`rebase`相较于`merge`而言有一个更加**整洁**的提交记录。

- rebase操作可以把**本地未push**的分叉提交历史**整理成直线**；
- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

### 标签

⚠️**标签**一般都和**commit相关**

#### 新建标签

- 命令`git tag <tagname>`用于新建一个标签，默认为最新的`commit`，也可以指定一个commit id;
- 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签附带的信息;
- 命令`git tag`可以查看所有标签。

#### 查看标签

* 通过`git show <tagname>`查看标签信息

#### 操作标签

- 命令`git push origin <tagname>`可以推送一个本地标签;
- 命令`git push origin --tags`可以推送全部未推送过的本地标签;
- 命令`git tag -d <tagname>`可以删除一个本地标签;
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