---
title: 浅翻Masonry README
date: 2017-03-28 22:04:35
tags: 翻译, masonry
---

看了几篇关于Masonry的文章和教程, 看到所有人都对Masonry推崇备至, 于是乎将源码下载下来仔细研读.

本文是对其Readme的翻译,基本方式是在其原文后补上中文翻译

#Masonry 

**Masonry is still actively maintained, we are committed to fixing bugs and merging good quality PRs from the wider community. However if you're using Swift in your project, we recommend using [SnapKit](https://github.com/SnapKit/SnapKit) as it provides better type safety with a simpler API.**

**该项目仍处于维护阶段, 我们会更新发现的bug和广大用户的优质提交. 如果你在使用swift语言, 我们建议你使用SnapKit.**

Masonry is a light-weight layout framework which wraps AutoLayout with a nicer syntax. Masonry has its own layout DSL which provides a chainable way of describing your NSLayoutConstraints which results in layout code that is more concise and readable.
Masonry 是一个轻量级布局框架, 该框架基于AutoLayout 并且提供一个更友好的链式语法来描述 约束(NSLayoutConstraints).

Masonry supports iOS and Mac OS X.
Masonry 支持iOS开发和 Mac OS X开发.

For examples take a look at the **Masonry iOS Examples** project in the Masonry workspace. You will need to run `pod install` after downloading.
你可以通过项目中**Masonry iOS Examples** 部分代码查看示例. 项目是通过cocoa pod 管理的,下载完成之后需要运行pod install.

## What's wrong with NSLayoutConstraints?
## 为什么要用Masonry! (直译为: NSLayoutConstraints 怎么了?)

Under the hood Auto Layout is a powerful and flexible way of organising and laying out your views. However creating constraints from code is verbose and not very descriptive.

自动布局(Auto Layout) 是一个强大且灵活的管理和布局视图(view)的工具. 但是, 通过在代码中创建约束的方式使用时很麻烦的.

Imagine a simple example in which you want to have a view fill its superview but inset by 10 pixels on every side
加入你有这么个需求: 将一个视图view 的边距设置为距其父视图 10像素, 使用 约束代码如下:
```obj-c
UIView *superview = self.view;

UIView *view1 = [[UIView alloc] init];
view1.translatesAutoresizingMaskIntoConstraints = NO;
view1.backgroundColor = [UIColor greenColor];
[superview addSubview:view1];

UIEdgeInsets padding = UIEdgeInsetsMake(10, 10, 10, 10);

[superview addConstraints:@[

    //view1 constraints
    // view1 约束
    [NSLayoutConstraint constraintWithItem:view1
                                 attribute:NSLayoutAttributeTop
                                 relatedBy:NSLayoutRelationEqual
                                    toItem:superview
                                 attribute:NSLayoutAttributeTop
                                multiplier:1.0
                                  constant:padding.top],

    [NSLayoutConstraint constraintWithItem:view1
                                 attribute:NSLayoutAttributeLeft
                                 relatedBy:NSLayoutRelationEqual
                                    toItem:superview
                                 attribute:NSLayoutAttributeLeft
                                multiplier:1.0
                                  constant:padding.left],

    [NSLayoutConstraint constraintWithItem:view1
                                 attribute:NSLayoutAttributeBottom
                                 relatedBy:NSLayoutRelationEqual
                                    toItem:superview
                                 attribute:NSLayoutAttributeBottom
                                multiplier:1.0
                                  constant:-padding.bottom],

    [NSLayoutConstraint constraintWithItem:view1
                                 attribute:NSLayoutAttributeRight
                                 relatedBy:NSLayoutRelationEqual
                                    toItem:superview
                                 attribute:NSLayoutAttributeRight
                                multiplier:1
                                  constant:-padding.right],

 ]];
```
Even with such a simple example the code needed is quite verbose and quickly becomes unreadable when you have more than 2 or 3 views.
即使是实现这么一个简单需求都会变得如此复杂, 如果视图个数超过2 个或3个, 整个代码将成为天书.

Another option is to use Visual Format Language (VFL), which is a bit less long winded.
当然你可以使用可视化编辑语言(应该指的是使用 storyboard / xib )简化代码.
 
However the ASCII type syntax has its own pitfalls and its also a bit harder to animate as `NSLayoutConstraint constraintsWithVisualFormat:` returns an array.
不论如何, 原始的 ASCII语义结构(ASCII type syntax)的语法是有缺陷的, 并且很难实现使用可视化语言让 `NSLayoutconstraint constraintsWithVisualForat: `返回一个数组. (不太理解, 可能我还不太懂 AutoLayout)

## Prepare to meet your Maker!
## 准备使用Maker (即Masonry)!

