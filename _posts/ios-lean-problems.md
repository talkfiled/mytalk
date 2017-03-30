---
title: ios 问题备忘录(1)
date: 2017-03-22 00:04:36
tags: ios 问题 备忘录
---

今天写代码遇到两个问题,不知道什么原因. 在这里记录一下: 
 
## 1 使用uitableviewcontroller时, 跳转出去之后再返回页面变黑问题.
我继承了UITableViewController 实现了一个Controller 
直接使用 self.tableView 添加数据,发现点击跳转之后  
[self.navigationController pushViewController:mTableViewController animated:YES]; 
再返回整个界面都变黑了 
 
于是自己又添加了个contenttableView实现,这样是好的. 
 
## tableheaderview 不显示问题.
因为需要实现头部view的效果,使用两个section 发现整个row 被选中后的状态会加一个灰色的选装状态 
于是想通过tableheaderview实现; 
但是在contenttable 中直接添加tableheaderview并不会显示出来; 
直接修改继承关系,将父类改为UIViewController 并实现UITableViewDataSource, UITableViewDelegate两个协议好用了 

## 说明
代码不方便上传,等我有空单独搞一份实例代码在验证验证, 说不定就找到原因了.

