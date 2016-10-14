---
layout: post
title:  "AVAudioPlayer"
date:   2016-06-23 01:23:32
categories: iOS高级
---

## 简述
AVAudioPlayer 是一个属于 `AVFoundation.framework` 的一个类，它的功能类似于一个功能强大的播放器，AVAudioPlayer 支持广泛的音频格式，主要是以下这些格式。

- AAC
- AMR (Adaptive multi-Rate，一种语音格式)
- ALAC (Apple lossless Audio Codec)
- iLBC (internet Low Bitrate Codec，另一种语音格式)
- IMA4 (IMA/ADPCM)
- linearPCM (uncompressed)
- u-law 和 a-law
- MP3 (MPEG-Laudio Layer 3)

## AVAudioPlayer 的使用

### AVAudioPlayer 初始化

1. `initWithContentsOfURL: error: ` 从URL加载音频，返回 AVAudioPlayer 对象
2. `initWithData: error:` 加载NSdata对象的音频文件，返回 AVAudioPlayer 对象

### AVAudioPlayer方法调用

1. `- (BOOL)play` 开始或恢复播放，调用该方法时，如果该音频还没有准备好，程序会隐士执行 `- (BOOL)prepareToPlay` 方法
2. `- (void)pause` 暂停
3. `- (void)stop` 停止 
4. `- (BOOL)playAtTime:(NSTimeInterval)time NS_AVAILABLE(10_7, 4_0)`  在某个时间点播放
5. `- (BOOL)prepareToPlay` 准备开始播放
6. `- (void)updateMeters` 更新音频测量值，注意如果要更新音频测量值必须设置meteringEnabled为YES，通过音频测量值可以即时获得音频分贝等信息
7. `- (float)averagePowerForChannel:(NSUInteger)channelNumber` 获得指定声道的分贝峰值，注意如果要获得分贝峰值必须在此之前调用updateMeters方法

### 使用 AVAudioPlayer 读取音频信息

1. `volume` 播放器的音频增益，值：0.0～1.0
2. `pan NS_AVAILABLE(10_7, 4_0)` 立体声设置 设为 `－1.0` 则左边播放  设为 `0.0` 则中央播放 设为 `1.0` 则右边播放
3.  `enableRate` 是否允许改变播放速率
4. `rate NS_AVAILABLE(10_8, 5_0)` 播放速率 `0.5` (半速播放) ～ `2.0`(倍速播放)   注`1.0` 是正常速度
5. `playing` 是否正在播放音频
6. `numberOfLoops` 循环次数，如果要单曲循环，设置为负数
7. `numberOfChannels` 该音频的声道次数 (只读)
8. `duration` 该音频时长
9. `currentTime` 该音频的播放点
10. `deviceCurrentTime` 输出设备播放音频的时间，注意如果播放中被暂停此时间也会继续累加
11. `url` 音频文件路径，只读
12. `data` 音频数据，只读
13. `channelAssignments` 获得或设置播放声道

### 代理方法

1. `- (void)audioPlayerDidFinishPlaying:(AVAudioPlayer *)player successfully:(BOOL)flag;` 音频播放完成
2. `- (void)audioPlayerDecodeErrorDidOccur:(AVAudioPlayer *)player error:(NSError * __nullable)error` 音频解码发生错误
3. `- (void)audioPlayerBeginInterruption:(AVAudioPlayer *)player` 如果音频被中断，比如有电话呼入，该方法就会被回调，该方法可以保存当前播放信息，以便恢复继续播放的进度

## 代码

### AVAudioPlayer 初始化


首先导入两首格式为 mp3 的歌曲，再导入歌手图片

在 ViewController.h 导入

```
#import <AVFoundation/AVFoundation.h>
#import <MediaPlayer/MediaPlayer.h>
```

然后必须持有一个 AVAudioPlayer 对象，若此对象不是属性，则无法播放

```
@property (nonatomic , strong) AVAudioPlayer *player;
```

添加控件如图所示,给控件命名

