---
layout: post
title:  "Welcome to React Native!"
date:   2016-09-01 01:23:32
categories: React Native
---

# 搭建开发环境

#### Homebrew

Homebrew, Mac系统的包管理器，用于安装NodeJS和一些其他必需的工具软件。


在终端输入:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
终端出现:

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

接下来他会让你输入密码,输入密码之后，终端出现:

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

终端出现:

```
==> Downloading and installing Homebrew...
```

然后过了一会 就在我以为大功告成的时候 
终端出现:

```
fatal: unable to access 'https://github.com/Homebrew/brew/': SSLRead() return error -9806
Failed during: git fetch origin master:refs/remotes/origin/master -n --depth=1
```

鬼知道什么东西啊！！！！！
但是，我觉得这个80%网络问题和那道墙有关，于是我开启VPN代理试一试

oh，my god ！

终端出现:

```
It appears Homebrew is already installed. If your intent is to reinstall you
should do the following before running this installer again:
    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
The current contents of /usr/local are bin Cellar etc Frameworks Homebrew include lib mysql mysql-5.7.14-osx10.11-x86_64 opt sbin share var
```

可能是因为我刚才没安装成功导致我只能卸载再重新安装吧

可是要怎么卸载呢！！！！！

执行下面3到终端命令
在终端输入:

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

在终端输入:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
然后他就会下载啦，下载很慢100K/s，但是文件也不算很大

直到终端出现:

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

注：在Max OS X 10.11（El Capitan)版本中，homebrew在安装软件时可能会碰到/usr/local目录不可写的权限问题。可以使用下面的命令修复：

```
sudo chown -R `whoami` /usr/local
```

#### Node

使用Homebrew来安装Node.js.
React Native需要NodeJS 4.0或更高版本。

在终端输入:

```
brew install node
```

VPN代理不能断啊！！！！

终端出现:

```
==> Downloading https://homebrew.bintray.com/bottles/node-6.5.0.yosemite.bottle.
######################################################################## 100.0%
==> Pouring node-6.5.0.yosemite.bottle.tar.gz
==> Using the sandbox
==> Caveats
Please note by default only English locale support is provided. If you need
full locale support you should either rebuild with full icu:
  `brew reinstall node --with-full-icu`
or add full icu data at runtime following:
  https://github.com/nodejs/node/wiki/Intl#using-and-customizing-the-small-icu-build

Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/node/6.5.0: 3,829 files, 41.4M
```

#### React Native的命令行工具（react-native-cli）

React Native的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。

在终端输入:

```
npm install -g react-native-cli
```
此时会出现进度条，待进度条走完，终端会出现：

```
/usr/local/bin/react-native -> /usr/local/lib/node_modules/react-native-cli/index.js
/usr/local/lib
└─┬ react-native-cli@1.0.0 
  ├─┬ chalk@1.1.3 
  │ ├── ansi-styles@2.2.1 
  │ ├── escape-string-regexp@1.0.5 
  │ ├─┬ has-ansi@2.0.0 
  │ │ └── ansi-regex@2.0.0 
  │ ├── strip-ansi@3.0.1 
  │ └── supports-color@2.0.0 
  ├── minimist@1.2.0 
  ├─┬ prompt@0.2.14 
  │ ├── pkginfo@0.4.0 
  │ ├─┬ read@1.0.7 
  │ │ └── mute-stream@0.0.6 
  │ ├── revalidator@0.1.8 
  │ ├─┬ utile@0.2.1 
  │ │ ├── async@0.2.10 
  │ │ ├── deep-equal@1.0.1 
  │ │ ├── i@0.3.5 
  │ │ ├─┬ mkdirp@0.5.1 
  │ │ │ └── minimist@0.0.8 
  │ │ ├── ncp@0.4.2 
  │ │ └─┬ rimraf@2.5.4 
  │ │   └─┬ glob@7.0.6 
  │ │     ├── fs.realpath@1.0.0 
  │ │     ├─┬ inflight@1.0.5 
  │ │     │ └── wrappy@1.0.2 
  │ │     ├── inherits@2.0.3 
  │ │     ├─┬ minimatch@3.0.3 
  │ │     │ └─┬ brace-expansion@1.1.6 
  │ │     │   ├── balanced-match@0.4.2 
  │ │     │   └── concat-map@0.0.1 
  │ │     ├── once@1.4.0 
  │ │     └── path-is-absolute@1.0.0 
  │ └─┬ winston@0.8.3 
  │   ├── colors@0.6.2 
  │   ├── cycle@1.0.3 
  │   ├── eyes@0.1.8 
  │   ├── isstream@0.1.2 
  │   ├── pkginfo@0.3.1 
  │   └── stack-trace@0.0.9 
  └── semver@5.3.0 
```

**如果你看到EACCES: permission denied这样的权限报错，那么请参照上文的homebrew译注，修复/usr/local目录的所有权**

```
sudo chown -R `whoami` /usr/local
```


#### Xcode 

React Native目前需要Xcode 7.0 或更高版本。你可以通过App Store或是到Apple开发者官网上下载。这一步骤会同时安装Xcode IDE和Xcode的命令行工具。

