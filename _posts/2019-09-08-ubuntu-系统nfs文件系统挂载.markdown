---
layout: post
title: ubuntu18.04系统nfs文件系统挂载
tags: [ubuntu, nfs, mount]
---

### 服务器安装配置NFS
1. 安装NFS<br/>
终端输入以下命令
```
sudo apt install nfs-kernel-server
```

2. 配置NFS<br/>
root权限打开/etc/exports添加要共享的目录和允许被挂载的服务器IP
```
sudo vim /etc/exports
/home/user/kgduan 192.168.1.*(rw, sync, no_root_squash, no_subtree_check)
```

3. 重启NFS
```
sudo /etc/init.d/nfs-kernel-server restart
```

### 客户端安装配置NFS
4. 安装NFS<br/>
终端输入以下命令
```
sudo apt install nfs-kernel-server
```
5. 新建要与服务器挂载的目录
```
mkdir -p /mnt/nfs
```
6. 挂载
```
mount -t nfs -o nolock 192.168.1.123:/home/user/kgduan /mnt/nfs
```
7. 若要开机自动挂载，执行以下指令，添加以下内容
```
sudo vim /etc/fstab
192.168.1.123:/home/user/kgduan /mnt/nfs rw 0 0
```