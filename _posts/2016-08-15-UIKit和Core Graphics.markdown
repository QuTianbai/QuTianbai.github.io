---
layout: post
title:  "UIKit和Core Graphics"
date:   2016-08-15 01:23:32
categories: iOS高级
---

#  绘图

iOS的绘图框架有

> UIKit

UIKit 是最高级的界面,他控制视图的布局、组成、绘图、图片、字体、动画等，所有 UI 组件都是 UIKit 的元素

> Core Graphics（Quartz)

UIKit 主要的绘图系统，用来绘制自定义视图

> Core Animation

Core Animation 提供了强大的动画效果

> Core Image

图片处理

> OpenGL ES

OpenGL 的子集  兼容多个平台

## 视图绘制周期

* 在UIView中，重写 `- (void)drawRect:(CGRect)rect` 可以绘制图形，且此方法一般情况下只会画一次.也就是说这个`- (void)drawRect:(CGRect)rect`方法一般情况下只会被掉用一次。
* 当某些情况下想要手动重画这个View,只需要掉用[self setNeedsDisplay]方法即可。
* 

## 视图的绘制和布局


# 自定义视图绘制

大部分自定义视图都是用UIKit 或 Core Graphics 实现的