Heres the same constraints created using MASConstraintMaker
如下是使用 Masonry实现之前例子的代码:
```obj-c
UIEdgeInsets padding = UIEdgeInsetsMake(10, 10, 10, 10);

[view1 mas_makeConstraints:^(MASConstraintMaker *make) {
    make.top.equalTo(superview.mas_top).with.offset(padding.top); //with is an optional semantic filler
    make.left.equalTo(superview.mas_left).with.offset(padding.left);
    make.bottom.equalTo(superview.mas_bottom).with.offset(-padding.bottom);
    make.right.equalTo(superview.mas_right).with.offset(-padding.right);
}];
```
Or even shorter
或者使用更少代码实现:

```obj-c
[view1 mas_makeConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(superview).with.insets(padding);
}];
```

Also note in the first example we had to add the constraints to the superview `[superview addConstraints:...`.
Masonry however will automagically add constraints to the appropriate view.

我们注意到在原始例子中需要使用`[superview addConstraints:...`将当前约束与其父view关联.
Masonry自动帮我们完成了这一调用.

Masonry will also call `view1.translatesAutoresizingMaskIntoConstraints = NO;` for you.
同事, Masonry会为控件调用`view1.translatesAutoresizingMaskIntoConstraints = NO;`  方法. 

## Not all things are created equal
## 约束里不仅有等于

> `.equalTo` equivalent to **NSLayoutRelationEqual**

> `.lessThanOrEqualTo` equivalent to **NSLayoutRelationLessThanOrEqual**

> `.greaterThanOrEqualTo` equivalent to **NSLayoutRelationGreaterThanOrEqual**
如上三个是masonry 和autolayout 中的等价关系.

These three equality constraints accept one argument which can be any of the following:
如上三个约束设置接收如下的参数:
#### 1. MASViewAttribute

```obj-c
make.centerX.lessThanOrEqualTo(view2.mas_left);
```

MASViewAttribute           |  NSLayoutAttribute
-------------------------  |  --------------------------
view.mas_left              |  NSLayoutAttributeLeft
view.mas_right             |  NSLayoutAttributeRight
view.mas_top               |  NSLayoutAttributeTop
view.mas_bottom            |  NSLayoutAttributeBottom
view.mas_leading           |  NSLayoutAttributeLeading
view.mas_trailing          |  NSLayoutAttributeTrailing
view.mas_width             |  NSLayoutAttributeWidth
view.mas_height            |  NSLayoutAttributeHeight
view.mas_centerX           |  NSLayoutAttributeCenterX
view.mas_centerY           |  NSLayoutAttributeCenterY
view.mas_baseline          |  NSLayoutAttributeBaseline

#### 2. UIView/NSView

if you want view.left to be greater than or equal to label.left :
如果你想讲view.left设置为不小于label.left, 可以使用如下代码:
```obj-c
//these two constraints are exactly the same
// 两种方式均可
make.left.greaterThanOrEqualTo(label);
make.left.greaterThanOrEqualTo(label.mas_left);
```

#### 3. NSNumber

Auto Layout allows width and height to be set to constant values.
AutoLayout允许为宽高设置常量值.
if you want to set view to have a minimum and maximum width you could pass a number to the equality blocks:
如果你希望为一个view设置一个最小和最大宽度,可以使用如下方法:

```obj-c
//width >= 200 && width <= 400
make.width.greaterThanOrEqualTo(@200);
make.width.lessThanOrEqualTo(@400)
```

However Auto Layout does not allow alignment attributes such as left, right, centerY etc to be set to constant values.
So if you pass a NSNumber for these attributes Masonry will turn these into constraints relative to the view&rsquo;s superview ie:

由于 AutoLayout不允许定位属性(例如: left, right, centerY等)设置为常量值. 因此如果你想为定位属性设置常量, Masonry会自动为你转换为相对于父控件的约束.
```obj-c
//creates view.left = view.superview.left + 10
make.left.lessThanOrEqualTo(@10)
```

Instead of using NSNumber, you can use primitives and structs to build your constraints, like so:
你可以使用NSNumber 或者基本数据类型来创建约束.
```obj-c
make.top.mas_equalTo(42);
make.height.mas_equalTo(20);
make.size.mas_equalTo(CGSizeMake(50, 100));
make.edges.mas_equalTo(UIEdgeInsetsMake(10, 0, 10, 0));
make.left.mas_equalTo(view).mas_offset(UIEdgeInsetsMake(10, 0, 10, 0));
```

