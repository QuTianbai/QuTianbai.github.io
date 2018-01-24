---
layout: post
title:  "System Sound Services"
date:   2016-06-11  01:23:32
tag: iOS高级
---

## System Sound Services 播放音效

使用 **System Sound Services**方式只适合播放提示或者警告短小音频

> 声音长度不能超过30秒  
> 声音文件必须是 PCM 或者是 IMA4(IMA/ADPCM) 格式。  
> 必须是 .caf、.aif 、.wav 、的文件  
> 不能控制播放进度  
> 没有循环播放  

iOS使用系统声音服务来支持三种不同的通知：

1.	声音：立刻播放一个简单的声音文件。如果手机被设置为静音，用户什么也听不到
2.	提醒：播放一个声音文件，如果手机被设置为静音或震动，将通过震动提醒用户
3.	震动：震动手机，而不考虑其他设置

对于提醒音来说，与系统声音之间的差别在于，如果手机处于静音状态，提醒音将自动触发震动。提醒音的设置和用法与系统声音相同，区别在于系统声音调用 `AudioServicesPlaySystemSound`，而提醒音调用`AudioServicesPlayAlertSound`。


调用`AudioServicesCreateSystemSoundID(CFURLRef inFileURL,SystemSoundID* outSystemSoundID)` 函数注册音频文件，第一个参数代表音频文件的URL(可通过NSURL 转换为CFURLRef),迭戈参数代表注册该音频的 SystemSoundID 。

当需要播放音频的时候，调用

`AudioServicesPlaySystemSound(SystemSoundID inSystemSoundID) ` 

立刻播放一个简单的声音文件。如果手机被设置为静音，用户什么也听不到

 `AudioServicesPlayAlertSound(SystemSoundID inSystemSoundID) ` 

播放一个声音文件并震动，如果手机被设置为静音或震动(与用户在`设置`-`声音`－`响铃模式震动`、`静音模式震动`设置有关)，那么将通过无提醒或震动提醒用户！

## 代码


首先  在项目中导入一个音频文件，注意是符合上述格式的音频

再导入头文件 

```
#import <AudioToolbox/AudioToolbox.h>
```

在ViewController.m 中

```
-(void)sound{
    SystemSoundID soundID;
    //NSBundle来返回音频文件路径
    NSString *soundFile = [[NSBundle mainBundle] pathForResource:@"dingdong" ofType:@"wav"];
    //建立SystemSoundID对象，但是这里要传地址(加&符号)。 第一个参数需要一个CFURLRef类型的url参数，要新建一个NSString来做桥接转换(bridge)，而这个NSString的值，就是上面的音频文件路径
    AudioServicesCreateSystemSoundID((__bridge CFURLRef)[NSURL fileURLWithPath:soundFile], &soundID);
    //播放提示音 带震动
    AudioServicesPlayAlertSound(soundID);
    //播放系统声音
//    AudioServicesPlaySystemSound(soundID);
}
```

Demo地址：[下载](https://github.com/QuTianbai/SystemSoundServicesTest)

