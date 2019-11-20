---
layout: post
title: ubuntu18.04系统多个Git账号配置
tags: [ubuntu, Git]
---

### 使用ssh分别生成每个账号的key

> 以其中一个账号为例

{% highlight shell %}
ssh-keygen -t rsa -C "876706485@qq.com"
{% endhighlight %}

输完命令后会要求设置key的保存文件路径，建议每个账号分别新建一个文件

{% highlight shell %}
Enter file in which to save the key (/home/user/.ssh/id_rsa): /home/user/.ssh/id_rsa_github
{% endhighlight %}

### 将生成的key分别添加到对应的github

github个人设置中有`SSH and GPG keys`选项，将生成的key拷贝到此处

### 备份原始gitconfig文件

如果存在原始gitconfig文件，将其备份

{% highlight shell %}
mv ~/.gitconfig ~/.gitconfig.bak
{% endhighlight %}

### 配置ssh的config

touch ~/.ssh/config，在config中添加所有git

{% highlight plaintext %}
# GitLib
Host git-server
        HostName 192.168.2.11
        port 22
        User git
        PreferredAuthentications publickey
        IdentityFile ~/.ssh/id_rsa

# GitHub
Host github.com
        HostName github.com
        User 876706485@qq.com
        IdentityFile ~/.ssh/id_rsa_github
{% endhighlight %}
### 将私钥交给agent管理

> 这样可以避免重复输入密码，也无需重复部署私钥

{% highlight shell %}
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa_github
{% endhighlight %}

### 测试ssh配置是否成功

{% highlight shell %}
ssh -T git@github.com
{% endhighlight %}

### clone repo

下载库时切换到`clone with ssh`，clone之后配置`user.email`和`user.name`，注意，不要添加`--global`选项，这样配置的邮箱和用户名只在当前repo生效，然后就可以修改源码并且push了

{% highlight shell %}
git clone git@github.com:vicwer/leetcode.git
git config user.email "100000@qq.com"
git config user.name vicwer

git add -A
git commit -m ""
git push

git clone git@gitlab.com:kgduan/proj.git
git config user.email "kgduan@company.com"
git config user.name kgduan

git add -A
git commit -m ""
git push
{% endhighlight %}
