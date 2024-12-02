---
title: PVE虚拟机迁移至本地VHDX
date: 2024-12-02T03:52:33.265Z
---

# 背景
当时遇到的情况是在Promox 机器上的两个Windows LTSC虚拟机，Promox的主机需要做别的用途，因此需要将这两个在使用当中的Windows LTSC虚拟机迁移到一台实体的Windows宿主机上，并用Windows自带的HYPER-V虚拟机管理器来导入虚拟机并正常使用。
![image.png](https://cloudflare-imgbed-p1r.pages.dev/file/1731372178850_image.png)


实际操作当中需要用到的简化步骤是将硬盘文件格式进行两次转换，这样在Windows宿主机上可以直接导入VHDX格式的虚拟机文件运行。
![image.png](https://cloudflare-imgbed-p1r.pages.dev/file/1731372336778_image.png)

# 步骤拆解
## 1. 将目标VM进行导出
- 在目标虚拟机对应的LVM控制器中，找到虚拟机硬盘编号，例如：vm-141-disk-0
![image.png](https://cloudflare-imgbed-p1r.pages.dev/file/1731372707943_image.png)
- 进入所在节点的总console，通过命令行的来找到对应的**DM**

下方代码中要找到vm-141-disk-0所对应的DM
```bash
find / -name vm-141-disk-0
/dev/vm-141-disk-0
ls -l /dev/vm-141/disk-0
lrwxrwxrwx 1 root root 7 Nov 15 14:35 /dev/pve/vm-141-disk-0 -> ../dm-6
```

上述代码中，第一个命令find找到vm-141-disk-0所在的位置，给出的结果是/dev/vm-141-disk-0，可以理解为这是虚拟机141在宿主机上的挂载文件。

第二个命令要通过ls -l选项找出该文件所真实指向的dm——device mapper文件。

这一步的命令总结为
```bash
find / -name [vmdiskNumber]
[mountpoint/linkfile]
ls -l [mountpoint/linkfile]
.... -> ../[dmfile]
```

- 导出DM为硬盘文件
继续在该节点的总console命令行中，执行下列命令
```bash
qemu-img convert -f raw -O vmdk /dev/[dmfile] [targetpath/targetfilename].vmdk
```
以上述的vm141为例：
```bash
qemu-img convert -f war -O vmdk /dev/dmfile
```

至此dm文件导出完成
## 2. 下载DM文件
通过mobaXterm、WinSCP、windterm、tabby等带有文件传输功能的终端工具，连接到总节点之后，切换到上述步骤中的targetpath中，然后下载导出完成的vmdk文件到本地即可。

## 3. 转换VMDK文件至vhdk
- 下载qemu-img工具
下载地址：https://cloudbase.it/qemu-img-windows/
- 解压qemu-img工具
- 执行转换
将下载到本地的vmdk文件放到解压之后的qemu-img工具目录下，在该目录下打开powershell并以管理员权限运行，之后执行命令：
```powershell
qemu-img.exe convert [filename].vmdk -O vhdx [filepaht\filename].vhdx
```

## 4. 导入vhdx文件至hyper-v中
打开hyper-v管理器，导入即可。