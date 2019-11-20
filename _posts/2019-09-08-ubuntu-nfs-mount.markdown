---
layout: post
title: ubuntu18.04系统nfs文件系统挂载
tags: [ubuntu, nfs, mount]
---

### 服务器安装配置NFS

- 安装NFS
终端输入以下命令
{% highlight shell %}
sudo apt install nfs-kernel-server
{% endhighlight %}

- 配置NFS
root权限打开/etc/exports添加要共享的目录和允许被挂载的服务器IP
{% highlight shell %}
sudo vim /etc/exports
/home/user/kgduan 192.168.1.*(rw, sync, no_root_squash, no_subtree_check)
{% endhighlight %}

- 重启NFS
{% highlight shell %}
sudo /etc/init.d/nfs-kernel-server restart
{% endhighlight %}

### 客户端安装配置NFS

- 安装NFS
终端输入以下命令
{% highlight shell %}
sudo apt install nfs-kernel-server
{% endhighlight %}

- 新建要与服务器挂载的目录
{% highlight shell %}
mkdir -p /mnt/nfs
{% endhighlight %}

- 挂载
{% highlight shell %}
mount -t nfs -o nolock 192.168.1.123:/home/user/kgduan /mnt/nfs
{% endhighlight %}

- 若要开机自动挂载，执行以下指令，添加以下内容
{% highlight shell %}
sudo vim /etc/fstab
192.168.1.123:/home/user/kgduan /mnt/nfs rw 0 0
{% endhighlight %}
