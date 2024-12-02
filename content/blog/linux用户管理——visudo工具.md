---
title: Linux用户管理——visudo工具
date: 2024-12-02T03:54:09.383Z
---

Linux默认情况下会限制显示输入的密码，也就是说用户不会知道是否键入了密码，也不知道已经输入密码的位数，这一特征来源于Unix，可以通过一些办法来修改。

- Visudo工具

使用root权限编辑root用户相关的一些设置

```bash
sudo visudo
```
这一行：
```bash
...
Defaults env_reset
...
```


修改这一行的内容
```bash
Defaults env_reset,pwfeedback,timestamp_timeout=15,passwd_tries=5
```
<!-- more -->
解释
- pwfeedback——显示键入密码的位数
- timestamp_timeout=15——超过15分钟自动中断连接
- passwd_tries=5——尝试密码次数从默认的3次改为5次

visudo工具会根据不同的发行版选择不同的编辑工具，有的是vim，有的是nano，更改这一编辑器的方式：
```bash
sudo update-alternatives --config editor
```
该命令会给出一系列编号，选择对应编号的编辑器即可，例如：
![image.png](https://cloudflare-imgbed-p1r.pages.dev/file/1731456996664_image.png)
输入2则会选择vim作为visudo的编辑器