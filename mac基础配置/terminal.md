[toc]

# mac终端设置
## 将用户名~前面的计算机名改成自定义的
`sudo scutil --set HostName 自定义名`
之后重新启动终端
## 将shell编译器由默认的bash改为zsh
1. 查看当前设配上安装的shell
    * `cat /etc/shells`
2. 更改mac默认的shell
   1. `chsh -s /bin/zsh`
3. 重新启动shell
4. 切换回原本的shell
   1. `chsh -s /bin/bash`
## 自定义强劲终端
[转载自以乐之名](https://www.cnblogs.com/kenz520/p/8259432.html)
### 安装oh-my-zsh
1. 通过curl安装
   * `sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`
2. 安装之后配置zsh
   * `vim ~/.zshrc`
   * ZSH_THEME="agnoster"可更改主题为agnoster
### 自动提示和命令补全
1. `git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions` 在git上把将zsh-autosuggestions拷下来
2. 通过vim编辑zsh配置
   1. `vim ~/.zshrc`
   2. 将其中的plugins=(git) -> plugins=(zsh-autosuggestions git)
3. 更新
   1. `source ~/.zshrc`
###卸载oh-my-zsh
`uninstall_oh_my_zsh`