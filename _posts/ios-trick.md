---
title: ios 学习日记(3)
date: 2017-03-22 22:32:46
tags: ios 奇技淫巧, 一些知识点
---

今天学到两个ios开发相关小技巧, 虽然我一直以为这些是ios开发中的 奇技淫巧 ,不值一提的的trick, 但是既然是初学,而且是日记形式的,就记录一下

## 关于tableview
看一个关于tableview 下拉刷新的控件的时候,看到如下用法:
```
[_contentTableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@"cell"];
```

在tableview 中注册一个UITableViewCell类,并设置Identifier.
看到这个就理解了当初看在storyboard里拖线的时候为啥UITableViewController 直接拖出来会自带一个Cell 了, 当时感觉完全反人类的设计啊, 这样一下就明白了.

自己手打了一下,发现另外一个注册cell的方法
```
 [_contentTableView registerNib:[UINib nibWithNibName:@"tableviewCellxib" bundle:nil]forCellReuseIdentifier:@"riv"];
```

代码说明是可以直接注册一个xib文件的,新建一个xib文件, 需要将其root view设置为继承至UITableViewCell;

##### 取消选中状态

在UITableView里面，选择了某一个cell以后，点击立刻取消该cell的选中状态，可以使用如下方法：

```
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath 
{
    //some functions
    ......

    // 取消选中状态
    [tableView deselectRowAtIndexPath:indexPath animated:NO]; 
}
```

## 渐变颜色
应该还有其他的渐变颜色的实现方法吧,但网上说这是最简单的一种(好多博文都写了这一种)
上代码:
```
CAGradientLayer *gradientLayer = [CAGradientLayer layer];
//  设置 gradientLayer 的 Frame
gradientLayer.frame = self.bounds;
//  创建渐变色数组，需要转换为CGColor颜色
gradientLayer.colors = @[(id)[UIColor hx_colorWithHexRGBAString:@"#559affff"].CGColor,(id)[UIColor hx_colorWithHexRGBAString:@"#2fb4d9ff"].CGColor];

//  设置两种种颜色变化点，取值范围 0.0~1.0
//		可以不设置
//    gradientLayer.locations = @[@(0.0f)];
//  设置渐变颜色方向，左上点为(0,0), 右下点为(1,1)
gradientLayer.startPoint = CGPointMake(0, 0);
gradientLayer.endPoint = CGPointMake(1, 0);
// 设置圆角弧度 5像素
gradientLayer.cornerRadius = 5;
//  添加渐变色到创建的 UIView 上去
[self.layer addSublayer:gradientLayer];
```
以上是我在一个UIVIew 中实现的代码, 直接给self 设置了一个sublayer;

关于[CAGradientLayer](https://developer.apple.com/reference/quartzcore/cagradientlayer) 其官方文档说它可以为一个空间设置渐变颜色的背景颜色 和 设置圆角. 可以设置多层级颜色的渐变,在不同位置上的渐变. ![样例](https://docs-assets.developer.apple.com/published/ea6ba49ff5/62414639-080e-41fd-96c2-c9379fd8a162.png)


