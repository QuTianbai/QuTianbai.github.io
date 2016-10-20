---
layout: post
title:  "动态UITableView 截图"
date:   2015-10-15 01:23:32
categories: iOS高级
---

# 需求
前一阵，项目里面生成库存调拨单，是一个动态的TableView，每一个cell 都是调拨的商品

问题是。 需要将tableview的调拨单 生成图片分享到其他平台


那么问题来了  如何生成截图呢


# 代码

```
+(UIImage *)getTableViewimagewithTabelview:(UITableView *)tableview{
    UIImage* viewImage = nil;
    UITableView *scrollView = tableview;
    UIGraphicsBeginImageContextWithOptions(scrollView.contentSize, scrollView.opaque, 0.0);
    {
        CGPoint savedContentOffset = scrollView.contentOffset;
        CGRect savedFrame = scrollView.frame;
        
        scrollView.contentOffset = CGPointZero;
        scrollView.frame = CGRectMake(0, 0, scrollView.contentSize.width, scrollView.contentSize.height);
        
        [scrollView.layer renderInContext: UIGraphicsGetCurrentContext()];
        viewImage = UIGraphicsGetImageFromCurrentImageContext();
        
        scrollView.contentOffset = savedContentOffset;
        scrollView.frame = savedFrame;
    }
    UIGraphicsEndImageContext();
    
    return viewImage;
}
```

# 效果

![](http://ooo.0o0.ooo/2016/10/18/5806176691c1a.png)


