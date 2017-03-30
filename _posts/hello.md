---
title: hexo 折腾记
date: 2017-03-11 12:18:53
tags: Hexo 相关
categories: Hexo教程
---
# Hexo 折腾记
周末无聊,又想起写博客的事情了, 之前了解过hexo可以在github 上发布自己自己的博客,而且是免费的,何乐而不为呢,于是乎开搞!

hexo是一个能够将Markdown 文件转换成静态页面的,需要Node.js为服务端支持,能够一键发布并且包含大量插件的博客系统(我自己定义的);

##安装和配置
首先去[hexo官网](https://hexo.io/)看了下,里面介绍了hexo怎么安装hexo到本地,并没有怎么搭建到github上的内容,于是乎转战google搜索,看看各位先驱们的解决方案, 于是乎找到了 [零基础免费搭建个人博客-hexo+github](http://hifor.net/2015/07/01/%E9%9B%B6%E5%9F%BA%E7%A1%80%E5%85%8D%E8%B4%B9%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2-hexo-github/)这样一篇教程,然后跟着一步一步做.

需要准备内容:
* git
* node.js
* github账号

我之前安装过git和node.js,这里就不在安装了,大概介绍下我对两个工具的认识

git是一个免费开源的分布式版本控制系统,简单来说就是用来控制你的代码版本的,可以通过它来实现代码的保存和回滚等功能,在hexo中基本不需要主动使用git操作,只需要安装好环境就可以了,hexo会自己搞定其他的,git的安装也很容易,按照[零基础免费搭建个人博客-hexo+github](http://hifor.net/2015/07/01/%E9%9B%B6%E5%9F%BA%E7%A1%80%E5%85%8D%E8%B4%B9%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2-hexo-github/)中操作即可, git必定是程序猿必备技能之一,推荐[廖雪峰老师git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)

官网上说,[Node.js](http://nodejs.cn/) 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。从这句描述中可以看出,Node.js是个运行环境,跟浏览器中运行的js脚本不是一个层面上的东西. 
也就是说,nodejs是个本地的运行环境,通过java脚本类实现想要实现的逻辑,处理网络网关对应接口传过来的数据请求. 
在Hexo中,nodejs的角色类似于jre之于java应用,dalvik之于apk;
==以上关于node.js的内容 除了第一句话来至于官网,其他均为我自己理解,即胡诌.==

安装好git和node.js就可以安装我们今天的主角hexo了:

* 第一步: 安装 hexo 的命令行工具
		
```
	npm install hexo-cli -g #npm是node.js的包管理器,由此也可看出hexo-cli相当于一个nodejs的应用,是依赖于nodejs存在的.
```
* 第二步: 初始化一个博客

```
	hexo init blog #初始化一个博客系统到blog文件夹
	cd blog #将当前工作目录切换到blog文件夹
```

* 第三步:安装生成器

```
	npm install #命令行会继续下载一堆安装包,等待安装完成
```
* 第四步: 运行默认网站
```
	hexo server #类似于tomcat服务器配置完成之后的默认网站, 运行该命令后会提示你打开 	http:localhost:4000 的网站看效果
```

以上命令完成后即安装完成hexo的运行环境,安装过程中可能因为网络的问题连接不上,毕竟是歪果仁搞出来的东西. 不过hexo是nodejs平台上的一个东东,nodejs在国内也有服务器,可以通过修改npm的源来实现下载,具体操作见[零基础免费搭建个人博客-hexo+github](http://hifor.net/2015/07/01/%E9%9B%B6%E5%9F%BA%E7%A1%80%E5%85%8D%E8%B4%B9%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2-hexo-github/).

接下来是[Github Page](https://pages.github.com/), github 平台提供了一个可以直接将仓库中页面预览的功能,据jekyll官方介绍说是Github Page ispowered by Jekyll, 而Jekyll的功能是将纯文本的内容转换为静态网页,因此推断github page 其实是相当于开启了一个静态的网站模式,开启之后可以将客户端(也就是浏览器)对该连接的请求看做一个普通请求,直接返回对应的内容信息,这样如果对应的内容是个html,浏览器就可以渲染出想要的样式了

![20170314148942290517316.jpg](http://omoo8c568.bkt.clouddn.com/20170314148942290517316.jpg)

由此看出,我们只需要将我们生产的静态网站的内容整体放入github page 对应的仓库中,即可实现自己的静态博客. 幸运的是hexo可以通过配置实现一键生成和发布到github仓库, 具体操作就略了,毕竟已经有很多先驱和很详细的操作步骤了.



## Hexo创建自己的博客
hexo 创建博客主要有三个命令

```
	hexo new "title" // 创建新的博客, 为md格式文本,文件在../blog/source/_posts/title.md
	hexo g // 将编写好的title.md 转换为静态的博客(html + js +css)
	hexo s // 启动本地服务器,预览生成后的样式
	hexo d // 一键发布到github, 假如你已经配置过了
```


##主题
hexo官方有很多主题,可以设置一下

##删除博客
hexo只有创建一篇博客的命令,没有删除的功能,需要手动删除_posts目录下的对应md文件, 使用 hexo clean 清楚对应数据 在hexo g重新生成整个博客.

##分类和标签
hexo new出来的md 文件中, 开始的几行如下图:
![20170314148942389732759.jpg](http://omoo8c568.bkt.clouddn.com/20170314148942389732759.jpg)

看名字就能看出来了,就是对篇内容的分类,都是可以修改的,测试一下就知道了,没什么可聊的.
ps:某些hexo主题是不支持categories的,比如我用的 [Daily](https://blog.hinpc.com/) .


## [多说](http://dev.duoshuo.com) 
添加评论功能, 多说是另一个新的评论系统,可以让博客er 方便的添加和管理评论,根据教程一步步来就可以了,没意思!

## 后记

###自定义域名
之前在腾讯云上买了个自己的域名,之前没时间,都是晚上12点左右写文章,懒得花更多精力去搞域名的事情. 今天想着还是折腾一下,给博客绑定域名.

[官方教程](https://help.github.com/articles/setting-up-a-custom-subdomain/)

好像已经没有什么可写的了,域名的东西也不是很懂.

提两个没想明白的问题吧:
1. 为什么Cname 连接需要 github page 端保存域名? 
	经测试原始的talkfiled.github.io 域名也是可以访问的,说明cname连接是将指向blog.talkfiled.xzy的连接转向了原来的域名, 但当我不在github page 保存新域名是,是不能访问到的, 难道就是因为http请求和https请求的不同?
2. 有没有一个办法可以直接设置普通项目网站?

![20170322149011435341388.jpg](http://omoo8c568.bkt.clouddn.com/20170322149011435341388.jpg)
	
根据github page的设置页面所示, 每个用户/组织可以通过创建名称为username.github.io的仓库构建一个域名. 经测试,如果不是以这种形式创建的仓库,其域名格式为 username.github.io/repository_name 的网址. 

这样网址中有一个斜线,在域名转发填写cname配置的时候通不过验证(确实也不是个合法域名)

有没有办法不使用特殊域名, 直接设置到一个单独项目网址上?

####ps:  经测试, 如果创建了username.github.io 并且配置了自定义域名之后, 新生成的网站链接都会变成自定义域名.
	

