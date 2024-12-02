---
title: Promox PVE 安装OpenWrt虚拟机
date: 2024-12-02T03:50:45.278Z
---

# 背景
OpenWRT作为开源的路由器固件/系统因其极高的可定制型以及各种功能广受好评，因此也被许多软路由设备采用，作为软路由设备其实占用的资源并不多，也就可以通过PVE的虚拟技术安装一个openwrt的虚拟机来当做软路由。

# 思路
部署的思路极其直白简单，在PVE上，上传PVE的镜像，直接新建一个虚拟机用作OPENWRT软路由设备即可。

# 操作
唯一需要注意的是在PVE上安装OpenWRT的虚拟机有些设置和正常的虚拟机操作系统不同，请参看下面的详细步骤，本文来源于自身操作的过程以及一位[OPENWRT玩家给出的经验](https://www.luyouqi.com/shezhi/3062.html)。感谢社区的分享！


## 1. 下载OPENWRT镜像
以23.05为例，[下载地址](https://downloads.openwrt.org/releases/22.03.5/targets/x86/64/openwrt-22.03.5-x86-64-generic-ext4-combined.img.gz)

[网页](https://downloads.openwrt.org/releases/22.03.5/targets/x86/64/)

更改对应版本号或者上级菜单可以切换不同版本，用于PVE虚拟环境的选择X86即可
下载完的文件为.GZ格式，使用7ZIP或者linux的gzip等命令行工具解压.IMG文件即可

## 2. 在PVE上上传镜像
在PVE环境中，选择集群内的LOCAL资源，点击ISO images，点击上传，选择解压完成的.IMG文件完成上传。

![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110164641.png)

## 3. 在VMM终端找到该镜像的绝对路径
选择总资源池，点击SHELL终端，输入命令：
```bash
find / -name openwrt*.img
```

- 注意：为避免冲突，可以输入完整的openwrt镜像文件文件名，如openwrt-22.03.5-x86-64-generic-ext4-commbined.img

## 4. 在PVE资源池中创建虚拟机
点击总资源池，点击创建VM
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110164759.png)
输入虚拟机的名称，如图中所示openwrt-23.05;选择VM ID或让系统自动生成，请记住该ID用于下面步骤的操作；选择所在资源池，进入下一步

- 这一步一定要选择Do not use any media，否则会自动加载系统
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110164833.png)

下一步的system中显卡选择默认即可，接下来选择hard disk，分配任意值皆可，在后续步骤中将删除该硬盘。CPU选择1socket1core也足够使用，memory按需求分配，1024M应该时绰绰有余的。
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110164924.png)

网络部分：可以根据实际情况分配MAC地址，或者自动默认即可
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110165000.png)

- Confirm页面请不要勾选在创建完成后开启虚拟机选项
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110165025.png)

至此，基本的安装过程已经结束，只要再设置一下OPENWRT虚拟机开机之前的设置即可
## 5. 更改openwrt虚拟机设置
### 5.1 更改openwrt虚拟机的硬件设备
选择CD驱动，点击移除
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110165147.png)
选择硬盘，点击detach，detach之后选择该硬盘点击remove按钮
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110165214.png)

![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110165227.png)

### 5.2 进入总资源池终端，更改虚拟机配置
点击到总资源池的终端按钮之后如下图输入命令，下图中的命令为：
```bash
qm importdisk [vm ID] [path of openwrt Image] [LVM location]
```

![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110165309.png)
命令参数解释：

- [vm ID]为创建时所设置的VM ID 或系统自动分配VM ID
- [Path of openwrt Image]为步骤3中命令行所获得的路径，如/var/lib/vz/template/iso/openwrt-22.03.5-x86-generic-generic-ext4-combined.img
- [LVM location]为虚拟机所在的PVE硬盘部分
--- 
重新进入虚拟机的配置页面，点击硬件，找到刚刚通过命令行添加的硬盘设备，此时该设备为unused状态，点选该硬盘然后点击Add
更改启动顺序：点击虚拟机的options标签，点击Boot order按钮，选择edit，将其他选项取消勾选，然后勾选刚刚添加的LVM 硬盘
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110165549.png)

接下来再回到虚拟机，点击开机，选择console，等到开机加载编译完毕，通过vi或者vim工具编辑/etc/config/network文件
编辑网卡内的设置——默认配置的IP 地址一般为192.168.1.1，需要根据实际情况进行变更
如IP地址，网关地址等，如果上级网络设备可以分配DHCP地址以及整套网络配置，只需要将proto部分设置成DHCP即可，不过要知道OPENWRT的网络地址更加方便OPENWRT的设置完成
![](https://raw.githubusercontent.com/vannear/olikonimgbed/main/20241110165628.png)
在网卡配置编辑完成之后，通过命令行reboot 0 或者PVE网页端gui界面重启OPENWRT虚拟机，之后OPENWRT便可开始正常使用
# 附录——OPENWRT上安装zerotier
Zerotier是一个比较成熟的异地组网的工具，可以用来在提供基础身份验证的情况下安全访问私有网络资源，适合NAS等工具，如果将内网的资源统一的节点放置在OPENWRT上，则可以省下zerotier的免费席位数量给更多的移动设备加入，也因此OPENWRT+Zerotier是一个不错的旁路由+个人资源私有内网透传的工具。具体内容参考[github上的指引](https://github.com/mwarning/zerotier-openwrt/wiki)。
## Zerotier设置的注意事项

1. 如果要设置OPENWRT做ZEROTIER的代理，必须在OPENWRT上开启masquerade的NAT模式
2. 加入zerotier的网络之后必须重启openwrt设备才能生效
3. 记得开启防火墙上允许zerotier流量的设置
参考网页中有明确提示如何操作