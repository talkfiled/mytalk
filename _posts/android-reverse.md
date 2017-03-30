---
title: Android逆向 (1)
date: 2017-03-20 23:50:39
tags: android逆向
---

过了个周末,不知道该写啥内容了,把之前写的三篇Android逆向相关的放出来充个数吧!

## Android应用的文件结构
Android的安装文件为apk，要逆向Android应用需要先了解apk文件的结构。使用rar或好压之类的压缩软件解压之后可以发现apk的文件结构大致包含如下内容：


![apk结构.png](http://upload-images.jianshu.io/upload_images/1407177-dfeef11457e1b056.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

包含三个文件：AndroidManifest.xml，classes.dex，resources.arsc和四个文件夹assets，lib，res，META-INF；
如果有一定的Android开发经验，我们知道一个Android项目中一般包含如下结构：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1407177-5a212dbb7a52955e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中对应关系如下：

apk结构 | 项目中结构
------- | -------
AndroidManifest.xml | AndroidManifest.xml
lib| libs
res| res
assets| assets
resources.arsc| res
classes.dex| src/gen/libs

* apk中的 AndroidManifest.xml是不能直接使用文本工具打开的，这里的AndroidManifest经过了编译过程，更加节省控件也更方便操作系统执行；
* lib目录对应于项目结构中的libs下的so文件，so为c/c++写的在linux下的可执行文件（elf格式）；
* res文件包含项目res文件下的drawable，layout和图片文件，其中drawable和layout也是经过编译的;
* assets目录下跟原来的assets目录下文件是相同的
resources.arsc文件主要是res中的字符串string.xml的编译后的结果。
* classes.dex是所有java代码的编译结果，包括自己项目中的.java文件，自动生成的gen文件夹下的java文件，引入的jar文件
* META-INF为签名文件，在项目打包apk的时候生成，里面的内容是对每个文件的签名信息，应用安装时会校验每个文件的签名信息，如果校验信息不正确是不能安装成功的。同时，如果同一个apk不同的签名信息进行签名，是不能覆盖安装的。

使用apktool反编译之后的结构如下
![apktoold.png](http://upload-images.jianshu.io/upload_images/1407177-9be5a84c083065fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
其与apk包结构的对应关系如下：

apk结构 | apktool反编译
------- | -------
AndroidManifest.xml | AndroidManifest.xml
lib | lib
res | res
assets| assets
resources.arsc | res
classes.dex | smali

经过apktool反编译之后，可以发生了如下的变化：
1. 所有的xml都可以使用文本编辑器查看了，包括AndroidManifest.xml，res下的drawable和layout文件；
2.res中增加了string.xml，public.xml等一些xml文件；
3.增加了一些smali文件；
通过以上的对应表我们可以知道resources.arsc中的内容为反编译后的string.xml和public.xml文件，classes.dex为所有的smali代码。
4. 反编译后多了一个original文件夹，该文件夹下包含AndroidManifest.xml和META-INF文件，这个是apk结构中原来的AndroidManifest.xml河META-INF的备份；

如上介绍了apk的结构、包括项目结构和apktool反编译之后的结构；从上述描述中可以理解如下两点：
* 1、assets目录下的资源文件不会被编译；res的资源文件都会被编译的；可知如果想要原来的apk中添加一些自己的文件而不被编译影响的话，可以添加到assets目录中，比如各种破解网站自己的启动页所需要的资源；
* 2、直接使用一个文件压缩工具修改一个apk之后是不能直接运行的，原因在于签名，apk的任何修改都会改变该文件的信息，对应的签名验证就不能通过；因此修改apk之后后在对其进行签名就可以正常安装了。

## Android逆向工具（1）

1、apktool
apktool是apk逆向中的大神器，能够将一个apk中dex和资源部分完整的反编译出来，并能够进行回编译操作；
apktool最先是由歪果仁搞出来的，目的是为了方便反编译整个apk，其[官方网站](https://ibotpeaches.github.io/Apktool/)，并且其整个代码是[开源的](https://github.com/iBotPeaches/Apktool)。
shakaapktool是rover12321大神搞出来的，他的github上的[项目首页](https://github.com/rover12421/ShakaApktool)，是一个中文汉化版的apktool,并且提供了更多功能, 例如直接修改对dex进行修复。
apktool工具的核心是一个jar包，使用java命令行进行操作，主要有如下使用：
```
	java -jar apktool.jar d abc.apk -o abc
	java -jar apktool.jar b abc -o abc.apk
```
d命令表示反编译，将abc.apk反编译出来，保存到abc目录
b命令表示回编译，将abc目录中的内容进行编译，生成abc.apk
具体的命令和参数可以使用--help获得；

2、baksmali.jar/smali.jar
baksmali和smali两个项目是哪来的没有研究过，这两个jar的功能是将dex文件进行反编译和回编译的：
java -jar baksmali.jar -o abc  classes.dex
java -jar smali.jar -o classes2.dex abc
basksmali.jar是反编译，将classes.dex反编译出smali代码，放到abc目录中；
smali.jar是回编译，将abc目录中的smali代码编译出dex，命名为classes2.dex;

3、signapk.jar
签名是Android系统的一个验证，修改了apk中的内容这个签名的校验就不能通过了，必须对这个包进行签名，签名不同的两个应用正常情况下是没办法覆盖安装的；
修改玩一个apk之后，要像能够运行必须重新签名，这就是signapk.jar的勇武之地：
java -jar signapk.jar testkey.x509.pem testkey.pk8 111.zip sign_apk.apk
其中testkey.x509.pem和testkey.pk8 是签名文件，每个发布的应用开发者都有自己的签名文件，signapk.jar通过给定的两个签名文件，计算111.zip压缩包中的每个文件的签名信息，并将计算好签名信息的包命名为sign_apk.apk
注：apk生成过程中使用jarsigner来进行签名的，jarsigner是jdk中提供的一个工具，详问google；

4、dex2jar
这个工具用于将dex转换成jar包，dex是Dalvik虚拟机的可执行文件，jar包为.class的压缩包，而.classs是jvm的可执行文件，两者都是通过java语言生成的，因此是存在转换关系的；
dex2jar就是将dex转换为jar的工具，转换不可逆，转换的目的是为了使用jd-gui等工具方便的查看代码
jd-gui：可以以java源码的方式查看jar文件的内容，并且支持反编译出java代码（具体能不能用就看运气了）
jdb：是Android的dex文件逆向神奇，功能类似于dex2jar+jd-gui，但是更加强大，支持一定程度上反混淆；

5、ApkIDE/Android Killer
两大神奇，apkide又称apk改之理，是国内安卓逆向整合的工具，合并了apktool，unicode编码转换，jd-gui，dex2jar，signapk，zipalign等常用工具；

6、ilasm.exe/ildasm.exe
mono开源框架实现了将微软的dll跨平台，Android上也可以使用c#代码生成dll来执行自己的逻辑；
ildasm.exe用于将dll反编译成il文件，这个il就类似于smali，是.net平台的汇编指令，且可以直接进行修改，修改完成之后使用ilasm.exe进行回编译即可；
ildasm Assembly-CSharp.dll  /output=111.il
ildasm支持界面和命令行两种方式，界面点一点就知道怎么搞了；如上命令将dll转换为111.il
ilasm /dll 111.il  /output=111.dll
将111.il转换为111.dll

7、.Net Reflactor/ILSpy
这两个工具是用于查看dll源码的，其中Reflactor是可以通过il指令修改dll的，ILSpy只能查看c#源码，并且两个工具都能将dll完全反编译出c#代码；

## boot.img刷机
学习Android逆向，首先要有自己的装备，其中重要的一点是要有合适的测试机；
我们知道对apk进行逆向时，动态调试是一个重要的技能；要动态调试一个apk需要符合如下两个条件之一：
1、/default.prop文件中ro.debuggable的值为1；
2、apk的AndroidManifest.xml的application标签包含属性：android:debuggable="true"；
逆向中经常要分析加密后的apk，因此对apk的任何修改都有可能损坏apk原来的结构，也就是说2、条件在部分情况下是很难满足的，所以1方法是个一劳永逸的办法；
下面以Nexus4测试机为例介绍修改boot.img中ro.debuggable的值的方法。测试机安装的是nexus4的官方rom；
1、下载官方包nexus4-4.4.4-occam-ktu84p-factory-b6ac3ad6.tgz，即当前手机中安装的rom(以下简称occam.tgz)，正常的话包是可以直接解压的（如果不行的话验证一下md5值，肯定就是下载出错了）
2、解压获取boot.img文件
先解压occam.tgz获取image-occam-ktu84p.zip，在解压该zip即可找到boot.img文件
3、完全解压boot.img获取default.prop文件
下载bootimg.exe可执行文件 [详细参考](http://bbs.pediy.com/showthread.php?t=198328)
按照一下步骤修改boot.img：
  1）完全解压boot.img,
    bootimg --unpack-bootimg；
  2）打开解压以后的default.prop文件，并修改ro.debuggable的值为1；
  3）重新打包boot.img
    bootimg --repack-bootimg
  4）打包之后在相同文件夹下生成boot-new.img文件，即我们修改之后的文件；
4、将boot-new.img刷如手机
  1）进入fastboot模式
    adb reboot bootloader
  2）刷入boot.img
    fastboot flash boot boot-new.img
  3）重启手机
    fastboot reboot
以上过程中所涉及到的[附件](http://pan.baidu.com/s/1bnZFBCb) 密码：tz97
5、案例的详细操作到此就结束了，以上过程在网络中一抓一大把，接下来我再分析下整个刷机过程的原理（胡诌）；
  1）刷机改变了那些内容？
    本实例中我们安装的系统为occam.tgz，又从occam.tgz提取并修改了boot.img刷入手机，因此我们只修改了boot.img部分。那么问题来了，换一个刷机包是不是还通用呢？这个没做过实验，估计要看情况，如果boot.img可以通用就是没有问题的，如果不能通用应该就不行，比如拿nexus 7 的刷机包提取的boot.img应该是行不通的；
  2）bootimg.exe是什么软件，为什么能够解压boot.img文件？
    这个对于搞逆向的技术人员应该不是很难理解的问题，bootimg.exe是一个专用的软件（感谢其作者将这么好的软件公布），就是专门用于解压和打包安卓镜像的，boot.img只是其支持的一种镜像；其原理就是根据boot.img的镜像结构将其分解为人类可以容易理解和修改的形式；也就是说如果你理解了boot.img的结构，你也是可以写出来的；我们使用bootimg.exe就已经在享受作者的劳动成果了；
    同理，bootimg.exe虽然很强大，但是只是针对安卓的有自己的局限性，如果逆向要将其用于其他的镜像解析需要思考一下；
  3）adb 和 fastboot
  adb和fastboot都是Android开发SDK中的工具，adb（Android Debug Bridge）用于实现在电脑端对Android手机的操作，从名称上来看就可以知道，adb 通过建立一个PC端与手机端的连接，实现命令的转发，换句话说需要手机端上运行相应的程序来接受这个adb发送过来的指令并执行才可以（这个程序就是addb），如果手机中addb没有正常启动时不能使用adb对手机进行操作的；
    fastboot模式是什么其实我不太清楚，根据多年装机和看别人装机的经验，该模式下可以进行操作系统文件的读写；一个系统不管外形如何变化，总归是一些文件的组成的；正常的运行模式下操作系统文件读写是受到保护的，都不一定能够找到其存放位置，更别说修改了；这也是为什么不能直接RE管理器查看到的原因；当操作系统总需要安装，这就需要进入一个特殊的模式，能够操作操作系文件的fastboot模式来进行修改；
    上述刷机实例中首先用adb reboot bootloader进入fastboot模式，这个命令是adb的命令；此时在以后的操作都是fastboot的命令了，就是上述的原因，在系统运行时，adb是可以执行命令的，我们想要进入fastboot模式，通过adb 告诉手机从新启动 reboot并进入bootloader模式，此时adb就可以休息了，因为手机端的addb没有启动，adb是不管用的；接下来手机从新启动后进入了fastboot模式，当然fastboot命令就可以执行了；使用fastboot flash boot boot-new.img命令将boot-new.img刷入手机，替换原来的boot；使用fastboot reboot重新启动手机；此时手机进入正常启动的模式，adb什么的又回来了；
6、通过5的分析知道了boot.img是如何刷入手机中的，如果现在想要将手机系统重做，应该怎么操作？


