---
layout: post
title:  "APNS and LocalNotification"
date:   2016-06-27 01:23:32
categories: iOS高级
---

## 简述

### UILocalNotification

> 一个UILocalNotification对象指定一个通知，一个应用程序可以在特定的日期和时间安排进行演示。操作系统负责在其预定时间提供本地通知;要做到这一点可以应用程序不必运行。虽然和通知类似，因为它们用于显示警报，播放声音和徽章应用程序图标远程通知，它们是由与本地传递，并且不需要与远程服务器连接。  

> 本地通知主要用于应用程序与基于计时器的行为和简单的日历或待办事项应用程序。即在后台运行的应用程序还可以安排本地通知，告知进入的消息，聊天，或更新的用户。一个应用程序只能有计划通知的数量有限;  

> 当您创建一个本地通知，您必须指定一个特定日期或地理区域为触发交付通知。基于日期的通知在指定的日期和时间交付，可时区变化需要作出。当用户进入或退出特定区域的基于区域的通知交付。在这两种情况下，你可以指定通知书是否是一次性事件或可再次改期并交付使用。  

> 或presentLocalNotificationNow：UiApplication类的方法创建一个UILocalNotification对象后，即使用scheduleLocalNotification安排吧。该scheduleLocalNotification：方法使用火日期如期交货;该presentLocalNotificationNow：方法立即呈现通知，而不管fireDate的值。您可以取消使用cancelLocalNotification一个或多个本地通知：或cancelAllLocalNotifications的对象的UIApplication方法。  

> 当系统提供了一个本地通知，几件事情可能发生，这取决于该应用状态和通知类型。如果应用程序是不是最前面可见，系统会显示警告信息，徽章的应用程序，播放声音，无论是在通知规定的。如果通知是警报和用户点击操作按钮（或者，如果设备被锁定，拖动打开动作滑块），应用程序被唤醒或启动。 （如果用户点击指定使用additionalActions属性自定义操作之一，应用程序被唤醒或射入背景）。在其应用：didFinishLaunchingWithOptions：方法中，应用程序的委托可以从启动选项字典获得UILocalNotification对象使用UIApplicationLaunchOptionsLocalNotificationKey键。委托可以检查该通知的属性，并且如果通知包括在其用户信息的字典的自定义数据，它可以访问该数据，并相应地进行处理。另一方面，如果本地通知只徽章的应用图标，并且响应用户启动该应用程序，该应用程序：didFinishLaunchingWithOptions：方法中被调用，但不UILocalNotification对象被包括在选项字典。当用户选择一个自定义操作，应用程序委托的申请：handleActionWithIdentifier：forLocalNotification：completionHandler：方法被调用来处理的动作。  

> 如果应用程序是最重要的可见当系统提供的通知后，应用程序委托的申请：didReceiveLocalNotification：被调用来处理通知。使用所提供的UILocalNotification对象中的信息来决定采取何种行动。该系统不显示任何警报，徽章应用程序的图标，或者发出任何声音时，应用程序已经最前面。  
> 一个应用程序是负责管理其图标显示的证件号码。例如，如果一个短信应用接收本地通知后，处理所有传入的消息，应该由的UIApplication对象的applicationIconBadgeNumber属性设置为0删除图标徽章。  

## 效果展示