![storyboard](http://ooo.0o0.ooo/2016/06/27/57711a843e843.png)

获取制定 url 对象

```
NSURL *url = [[NSBundle mainBundle] URLForResource:@"吴亦凡-时间煮雨" withExtension:@"mp3"];
```

初始化 AVAudioPlayer 对象

```
self.player = [[AVAudioPlayer alloc]initWithContentsOfURL:url error:nil];
```

设置代理

```
self.player.delegate = self;
```

这样就播放器就初始化完成了

将storyboard里面 `开始` 按钮绑定 `- (IBAction)start:(id)sender` 方法


```
- (IBAction)start:(id)sender {
    
    if ([self.player isPlaying]) {
        [self.startBtn setBackgroundImage:[UIImage imageNamed:@"播放"] forState:0];
        [self.player pause];
    }
    else
    {
        [self.startBtn setBackgroundImage:[UIImage imageNamed:@"暂停"] forState:0];
        [self.player play];
    }
    
    if (_timer == nil) {
        _timer = [NSTimer scheduledTimerWithTimeInterval:0.01 target:self selector:@selector(updateProgress) userInfo:nil repeats:YES];
    }
    
}
```
注： 上诉代码中定义了一个 `NSTimer` 变量，因为播放器没有播放进程的委托，所以只能自定义NSTimer变量对播放器进行监控

```
@property (strong, nonatomic) NSTimer *timer;
```

将storyboard里面 `停止` 按钮绑定 `- (IBAction)stop:(id)sender` 方法

```
- (IBAction)stop:(id)sender {
    [self.player stop];
    //计时器停止
    [_timer invalidate];
    //释放定时器
    _timer = nil;
}
```

设置刚才绑定的定时器调用方法`updateProgress`

```
-(void)updateProgress{
    //进度条显示播放进度
    self.progress.progress = self.player.currentTime/self.player.duration;
    self.info1.text = [NSString stringWithFormat:@"当前播放时间%f",self.player.currentTime];
}
```

实现 `AVAudioPlayer` 的委托方法

```
- (void)audioPlayerDidFinishPlaying:(AVAudioPlayer *)player successfully:(BOOL)flag{
    
    if (player == _player && flag) {
        [self.startBtn setBackgroundImage:[UIImage imageNamed:@"播放"] forState:0];
    }
}
-(void)audioPlayerBeginInterruption:(AVAudioPlayer *)player{
    if (player == _player){
        NSLog(@"播放被中断");
    }
}

```
此时，音乐就可以播放了！！！

但是，人们使用音乐大都是后台播放，那么如何添加后台播放支持呢？

### 后台播放

在项目里 `info.plist` 右键 `open As` -- `source code` 添加代码：

```
<key>UIBackgroundModes</key>
	<array>
		<string>audio</string>
	</array>
```
 
 同样，代码里面也要添加后台播放支持
 
```
//设置锁屏仍能继续播放
[[AVAudioSession sharedInstance] setCategory: AVAudioSessionCategoryPlayback error:nil];
[[AVAudioSession sharedInstance] setActive: YES error: nil];
```

这样 音乐在后台就可以播放了！！

那么很多播放器都有一个功能，那就是在用户拔掉耳机的时候，暂停播放

### 监听播放设备

```
//添加通知，拔出耳机后暂停播放
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(routeChange:) name:AVAudioSessionRouteChangeNotification object:nil];
    
/**
 *  一旦输出改变则执行此方法
 *
 *  @param notification 输出改变通知对象
 */
-(void)routeChange:(NSNotification *)notification{
    NSDictionary *dic=notification.userInfo;
    int changeReason= [dic[AVAudioSessionRouteChangeReasonKey] intValue];
    //等于AVAudioSessionRouteChangeReasonOldDeviceUnavailable表示旧输出不可用
    if (changeReason==AVAudioSessionRouteChangeReasonOldDeviceUnavailable) {
        AVAudioSessionRouteDescription *routeDescription=dic[AVAudioSessionRouteChangePreviousRouteKey];
        AVAudioSessionPortDescription *portDescription= [routeDescription.outputs firstObject];
        //原设备为耳机则暂停
        if ([portDescription.portType isEqualToString:@"Headphones"]) {
            [self.player pause];
        }
    }
}
```

这样就能在拔掉耳机的时候，暂停播放了！！！

但是似乎还是差了一些什么，好像锁屏界面空空如也啊！！！

### 定制锁屏界面

设置后台播放时显示的东西，例如歌曲名字，图片等

要用到 `<MediaPlayer/MediaPlayer.h>` 这个库，刚才已经导入过

```
- (void)setPlayingInfo {
    //    设置后台播放时显示的东西，例如歌曲名字，图片等
    MPMediaItemArtwork *artWork = [[MPMediaItemArtwork alloc] initWithImage:[UIImage imageNamed:@"吴亦凡.jpg"]];
    
    NSDictionary *dic = @{MPMediaItemPropertyTitle:@"时间煮雨",
                          MPMediaItemPropertyArtist:@"吴亦凡",
                          MPMediaItemPropertyArtwork:artWork
                          };
    [[MPNowPlayingInfoCenter defaultCenter] setNowPlayingInfo:dic];
}
```

这样，图片就在锁屏界面显示了，不过，锁屏界面上面的三个按钮，怎么控制播放呢！！


### 接收锁屏歌曲按钮控制 (上一首，播放，下一首)

首先，你要在一个继承 `UIResponder` 类里面接收远程控制，本文以 ViewController 为例

添加如下代码，接受远程控制和取消远程控制。

```
- (void)viewDidAppear:(BOOL)animated {
    //    接受远程控制
    [self becomeFirstResponder];
    [[UIApplication sharedApplication] beginReceivingRemoteControlEvents];
}

- (void)viewDidDisappear:(BOOL)animated {
    //    取消远程控制
    [self resignFirstResponder];
    [[UIApplication sharedApplication] endReceivingRemoteControlEvents];
}
```

接收方法设置

```
- (void)remoteControlReceivedWithEvent:(UIEvent *)event {
    if (event.type == UIEventTypeRemoteControl) {  //判断是否为远程控制
        switch (event.subtype) {
            case  UIEventSubtypeRemoteControlPlay:
                if (![_player isPlaying]) {
                    [_player play];
                }
                break;
            case UIEventSubtypeRemoteControlPause:
                if ([_player isPlaying]) {
                    [_player pause];
                }
                break;
            case UIEventSubtypeRemoteControlNextTrack:
                NSLog(@"下一首");
                break;
            case UIEventSubtypeRemoteControlPreviousTrack:
                NSLog(@"上一首 ");
                break;
            default:
                break;
        }
    }
}
```

效果图：

![播放页面](http://ooo.0o0.ooo/2016/06/27/5771248a6cb54.png)
![锁屏页面](http://ooo.0o0.ooo/2016/06/27/577124bc912ad.png)


这样，播放器的基本功能就已经完成  ！！   代码多有不祥之处，时间匆忙，敬请谅解，不实之处，请多包涵！！

Demo地址：
[点此下载](https://github.com/QuTianbai/AVAudioPlayerText)