虽然一般来说命令行工具都是默认安装了，但你最好还是启动Xcode，并在Xcode | Preferences | Locations菜单中检查一下是否装有某个版本的Command Line Tools。Xcode的命令行工具中也包含一些必须的工具，比如git等。

## 推荐安装的工具
#### Watchman
Watchman是由Facebook提供的监视文件系统变更的工具。安装此工具可以提高开发时的性能（packager可以快速捕捉文件的变化从而实现实时刷新）

 在终端输入:
 
 ```
 brew install watchman
 ```

终端会出现

```
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
gdm

==> Installing dependencies for watchman: pcre
==> Installing watchman dependency: pcre
==> Downloading https://homebrew.bintray.com/bottles/pcre-8.39.yosemite.bottle.tar.gz
######################################################################## 100.0%
==> Pouring pcre-8.39.yosemite.bottle.tar.gz
🍺  /usr/local/Cellar/pcre/8.39: 203 files, 5.4M
==> Installing watchman
==> Downloading https://homebrew.bintray.com/bottles/watchman-4.6.0.yosemite.bottle.tar.gz
######################################################################## 100.0%
==> Pouring watchman-4.6.0.yosemite.bottle.tar.gz
==> Using the sandbox
🍺  /usr/local/Cellar/watchman/4.6.0: 21 files, 397.1K
```

Watchman 安装完成 !!

#### Flow
Flow是一个静态的JS类型检查工具。译注：你在很多示例中看到的奇奇怪怪的冒号问号，以及方法参数中像类型一样的写法，都是属于这个flow工具的语法。这一语法并不属于ES标准，只是Facebook自家的代码规范。所以新手可以直接跳过（即不需要安装这一工具，也不建议去费力学习flow相关语法）。

在终端输入:

```
brew install flow
```

终端会出现:

```
==> Downloading https://homebrew.bintray.com/bottles/flow-0.32.0.yosemite.bottle.tar.gz
######################################################################## 100.0%
==> Pouring flow-0.32.0.yosemite.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d

zsh completion has been installed to:
  /usr/local/share/zsh/site-functions
==> Summary
🍺  /usr/local/Cellar/flow/0.32.0: 7 files, 4.8M
```

Flow 安装完成 !!

#### Nuclide
[Nuclide](https://nuclide.io)（此链接需要科学上网）是由Facebook提供的基于atom的集成开发环境，可用于编写、[运行](https://nuclide.io/docs/platforms/react-native/)和 [调试](https://nuclide.io/docs/platforms/react-native/)React Native应用。

点击这里阅读[Nuclide的入门文档](https://nuclide.io/docs/quick-start/getting-started/)。

译注：推荐使用[WebStorm](https://www.jetbrains.com/webstorm/)或[Sublime Text](http://www.sublimetext.com)来编写React Native应用。

## 测试安装

在终端输入:

```
react-native init testProject
```
此时，会创建应用，终端会出现:

```
This will walk you through creating a new React Native project in /Users/Tianbai/testProject
Installing react-native package from npm...
Setting up new React Native app in /Users/Tianbai/testProject
testProject@0.0.1 /Users/Tianbai/testProject
└── react@15.3.1 

To run your app on iOS:
   cd /Users/Tianbai/testProject
   react-native run-ios
   - or -
   Open /Users/Tianbai/testProject/ios/testProject.xcodeproj in Xcode
   Hit the Run button
To run your app on Android:
   Have an Android emulator running (quickest way to get started), or a device connected
   cd /Users/Tianbai/testProject
   react-native run-android
```
此时就生成了 `android` `iOS` 两端程序

![](/assets/postImages/ios/Welcome to React Native/QQ20160909-0@2x.png)

然后在终端输入:

```
cd AwesomeProject
```

```
react-native run-ios
```

终端出现:

```
Found Xcode project testProject.xcodeproj
Could not find service "com.apple.CoreSimulator.CoreSimulatorService" in domain for uid: 501
2016-09-09 23:39:50.818 simctl[6335:101682] launchctl print returned an error code: 28928
2016-09-09 23:39:50.818 simctl[6335:101682] Failed to locate a valid instance of CoreSimulatorService in the bootstrap.  Adding it now.
Launching iPhone 6 (9.2)...
Building using "xcodebuild -project testProject.xcodeproj -scheme testProject -destination id=3D9BE68A-5DDF-4081-BAA0-E5D66D28E37D -derivedDataPath build"
User defaults from command line:
    IDEDerivedDataPathOverride = /Users/Tianbai/testProject/ios/build
```

这时候模拟器启动

终端出现实时build 状态


![](/assets/postImages/ios/Welcome to React Native/QQ20160909-1@2x.png
)

模拟器上会出现:

![](/assets/postImages/ios/Welcome to React Native/Untitled.gif)

## 修改项目
现在你已经成功运行了项目，使用你喜欢的编辑器打开index.ios.js并随便改上几行。


## 结束语

你已经成功运行并修改了你的第一个React Native应用。