![效果图](http://ooo.0o0.ooo/2016/07/09/5781ab9d8d6bd.png)

## 代码

### 发送代码


```
- (IBAction)SendNotification_Click:(id)sender {
	//新建本地通知
	UILocalNotification *localNotification = [[UILocalNotification alloc] init];
	//触发通知时间
	localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:6];
	//重复间隔
	//localNotification.repeatInterval = kCFCalendarUnitDay;
	//设置通知的交付日期
	localNotification.timeZone = [NSTimeZone defaultTimeZone];
	//通知内容
	localNotification.alertBody = @"本地通知";
	//应用角标
	localNotification.applicationIconBadgeNumber = 1;
	//应用提示音
	localNotification.soundName = UILocalNotificationDefaultSoundName;
	//通知参数
	localNotification.userInfo = @{@"key": @"。。。。"};
	//这组事件的名称
	localNotification.category = @"tblocalNotification";
	//发送通知
	[[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
}
```

可以设置系统提示方式

![系统设置－通知](http://ooo.0o0.ooo/2016/07/09/5781addf6d1b4.png)

如果设置为**提醒模式**，那么可在`SendNotification_Click`方法里追加

```
//提示框的按钮，提示方式要设置为alert
localNotification.alertAction = @"查看";  //提示框按钮
localNotification.hasAction = YES; //是否显示额外的按钮，为no时alertAction消失
```

如果设置为**横幅模式**

`UIMutableUserNotificationAction`对象,当一个通知交付，系统会显示与通知相关联的每个自定义操作的按钮。按一个按钮就启动您的应用（无论是在前台或后台），并为您进行相应的操作机会。您可以使用这个类来配置有关，显示该按钮的细节和信息您的应用程序需要执行相应的动作。

```
- (void)registerLocalNotification{
	UIMutableUserNotificationAction *action1 = [[UIMutableUserNotificationAction alloc] init];
	//内部识别操作的字符串
	action1.identifier = @"smile";
	//本地化的字符串作为按钮标题为操作使用
	action1.title = @"😄";
	//在该模式下进行操作时运行应用程序(在后台处理)
	action1.activationMode = UIUserNotificationActivationModeBackground;
	//指示执行操作之前，用户是否必须解锁设备。
	action1.authenticationRequired = YES;
	//destructive 破坏性按钮，多用于删除
	//destructive属性设置后，在通知栏或锁屏界面左划，按钮颜色会变为红色
	//如果两个按钮均设置为YES，则均为红色（略难看）
	//如果两个按钮均设置为NO，即默认值，则第一个为蓝色，第二个为浅灰色
	//如果一个YES一个NO，则都显示对应的颜色，即红蓝双色 (CP色)。
	action1.destructive = NO;
	
	
	//第二个动作
  	UIMutableUserNotificationAction *action2 = [[UIMutableUserNotificationAction alloc] init];
  	action2.identifier = @"text";
  	action2.title = @"评论";
 	//当点击的时候不启动程序，在后台处理
  	action2.activationMode = UIUserNotificationActivationModeBackground;
  	//设置了behavior属性为 UIUserNotificationActionBehaviorTextInput 的话，则用户点击了该按钮会出现输入框供用户输入
  	action2.behavior = UIUserNotificationActionBehaviorTextInput;
  	//这个字典定义了当用户点击了右按钮后，输入框右侧的按钮名称，如果不设置该字典，则右侧按钮名称默认为 “发送”
 	action2.parameters = @{UIUserNotificationTextInputActionButtonTitleKey:@"发送"};
 	//创建动作(按钮)的类别集合
 	UIMutableUserNotificationCategory *category = [[UIMutableUserNotificationCategory alloc] init];
	//这组动作的唯一标示(要和你发送通知时填写的cateory的名称保持一致)
  	category.identifier = @"tblocalNotification";
  	//最多支持两个，如果添加更多的话，后面的将被忽略
  	[category setActions:@[action1, action2] forContext:(UIUserNotificationActionContextDefault)];
  	//创建UIUserNotificationSettings，并设置消息的显示类类型
  	UIUserNotificationSettings *uns = [UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeAlert|UIUserNotificationTypeBadge|UIUserNotificationTypeSound) categories:[NSSet setWithObject:category]];
  	//注册通知
  	[[UIApplication sharedApplication] registerUserNotificationSettings:uns];
}
	//注册服务器推送，在应用第一次启动时弹出对话框让用户确认是否开启消息推送，注册的消息类型有BadgeNumber, 声音, 顶部消息框. 可以选择其中的一种或多种。
// [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
// (UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
}
```

上面代码最好在应用第一次启动时注册通知，即 `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 方法内


应用程序正在运行接收到本地通知

```
- (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
{
    NSLog(@"%@", notification.userInfo);
    NSLog(@"app在前台状态时收到推送消息");
}
```

在非本App界面时收到本地消息，下拉消息会有快捷回复的按钮，点击按钮后调用的方法，根据identifier来判断点击的哪个按钮，notification为消息内容

```
- (void)application:(UIApplication *)application handleActionWithIdentifier:(nullable NSString *)identifier forLocalNotification:(UILocalNotification *)notification withResponseInfo:(NSDictionary *)responseInfo completionHandler:(void(^)())completionHandler
{
    if ([identifier isEqualToString:@"smile"]) {
        NSLog(@"smile");
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"smile" message:nil delegate:nil cancelButtonTitle:@"确定" otherButtonTitles:nil];
        [alert show];
    } else if ([identifier isEqualToString:@"text"]) {
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:[NSString stringWithFormat:@"评论:%@", responseInfo[UIUserNotificationActionResponseTypedTextKey]] message:nil delegate:nil cancelButtonTitle:@"确定" otherButtonTitles:nil];
        [alert show];
        
    }
    completionHandler();
}
```
博主在测试的时候，设置了循环通知，然后就是被一直提醒，把代码循环通知注释掉也没有用，最后，想到了，是不是有注销通知的方法，经查：


```
- (IBAction)remove:(id)sender {
    for (UILocalNotification *obj in [UIApplication sharedApplication].scheduledLocalNotifications) {
        if ([obj.userInfo.allKeys containsObject:@"key"]) {
            [[UIApplication sharedApplication] cancelLocalNotification:obj];
        }
    }
    //直接取消全部本地通知
    //[[UIApplication sharedApplication] cancelAllLocalNotifications];
}
```
 最后，要在开启
 ![开启后台](http://ooo.0o0.ooo/2016/07/09/5781bd0ac4dec.png)
 
## APNS
 
### 注册通知 
 在应用第一次启动时注册通知，即 `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 方法内
 
```
//注册服务器推送，在应用第一次启动时弹出对话框让用户确认是否开启消息推送，本句注册的消息类型有BadgeNumber, 声音, 顶部消息框. 可以选择其中的一种或多种。
	[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
	(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
```

### 获取token

```
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(nonnull NSData *)deviceToken{
    
    NSString *token = [NSString stringWithFormat:@"%@", deviceToken];
    //获取终端设备标识，这个标识需要通过接口发送到服务器端，服务器端推送消息到APNS时需要知道终端的标识，APNS通过注册的终端标识找到终端设备。
    NSLog(@"My token is:%@", token);
    
}
```

### 获取消息

```
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(nonnull NSDictionary *)userInfo
{
    //在此处理接收到的消息。
    NSLog(@"Receive remote notification : %@",userInfo);
}
```

本文到这里就结束了，APNS可以结合本地通知进行使用！


