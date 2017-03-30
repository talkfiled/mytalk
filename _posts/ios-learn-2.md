---
title: ios 开发 学习日记(2)
date: 2017-03-16 23:36:29
tags: ios 开发 胡诌 学习日记
---
## 苹果官方文档

自学ios开发, 先看了一些直接上手的教程,然后开始查找官方文档做些小项目; 因为没人指导,官方文档都找了好久(囧.jpg),总于算是理清楚苹果放文档的思路了.

有两个地方可以查看官方文档,一个是苹果[开发者网站](https://developer.apple.com/develop/) 另一个是xcode 的 [Document and API Reference]().两者的内容是基本相投的, 其中开发者网站中只能查看官方发布的最新的api和文档.

文档共分为API Reference, Guides, Sample code三部分, 其中Api Reference 主要内容包含苹果开放平台的所有接口. Guides 和Sample Code 是一体的, 以引导的方式介绍整个苹果开放平台的内容,包括开发语言的学习,整个系统架构和样例代码等内容.

## objective-c 学习笔记
Ojbective-c 是个很神奇的语言,因为目前开来它只用于苹果体系系统的开发, 没见到其的非苹果有用的. 虽然官方和很多教程/文章都说obj-c是c语言的一个超集,有怎样怎样的历史呀,bla bla bla一大堆. 但是没有在其他地方见到过,所以我还是认为obj-c是专属苹果的,一套自娱自乐的开发语言,因此要学习还是能找到其官方介绍最好,经过两天不懈努力,终于让我给找到了, 在 Document and API Reference 部分
![20170316148967515818137.jpg](http://omoo8c568.bkt.clouddn.com/20170316148967515818137.jpg)
Ojbective-C 苹果开发者平台[学习文档](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011210-CH1-SW1)同时还找到了个中文版本[文档](http://wiki.jikexueyuan.com/project/programming-with-objective-c/)(我能说我是先找到中文版,看到它对应的官方链接之后才在x-code 中找到的吗 [傲娇.png]).

### objective-c 中的数据封装特征关键字

#### 1. readonly/ readwrite
默认情况下, 编译器会自动为一个声明的属性生成getter 方法和setter 方法:
```
eg: 声明属性
	@property NSString * name;

//自动生成访问器(accessor)
	getter:
	NSString *firstName = [somePerson firstName];

	setter:
    [somePerson setFirstName:@"Johnny"];
```
如果不希望生成setter 方法,可以声明readonly特征关键字
```
@property(readonly) NSString * name;
```
ps: 不声明readonly时为默认情况 即readwrite

==readonly 关键字限制生成 setter 访问器==

修改编译器自动生成的访问器方法:

```
@property (getter=isFinished) BOOL finished;

@property (readonly, getter=isFinished) BOOL finished;
```
#### 2. 实例变量
总觉得实例变量是个很神奇的变量,从文档的介绍中一直想不明白为什么苹果要搞个实例变量出来. 现在我能想到的唯一的解释就是苹果搞挫了(以下内容为胡诌):
obj-c的方括号调用和所谓的点语法,彻底混淆了类中属性的指向, 例如如下代码,鬼知道你是想指向一个abc的属性还是指向一个abc的无参函数.

```
	[self.abc]
```

同时苹果还要随大流的弄出个getter和setter方法来提现 面向对象的理念, 这样就更搞不清楚一个self.abc的abc 是什么东东了,于是乎为了能够指向属性所代表的那个对象本身,又搞出一个叫实例变量的玩意儿(反正都是系统里的一块内存,有事自己定义语法,想怎么搞就怎么搞),都是作死作出来.

```
// 指定实例变量的名字
@implementation YourClass
    @synthesize propertyName = instanceVariableName;
    ...
    @end

// 默认instanceVariableName 为下划线加属性名(_propertyName)
```

纠结和找了好久的lazy访问器:
```
- (XYZObject *)someImportantObject {
    if (!_someImportantObject) {
        _someImportantObject = [[XYZObject alloc] init];
    }

    return _someImportantObject;
    }
```

#### 3. nonatomic 和atomic
文档上对atomic的说名:
```
This means that the synthesized accessors ensure that a value is always fully retrieved by the getter method or fully set via the setter method, even if the accessors are called simultaneously from different threads.
```
意味着在多线程下,getter / setter是同步的,相当于不可拆分的原子.
明显nonatomic 比atomic 快;

#### 4. strong 和 weak 和关于ARC机制的诌
weak的出现唯一目的就是避免强引用环. obj-c采用ARC机制, 而ARC 机制通过自动引用计数的方式在编译阶段自动的在合适的地方加上释放内存的代码. 当存在强引用换时,ARC机制找不到这个合适的地方去释放内存,产生内存泄露.

java 中的引用分为强引用, 软引用, 弱引用 和虚引用, 它们的区别是在gc时是否被回收, 都跟内存泄露无关.
据说java[对象循环引用也会被回收](http://blog.sina.com.cn/s/blog_6a0eedb60100y76b.html)

ARC 机制是代码级别的内存管理, 通过在编译过程中自动添加代码实现内存释放, gc 是运行时垃圾回收机制,通过gc 检查系统中每块内存(对象)的运行情况,发现不需要了就可以自动干掉.

unsafe_unretained ,不安全引用,一个不安全引用与弱关联之间的相同点在于，它们都不会保证相应对象的活动。但当目标对象被释放时，不安全引用不会被设置成 nil 。

#### copy 与深浅拷贝的诌
obj-c中引用一般都是指针类型的,当初始话的对象在别的指针引用的情况向被修改了,初始化的新指针也会修改,有事我们不希望这些修改,就可以使用copy.

深浅拷贝是最无聊的一组术语, 或者说都是不应该存在的术语, 关于拷贝问题,我们需要理清楚指针是什么, 对象是在哪里, 并且牢记所有代码都是计算机在运行过程中内存变化的体现,这样就可以了.


