---
title: WSL常见错误
date: 2024-12-02T04:06:22.223Z
---

多次遇到这个问题了：
```powershell
WslRegisterDistribution failed with error: 0x80004005
```

这个错误常见于下载WSL之后点击进入安装过程的时候。

之前在PC上的WINDOWS11中尝试过重新开关虚拟化组件以及WSL组件解决，这次在公司笔记本上安装kali-linux又遇到了一样的报错，用重新开关组件的办法并不能解决，求助于copilot。它给出的方案是更新WSL这个组件本身：

以管理员身份运行powershell/Windows终端

```powershell
wsl --update
```
待更新过程结束之后，再尝试安装WSL对应的DISTRO，目前解决了问题。


<!-- more -->

Copilot还给出了其他的解决方案：

1. 重启电脑——我从没遇到过重启能解决WSL这个问题的情况
2. 更改WSL的服务设置：

    在运行当中输入services.msc -> 找到LxssManager -> 将其启动方式设置为自动
3. 重置遇到问题的distro：

同样以管理员身份运行Powershell/Windows终端

```powershell
wsl --unregister [distroName]
wsl --install -d [distroName]
```

不过我遇到的情况都是安装空的DISTRO会出现这个报错，估计也不是特别适用。
4. 更改WSL版本到最新版

由于我自己的习惯是在开启WSL之前都会将版本设置成第二代，所以这个方法对我的情况而言不适用：

同样以管理员身份运行Powershell/Windows终端
```powershell
wsl --set-version [distroName] 2
```

