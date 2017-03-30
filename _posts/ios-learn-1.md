---
title: ios 开发 学习日记(1)
date: 2017-03-15 00:08:37
tags: ios 开发 胡诌
---
## IOS 应用中info.plist文件

### 介绍
[info.plist](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) 官方说
```
	This documentation contains preliminary information about an API or technology in development. This information is subject to change, and software implemented according to this documentation should be tested with final operating system software.
```
info.list 包含了app启动时所设定API信息,版本信息等,是一个非常重要的文件.其内容以键值对的形式保存,其根据官方分类,其键值有如下类别:

类别 | 链接
------- | -------
1 | [Core Foundation Keys Describe Common Behavior](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW1)
2 | [Launch Services Keys Describe Launch-Time Behavior](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/LaunchServicesKeys.html#//apple_ref/doc/uid/TP40009250-SW1)
3| [Cocoa Keys Describe Behavior for Cocoa and Cocoa Touch Apps](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW1)
4| [macOS Keys Describe Behavior for macOS Apps](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/GeneralPurposeKeys.html#//apple_ref/doc/uid/TP40009253-SW1)
5| [iOS Keys Describe Behavior for iOS Apps](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW1)
6| [watchOS Keys Describe Behavior for Watch Apps](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/watchOSKeys.html#//apple_ref/doc/uid/TP40016498-SW1)
7| [App Extension Keys Describe Behavior for iOS and macOS App Extensions](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW1)

### plist相关
知道官方对info.plist定义了,我们看下plist在ios 编程中常见设置:

* 创建工程后会自动创建 工程名称-Info.plist(xcode-8.2不包含工程名称,为info.plist)
* 项目中可以创建其他plist文件,但是不能带有Info关键字
* 使用一个xcode打开plist文件时, PropertyList和Source Code模式打开时key值不一直,但是一一对应的.

### 不懂待查
* 问题某些key的值为 $(XXX_XXX)是在哪里定义的?

## pch 头文件
没找在官网上找到介绍, 目前的来看,pch头文件是专属于ios 的objc开发中的一个共享头文件,编译过程中,会将pch文件中内容复制到工程目录下的所有文件中.

pch头文件在xcode6之前在创建工程时生成,之后版本不再自动生成,需要[手动添加](http://www.jianshu.com/p/67ce72c4ad6c) .

* 在pch头文件中定义全局的宏
* 定义头文件的引用

常用技巧

```
#ifdef DEBUG
// 可用于定义一个log 宏定义,实现在Debug模式下打印, release模式下不打印

#ifdef __OBJ__
// 可用于判断是否当前文件为objc文件,pch头文件会加入到工程下所有代码文件中,如果不是objc文件加入会出错!
```

## App的运行过程(个人见解)
因为之前做过Android的开发和逆向研究,对Android的启动过程有一定了解,所以学习ios开发总是特别注意这一点,想要找找IOS app的具体的启动入口, 以及app在ios系统中具体的运行机制和原理;

#### 系统启动
ios app的入口很明显,就在工程下main.m目录下的main 方法,其中只有一行关键代码

```
return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
```
我们可以看出AppDelegate 类是我们整个开发的入口类,根据类名称由此我们可以推测UIApplicationMain 方法使用一个AppDelegate 类初始化了一个UIApplication对象, 并生成一个AppDelegate 对象作为UIApplication的代理,在UIApplication 启动后,根据自身在系统中不同的变化时期调用代理中不同的方法(声明周期). app开发者所有操作的部分都是根据这一机制在AppDelegate中完成的.

#### 界面相关
ios准守严格的mvc结构, 因此我们总是能够在代码中看到各种 Model View 和Controller关键字.

在个个教程或者代码中,我们经常会看到在Controller类中设置view, 甚至在storyboard中view是包含于controller之内的, 这可能是苹果无意间搞出来的错觉,其实controller和view其实是两个不同的东西,一个用来提供逻辑,一个用来展示界面,只不过由于ios提供的api限制,只能通过一个controller将view显示出来.

ios的有三种写界面的方法:
1 stroyboard,这中方法在plist文件中指定一个启动的stroyboard, 程序启动后即加载当前stroyboard中的controller 和配置的view.
2 xib文件,这中方式单独声明一个view
3 纯手写代码
以上三种方式是可以相互转换的.具体的代码见google和iso developer 官网(可以通过google获取的需要背的api都不重要).

