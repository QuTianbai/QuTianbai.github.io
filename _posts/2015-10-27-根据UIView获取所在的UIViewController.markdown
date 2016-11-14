---
layout: post
title:  "iOS中如何根据UIView获取所在的UIViewController
"
date:   2015-10-27 01:23:32
categories: iOS基础
---

## 简述
- 	本文将讲述在iOS开发中如何根据某个UIView（前提是该UIView已经被加入到Controller中）来获取所属的UIViewController。

## UIResponder
- 在UIKit中，UIApplication、UIView、UIViewController这几个类都是直接继承自UIResponder类。另外SpriteKit中的SKNode也是继承自UIResponder类。因此UIKit中的视图、控件、视图控制器，以及我们自定义的视图及视图控制器都有响应事件的能力。这些对象通常被称为响应对象，或者是响应者(以下我们统一使用响应者)。
- 视图、视图控制器、窗口对象和app对象都能处理事件。另外需要注意的是，手势也会影响到事件的传递。
- Responder Chain 大概的传递规则就是从视图顶层的UIView向下到UIViewController再到RootViewController再到Window最后到Application。

## 实现
- OC版本

```
id responder = self.nextResponder;
while (![responder isKindOfClass: [UIViewController class]] && ![responder isKindOfClass: [UIWindow class]])
	 {
        responder = [responder nextResponder];
    }
    if ([responder isKindOfClass: [UIViewController class]])
    {
        // responder就是view所在的控制器
        // do something
    }
```
- Swift版本

```
func viewController(aClass: AnyClass) -> UIViewController?{
       for(var next=self.superview;(next != nil);next=next?.superview){
           let nextResponder = next?.nextResponder()
           if((nextResponder?.isKindOfClass(aClass)) != nil){
               return nextResponder as? UIViewController
           }
       }
       return nil
   }

```


