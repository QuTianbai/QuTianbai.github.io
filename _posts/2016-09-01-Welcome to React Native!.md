---
layout: post
title:  "Welcome to React Native!"
date:   2016-09-01 01:23:32
categories: React Native
---

# 搭建开发环境

#### Homebrew

Homebrew, Mac系统的包管理器，用于安装NodeJS和一些其他必需的工具软件。


在终端输入：

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
终端上会出现

```
==> This script will install:
==> The following new directories will be created:
```

```
Press RETURN to continue or any other key to abort
```
输入任意键

```
==> /usr/bin/sudo /bin/mkdir -p /usr/local/Homebrew
```

接下来他会让你输入密码,输入密码之后，会出现

```
==> /usr/bin/sudo /bin/chmod g+rwx /usr/local/Homebrew
==> /usr/bin/sudo /bin/chmod u+rwx share/zsh share/zsh/site-functions
==> /usr/bin/sudo /usr/sbin/chown Tianbai /usr/local/Homebrew
==> /usr/bin/sudo /usr/bin/chgrp admin /usr/local/Homebrew
==> /usr/bin/sudo /bin/mkdir -p /Users/Tianbai/Library/Caches/Homebrew
==> /usr/bin/sudo /bin/chmod g+rwx /Users/Tianbai/Library/Caches/Homebrew
==> /usr/bin/sudo /usr/sbin/chown Tianbai /Users/Tianbai/Library/Caches/Homebrew
```

开始下载 `Homebrew`

```
==> Downloading and installing Homebrew...
```

然后过了一会 就在我以为大功告成的时候  出现了

```
fatal: unable to access 'https://github.com/Homebrew/brew/': SSLRead() return error -9806
Failed during: git fetch origin master:refs/remotes/origin/master -n --depth=1
```

鬼知道什么东西啊！！！！！
但是，我觉得这个80%网络问题和那道墙有关，于是我开启代理试一试

oh，my god ！

又出现了：

```
It appears Homebrew is already installed. If your intent is to reinstall you
should do the following before running this installer again:
    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
The current contents of /usr/local are bin Cellar etc Frameworks Homebrew include lib mysql mysql-5.7.14-osx10.11-x86_64 opt sbin share var
```

可能是因为我刚才没安装成功导致我只能卸载再重新安装吧

可是要怎么卸载呢！！！！！

执行下面3到终端命令

```
sudo rm -rf /usr/local/.git
```
```
rm -rf Library .git .gitignore bin/brew README.md share/man/man1/brew
```
```
rm -rf ~/Library/Caches/Homebrew
```

然后再到 `/usr/local/Homebrew` 的 `Homebrew` 文件夹删了

重新执行安装命令

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
然后他就会下载啦，下载很慢100K/s，但是文件也不算很大

直到出现

```
remote: Counting objects: 1037, done.
remote: Compressing objects: 100% (927/927), done.
remote: Total 1037 (delta 93), reused 644 (delta 68), pack-reused 0
Receiving objects: 100% (1037/1037), 1.04 MiB | 6.00 KiB/s, done.
Resolving deltas: 100% (93/93), done.
From https://github.com/Homebrew/brew
 * [new branch]      master     -> origin/master
HEAD is now at aaf8c41 Merge pull request #899 from MikeMcQuaid/diagnostic-duplicate-remove
==> Tapping homebrew/core
Cloning into '/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core'...
remote: Counting objects: 3725, done.
remote: Compressing objects: 100% (3612/3612), done.
remote: Total 3725 (delta 15), reused 1664 (delta 4), pack-reused 0
Receiving objects: 100% (3725/3725), 2.89 MiB | 779.00 KiB/s, done.
Resolving deltas: 100% (15/15), done.
Checking connectivity... done.
Tapped 3603 formulae (3,752 files, 9.0M)
Already up-to-date.
==> Installation successful!
==> Next steps
Run `brew help` to get started
Further documentation: https://git.io/brew-docs
==> Homebrew has enabled anonymous aggregate user behaviour analytics
Read the analytics documentation (and how to opt-out) here:
  https://git.io/brew-analytics 
```

`Homebrew` 就安装完成了

译注：在Max OS X 10.11（El Capitan)版本中，homebrew在安装软件时可能会碰到/usr/local目录不可写的权限问题。可以使用下面的命令修复：

```
sudo chown -R `whoami` /usr/local
```

