---
title: Zsh美化经验
date: 2024-12-02T03:34:26.810Z
---

# 参考内容
1. [文章](https://www.sitepoint.com/zsh-tips-tricks/)
2. [geekhour视频](https://www.bilibili.com/video/BV1kw411z7SV/)

# 1. 前提步骤-安装zsh并启用

安装：根据不同distro的命令进行安装，某些distro会自带甚至默认是zsh──例如manjaro。下面以debian为例，Debian使用的是apt管理器因此使用apt相关命令
```bash
sudo apt install zsh -y
```

启用
```bash
chsh -s /bin/zsh
#之后prompt会询问当前用户密码以确保有sudo身份进行更换
```

注：zsh的路径可能不仅限于/bin/zsh，可通过cat /etc/shells查找对应的zsh目录，例如/usr/bin/zsh


# 2. 安装oh-my-zsh

oh my zsh是目前最为流行和方便的一个zsh美化插件，在美化zsh的同时也能简化一些操作步骤并且提升一定的效率。

安装方式一般通过curl或者wget两个命令行网络下载工具进行安装
```bash
#使用Curl的方式:
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
```bash
#使用wget的方式:
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

# 3. 更改oh-my-zsh主题

[oh-my-zsh自带主题](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes )

在.zshrc文件中，找到ZSH_THEME=""条目，在""中填入自己需要的theme。此外还有一些需要额外安装的主题，参考:[其他主题列表](https://github.com/ohmyzsh/ohmyzsh/wiki/External-themes)

我目前使用的是参考文章当中推荐的powerlevel10k：安装方式请根据自己的发行版版本找到下述文档当中的[具体说明](https://github.com/romkatv/powerlevel10k#installation)



# 4. 插件安装

自动填充插件，运行下列命令之后，在.zshrc文件中找到plugins=()区域，将zsh-autosuggestions添加到括号中，注意：plugins括号中的插件用空格区分
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

这是另一个插件，用于高亮命令行中的命令部分语法，如果命令可用显示为绿色，不可用显示为红色，非常有助于命令操作，避免打错字造成效率降低。

https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md

其他插件则可以直接在.zshrc文件中的plugins=()的括号内进行添加

最后安装的插件有:aliases battery git sudo vscode z zsh-autosuggestions

# 5. 一些zsh自带的快捷键




| 按键组合| 用途 |
| --- | --- |
| Ctrl+U | 删除从光标位置到行首的内容 |
|Ctrl+K|  删除从光标位置到行尾的内容|
|Ctrl+W|删除从光标位置到第一个单词首位字符的内容|
|ALT+D|删除从光标到下一个单词的内容|

