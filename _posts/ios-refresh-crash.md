---
title: ios 列表下拉刷新崩溃问题现象及解决
date: 2017-03-27 23:24:34
tags: bug, ios, 奇技淫巧
---

# tableView 崩溃的现象

今天写了个列表, 需要从网络端读取数据并加载到列表中显示; 既然是个列表就少不了下拉刷新和上拉加载功能, 于是乎在cocoachina 中找到个叫SCRefrsh的控件开始搞起;

列表写完了发现下拉刷新时一个bug: 在一次刷新操作完成之前,再次下拉列表列表,当鼠标放开时会崩溃;看日志也没有什么有用的信息. 于是一行行开始分析代码. 原代码因为涉及到公司代码不方便放出,于是重新根据找到的原因写了个简单的tableView.

实现demo: [下拉崩溃 demo](https://github.com/talkfiled/TableViewRelated/tree/master/TableViewRefresh)

#### tableview 视图

1. ViewController 继承了UITableViewDelegate, UITableViewDataSource 两个协议,并且直接将两个协议的代理对象设置给contentView (UITableView * 对象). 
2. contentView 以"@cell"为标示注册一个UITableViewCell ;
3. 实现如下两个接口

```
-(NSInteger) tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{
    return self.dataSource.count;
}

-(UITableViewCell*) tableView:(UITableView *) tableView cellForRowAtIndexPath:(nonnull NSIndexPath *)indexPath{
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"cell"];
    cell.textLabel.text = self.dataSource[indexPath.row];
    return cell;
}
```

其中
```
@property (nonatomic, strong) NSMutableArray *dataSource;
```

#### 下拉刷新监听设置

```
self.contentView.refresh = [ SCRefresh refreshWithTarget:self HeaderAction:@selector(refresh) FooterAction:@selector(loadMore)];
```

#### 数据加载过程

1. controller 初始化时, 加载20条数据
2. 下拉刷新时,清空self.dataSource并加载新数据

```
-(void) refresh{
    self.size = 20;
    NSLog(@" %s ", __func__);
    [self.dataSource removeAllObjects];
    __weak __typeof__(self) weakSelf = self;
    
    // 模拟延迟操作
    dispatch_time_t delayTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0* NSEC_PER_SEC));
    dispatch_after(delayTime, dispatch_get_main_queue(), ^{
        
        [weakSelf modelChanged];
        [weakSelf.contentView.refresh endRefreshRefreshType:RefreshOptionHeader];
    });
    
}
```

####  崩溃问题
1. 当self.size =5时,不会出现崩溃问题
2. 当self.size =20 (屏幕上放不下20条数据)时, 两次连续下拉会直接退出程序;
3. xcode 调试日志:

```
// 神奇的xcode, demo版本竟然有日志!!!
TableViewRefresh[19031:6567158] RefreshStatePulled
TableViewRefresh[19031:6567158]  -[ViewController refresh] 
TableViewRefresh[19031:6567158] *** Terminating app due to uncaught exception 'NSRangeException', reason: '*** -[__NSArrayM objectAtIndex:]: index 13 beyond bounds for empty array'
```

#### 原因分析
从上面的日志中我们看出, 在刷新的时候报错了. 并且是数据越界的错误;

猜想可能是由于刷新过程中要清空原来的数据,于是乎就在访问网络(模拟延迟操作)之前将数据给清空了, 这样一来两次下拉刷新连续操作的时候. 第一次操作完成之后 reloadData, 刷新界面, 此时又清空了数据, 导致数组越界.

#### 解决办法

1. 在refresh(下拉刷新) 的时候标记一下;
2. 去掉访问网络之前的清空数据操作, 改到结果获取之后;
3. 结果获取之后,根据1中标记判断是否清理元数据;
4. 为了防止两次刷新只清空一次数据的问题, 在网络请求之前做标记, 判断是否正在请求, 如果请求则直接返回,否则继续;
5. 解决后代码略, 可以根据1-4 再修改


#### 后记
在写这篇文章过程中,发现了个更通用的下拉刷新,上滑加载的解决方案: [MJRefresh](https://github.com/CoderMJLee/MJRefresh) 
...