By default, macros which support [autoboxing](https://en.wikipedia.org/wiki/Autoboxing#Autoboxing) are prefixed with `mas_`. Unprefixed versions are available by defining `MAS_SHORTHAND_GLOBALS` before importing Masonry.

默认情况下,为了兼容macros所有前缀加`mas_`, 在引入Masonry.h之前定义一个`MAS_SHORTHAND_GLOBALS`值可以去掉前缀.

#### 4. NSArray

An array of a mixture of any of the previous types
可以接收之前所有类型的一个混合数组.

```obj-c
make.height.equalTo(@[view1.mas_height, view2.mas_height]);
make.height.equalTo(@[view1, view2]);
make.left.equalTo(@[view1, @100, view3.right]);
````

## Learn to prioritize

> `.priority` allows you to specify an exact priority

> `.priorityHigh` equivalent to **UILayoutPriorityDefaultHigh**

> `.priorityMedium` is half way between high and low

> `.priorityLow` equivalent to **UILayoutPriorityDefaultLow**

Priorities are can be tacked on to the end of a constraint chain like so:
```obj-c
make.left.greaterThanOrEqualTo(label.mas_left).with.priorityLow();

make.top.equalTo(label.mas_top).with.priority(600);
```

## Composition, composition, composition
## 组合才是王道!

Masonry also gives you a few convenience methods which create multiple constraints at the same time. These are called MASCompositeConstraints
通过使用MASCompositeConstraints, 你可以方便的构建一个多重的约束

#### edges 边距

```obj-c
// make top, left, bottom, right equal view2
make.edges.equalTo(view2);

// make top = superview.top + 5, left = superview.left + 10,
//      bottom = superview.bottom - 15, right = superview.right - 20
make.edges.equalTo(superview).insets(UIEdgeInsetsMake(5, 10, 15, 20))
```

#### size 大小

```obj-c
// make width and height greater than or equal to titleLabel
make.size.greaterThanOrEqualTo(titleLabel)

// make width = superview.width + 100, height = superview.height - 50
make.size.equalTo(superview).sizeOffset(CGSizeMake(100, -50))
```

#### center 中心
```obj-c
// make centerX and centerY = button1
make.center.equalTo(button1)

// make centerX = superview.centerX - 5, centerY = superview.centerY + 10
make.center.equalTo(superview).centerOffset(CGPointMake(-5, 10))
```

You can chain view attributes for increased readability:
使用链式语法增加可读性:

```obj-c
// All edges but the top should equal those of the superview
make.left.right.and.bottom.equalTo(superview);
make.top.equalTo(otherView);
```

## Hold on for dear life
## 珍爱生命(什么鬼标题)

Sometimes you need modify existing constraints in order to animate or remove/replace constraints.
In Masonry there are a few different approaches to updating constraints.
在Masonry中有如下几种修改选优约束的方法.

#### 1. References 引用

You can hold on to a reference of a particular constraint by assigning the result of a constraint make expression to a local variable or a class property.
You could also reference multiple constraints by storing them away in an array.

通过一个引用, 直接调用uninstall 移除所有的约束.

```obj-c
// in public/private interface
@property (nonatomic, strong) MASConstraint *topConstraint;

...

// when making constraints
[view1 mas_makeConstraints:^(MASConstraintMaker *make) {
    self.topConstraint = make.top.equalTo(superview.mas_top).with.offset(padding.top);
    make.left.equalTo(superview.mas_left).with.offset(padding.left);
}];

...
// then later you can call
[self.topConstraint uninstall];
```

#### 2. mas_updateConstraints
Alternatively if you are only updating the constant value of the constraint you can use the convience method `mas_updateConstraints` instead of `mas_makeConstraints`n 
通过使用`mas_updateConstraints`方法来更新约束.

```obj-c
// this is Apple's recommended place for adding/updating constraints
// this method can get called multiple times in response to setNeedsUpdateConstraints
// which can be called by UIKit internally or in your code if you need to trigger an update to your constraints
- (void)updateConstraints {
    [self.growingButton mas_updateConstraints:^(MASConstraintMaker *make) {
        make.center.equalTo(self);
        make.width.equalTo(@(self.buttonSize.width)).priorityLow();
        make.height.equalTo(@(self.buttonSize.height)).priorityLow();
        make.width.lessThanOrEqualTo(self);
        make.height.lessThanOrEqualTo(self);
    }];

    //according to apple super should be called at end of method
    [super updateConstraints];
}
```

### 3. mas_remakeConstraints
`mas_updateConstraints` is useful for updating a set of constraints, but doing anything beyond updating constant values can get exhausting. That's where `mas_remakeConstraints` comes in.

通过使用`mas_remakeConstraints`该实现.

`mas_remakeConstraints` is similar to `mas_updateConstraints`, but instead of updating constant values, it will remove all of its constraints before installing them again. This lets you provide different constraints without having to keep around references to ones which you want to remove.

```obj-c
- (void)changeButtonPosition {
    [self.button mas_remakeConstraints:^(MASConstraintMaker *make) {
        make.size.equalTo(self.buttonSize);

        if (topLeft) {
        	make.top.and.left.offset(10);
        } else {
        	make.bottom.and.right.offset(-10);
        }
    }];
}
```

You can find more detailed examples of all three approaches in the **Masonry iOS Examples** project.

## When the ^&*!@ hits the fan!
## 使日志输出更有意义!

Laying out your views doesn't always goto plan. So when things literally go pear shaped, you don't want to be looking at console output like this:

```obj-c
Unable to simultaneously satisfy constraints.....blah blah blah....
(
    "<NSLayoutConstraint:0x7189ac0 V:[UILabel:0x7186980(>=5000)]>",
    "<NSAutoresizingMaskLayoutConstraint:0x839ea20 h=--& v=--& V:[MASExampleDebuggingView:0x7186560(416)]>",
    "<NSLayoutConstraint:0x7189c70 UILabel:0x7186980.bottom == MASExampleDebuggingView:0x7186560.bottom - 10>",
    "<NSLayoutConstraint:0x7189560 V:|-(1)-[UILabel:0x7186980]   (Names: '|':MASExampleDebuggingView:0x7186560 )>"
)

Will attempt to recover by breaking constraint
<NSLayoutConstraint:0x7189ac0 V:[UILabel:0x7186980(>=5000)]>
```

Masonry adds a category to NSLayoutConstraint which overrides the default implementation of `- (NSString *)description`.
Now you can give meaningful names to views and constraints, and also easily pick out the constraints created by Masonry.

which means your console output can now look like this:

```obj-c
Unable to simultaneously satisfy constraints......blah blah blah....
(
    "<NSAutoresizingMaskLayoutConstraint:0x8887740 MASExampleDebuggingView:superview.height == 416>",
    "<MASLayoutConstraint:ConstantConstraint UILabel:messageLabel.height >= 5000>",
    "<MASLayoutConstraint:BottomConstraint UILabel:messageLabel.bottom == MASExampleDebuggingView:superview.bottom - 10>",
    "<MASLayoutConstraint:ConflictingConstraint[0] UILabel:messageLabel.top == MASExampleDebuggingView:superview.top + 1>"
)

Will attempt to recover by breaking constraint
<MASLayoutConstraint:ConstantConstraint UILabel:messageLabel.height >= 5000>
```

For an example of how to set this up take a look at the **Masonry iOS Examples** project in the Masonry workspace.

## Where should I create my constraints?
## 应该在哪里创建约束?

```objc
@implementation DIYCustomView

- (id)init {
    self = [super init];
    if (!self) return nil;

    // --- Create your views here ---
    self.button = [[UIButton alloc] init];

    return self;
}

// tell UIKit that you are using AutoLayout
+ (BOOL)requiresConstraintBasedLayout {
    return YES;
}

// this is Apple's recommended place for adding/updating constraints
// 苹果推荐!!
- (void)updateConstraints {

    // --- remake/update constraints here
    [self.button remakeConstraints:^(MASConstraintMaker *make) {
        make.width.equalTo(@(self.buttonSize.width));
        make.height.equalTo(@(self.buttonSize.height));
    }];
    
    //according to apple super should be called at end of method
    [super updateConstraints];
}

- (void)didTapButton:(UIButton *)button {
    // --- Do your changes ie change variables that affect your layout etc ---
    self.buttonSize = CGSize(200, 200);

    // tell constraints they need updating
    // 通知约束改变!
    [self setNeedsUpdateConstraints];
}

@end
```

## Installation 使用
Use the [orsome](http://www.youtube.com/watch?v=YaIZF8uUTtk) [CocoaPods](http://github.com/CocoaPods/CocoaPods).

In your Podfile
>`pod 'Masonry'`

If you want to use masonry without all those pesky 'mas_' prefixes. Add #define MAS_SHORTHAND to your prefix.pch before importing Masonry
>`#define MAS_SHORTHAND`

Get busy Masoning
>`#import "Masonry.h"`

## Code Snippets

Copy the included code snippets to ``~/Library/Developer/Xcode/UserData/CodeSnippets`` to write your masonry blocks at lightning speed!

`mas_make` -> `[<view> mas_makeConstraints:^(MASConstraintMaker *make){<code>}];`

`mas_update` -> `[<view> mas_updateConstraints:^(MASConstraintMaker *make){<code>}];`

`mas_remake` -> `[<view> mas_remakeConstraints:^(MASConstraintMaker *make){<code>}];`

## Features
* Not limited to subset of Auto Layout. Anything NSLayoutConstraint can do, Masonry can do too!
* Great debug support, give your views and constraints meaningful names.
* Constraints read like sentences.
* No crazy macro magic. Masonry won't pollute the global namespace with macros.
* Not string or dictionary based and hence you get compile time checking.

## TODO
* Eye candy 六六六
* Mac example project
* More tests and examples


