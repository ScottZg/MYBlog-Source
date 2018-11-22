---
title: 对Link Map File的初步认识
date: 2018-11-20 21:01:47
tags: ["LinkMapFile","iOS"]
---
Link Map File中文直译为链接映射文件，它是在Xcode生成可执行文件的同时生成的链接信息文件，用于描述可执行文件的构造部分，包括了代码段和数据段的分布情况。Xcode在生成可执行文件的时候默认情况下不生成该文件，需要开发者手动设置Target --> Build Setting --> Write Link Map File为YES：
![Link Map Setting](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/linkmapfile/linkmapsetting.png)
这里还可以设置Link Map存放的位置，默认的位置为：

```shell
$(TARGET_TEMP_DIR)/$(PRODUCT_NAME)-LinkMap-$(CURRENT_VARIANT)-$(CURRENT_ARCH).txt
```
例如：
```shell
/Users/zhanggui/Library/Developer/Xcode/DerivedData/LinkMapTest-ffnpzjkbsmhwvdcxorqbxpyvjtob/Build/Intermediates.noindex/LinkMapTest.build/Debug-iphonesimulator/LinkMapTest.build/LinkMapTest-LinkMap-normal-x86_64.txt
```
开发者也可以根据自己的需要自行设置该文件的位置。
### Link Map File的组成
打开Link Map File，里面包含了以下几个部分：
#### 1. Path
```
# Path: /Users/zhanggui/Library/Developer/Xcode/DerivedData/LinkMapTest-ffnpzjkbsmhwvdcxorqbxpyvjtob/Build/Products/Debug-iphonesimulator/LinkMapTest.app/LinkMapTest
```
Path是生成可执行文件的路径。
#### 2. Arch
```
# Arch: x86_64
```
Arch指代架构类型。
#### 3. Object files：
```objective-c
# Object files:
[ 0] linker synthesized
[ 1] /Users/zhanggui/Library/Developer/Xcode/DerivedData/LinkMapTest-ffnpzjkbsmhwvdcxorqbxpyvjtob/Build/Intermediates.noindex/LinkMapTest.build/Debug-iphonesimulator/LinkMapTest.build/LinkMapTest.app-Simulated.xcent
[ 2] /Users/zhanggui/Library/Developer/Xcode/DerivedData/LinkMapTest-ffnpzjkbsmhwvdcxorqbxpyvjtob/Build/Intermediates.noindex/LinkMapTest.build/Debug-iphonesimulator/LinkMapTest.build/Objects-normal/x86_64/ViewController.o
[ 3] /Users/zhanggui/Library/Developer/Xcode/DerivedData/LinkMapTest-ffnpzjkbsmhwvdcxorqbxpyvjtob/Build/Intermediates.noindex/LinkMapTest.build/Debug-iphonesimulator/LinkMapTest.build/Objects-normal/x86_64/main.o
[ 4] /Users/zhanggui/Library/Developer/Xcode/DerivedData/LinkMapTest-ffnpzjkbsmhwvdcxorqbxpyvjtob/Build/Intermediates.noindex/LinkMapTest.build/Debug-iphonesimulator/LinkMapTest.build/Objects-normal/x86_64/AppDelegate.o
[ 5] /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator12.1.sdk/System/Library/Frameworks//Foundation.framework/Foundation.tbd
[6]/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator12.1.sdk/usr/lib/libobjc.tbd
[7]/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator12.1.sdk/System/Library/Frameworks//UIKit.framework/UIKit.tbd
```
Object Files列举了可执行文件里所有的obj以及tbd。每一行代表对文件的编号。例如ViewController.o文件，其编号为2。编号的具体作用稍后介绍。
#### 4. Sections
```
# Sections:
# Address	Size    	Segment	Section
0x100001730	0x00000333	__TEXT	__text
0x100001A64	0x0000002A	__TEXT	__stubs
0x100001A90	0x00000056	__TEXT	__stub_helper
0x100001AE6	0x00000A27	__TEXT	__objc_methname
0x10000250D	0x0000003C	__TEXT	__objc_classname
0x100002549	0x0000086D	__TEXT	__objc_methtype
0x100002DB6	0x0000007A	__TEXT	__cstring
0x100002E30	0x00000182	__TEXT	__entitlements
0x100002FB4	0x00000048	__TEXT	__unwind_info
0x100003000	0x00000010	__DATA	__nl_symbol_ptr
0x100003010	0x00000010	__DATA	__got
0x100003020	0x00000038	__DATA	__la_symbol_ptr
0x100003058	0x00000010	__DATA	__objc_classlist
0x100003068	0x00000010	__DATA	__objc_protolist
0x100003078	0x00000008	__DATA	__objc_imageinfo
0x100003080	0x00000BE8	__DATA	__objc_const
0x100003C68	0x00000010	__DATA	__objc_selrefs
0x100003C78	0x00000008	__DATA	__objc_classrefs
0x100003C80	0x00000008	__DATA	__objc_superrefs
0x100003C88	0x00000008	__DATA	__objc_ivar
0x100003C90	0x000000A0	__DATA	__objc_data
0x100003D30	0x000000C0	__DATA	__data
```
单从字面含义理解：每个Section包含了Address、Size、Segment以及Section。介绍之前，这里先简单介绍一下Mach-O文件。
上面第一部分的Path是可执行文件的路径，使用iTerm进去到该文件夹，然后使用file命令即可查看该文件的类型：
```shell
file LinkMapTest
```
输出结果为：
```shell
LinkMapTest: Mach-O 64-bit executable x86_64
```
可以知道该文件是一个Mach-O格式的文件，它是iOS系统应用执行文件格式。Mach-O文件中的虚拟地址最终会被映射到物理地址上，这些地址会被分为不同的段类型：_ _ TEXT 、_ _ DATA以及_ _ LINKEDIT等。各个段的含义如下：

1. _ _ TEXT包含了被执行的代码。这些代码是只读、可执行
2. _ _ DATA包含了包含了将会被更改的数据，例如全局变量、静态变量等，可读写，但是不可执行
3. _ _ LINKEDIT 包含了加载程序的元数据，比如函数名称和地址，只读。

Segment又被划分成了不同的Section，不同的Section存储了不同的信息，例如 _ _ objc _ methname 为方法的名称。  
再回顾上面的Sections，Address是起始位置、Size是大小、Segment是段、Section。
#### 5. Symbols
```shell
# Address	Size    	File  Name
0x100001730	0x0000003C	[  2] -[ViewController viewDidLoad]
0x100001770	0x00000092	[  3] _main
0x100001810	0x00000080	[  4] -[AppDelegate application:didFinishLaunchingWithOptions:]
0x100001890	0x00000040	[  4] -[AppDelegate applicationWillResignActive:]
0x1000018D0	0x00000040	[  4] -[AppDelegate applicationDidEnterBackground:]
0x100001910	0x00000040	[  4] -[AppDelegate applicationWillEnterForeground:]
0x100001950	0x00000040	[  4] -[AppDelegate applicationDidBecomeActive:]
0x100001990	0x00000040	[  4] -[AppDelegate applicationWillTerminate:]
0x1000019D0	0x00000020	[  4] -[AppDelegate window]
0x1000019F0	0x00000040	[  4] -[AppDelegate setWindow:]
0x100001A30	0x00000033	[  4] -[AppDelegate .cxx_destruct]
0x100001A64	0x00000006	[  5] _NSStringFromClass
0x100001A6A	0x00000006	[  7] _UIApplicationMain
0x100001A70	0x00000006	[  6] _objc_autoreleasePoolPop
0x100001A76	0x00000006	[  6] _objc_autoreleasePoolPush
0x100001A7C	0x00000006	[  6] _objc_msgSendSuper2
0x100001A82	0x00000006	[  6] _objc_retainAutoreleasedReturnValue
0x100001A88	0x00000006	[  6] _objc_storeStrong
0x100001A90	0x00000010	[  0] helper helper
0x100001AA0	0x0000000A	[  5] _NSStringFromClass
0x100001AAA	0x0000000A	[  6] _objc_autoreleasePoolPop
0x100001AB4	0x0000000A	[  6] _objc_autoreleasePoolPush
0x100001ABE	0x0000000A	[  6] _objc_msgSendSuper2
0x100001AC8	0x0000000A	[  6] _objc_retainAutoreleasedReturnValue
0x100001AD2	0x0000000A	[  6] _objc_storeStrong
0x100001ADC	0x0000000A	[  7] _UIApplicationMain
0x100001AE6	0x0000000C	[  2] literal string: viewDidLoad
. . .
```
根据Sections的起始地址，可以将Symbols分为Sections个数的组，例如0x100001730到0x100001A64之间，就是 _ _ test代码区。  
Symbols包含的信息有：  
1. Address：起始地址
2. Size：所占内存大小，这里使用16进制表示。
3. File：该Name所在的文件编号，也就是Object files部分的中括号的数字，例如-[ViewController viewDidLoad]对应的文件编号为2，根据Object files部分可以看到所属的文件为：ViewController.o。这样可以计算某个o文件所占内存的大小。只需要把Symbols中编号为o编号Symbols累加统计即可。
4. Name就是该Sybmols的名称。
#### 6. Dead Stripped Symbols
```shell
# Dead Stripped Symbols:
#        	Size    	File  Name
<<dead>> 	0x00000018	[  2] CIE
<<dead>> 	0x00000018	[  3] CIE
<<dead>> 	0x00000006	[  4] literal string: class
<<dead>> 	0x00000008	[  4] literal string: v16@0:8
<<dead>> 	0x00000018	[  4] CIE
. . .
```
上面便是对Link map file做了简单的介绍。   
### itools
花了两天的时间，根据对Link Map File的学习，使用Ruby写了一个脚本文件，可以方便地统计出指定Link Map File中的组件或者tbd占用内存大小，类似：
```
AppDelegate.o          8.50KB
ViewController.o          735B
LinkMapDemo.app-Simulated.xcent          386B
main.o          192B
 linker synthesized          128B
libobjc.tbd          120B
Foundation.tbd          24B
UIKit.tbd          24B
总大小为(仅供参考)：10.07KB
```
想了解更多可以访问[https://github.com/ScottZg/itools](hhttps://github.com/ScottZg/itools)
### 总结
1. 苹果开发还是有很多细节的东西需要去学习去了解。
2. 学习一门脚本语言，也会给平时的开发带来很大的方便。

### 参考
1. [Mach-O可执行文件](https://objccn.io/issue-6-3/)
2. [iOS调优|深入理解Link Map File](https://www.jianshu.com/p/52e0dee35830)
3. [iOS APP可执行文件的组成](http://blog.cnbang.net/tech/2296/)

转载请注明出处：[http://supermokey.com/2018/11/20/link-map-file-known/](http://supermokey.com/2018/11/20/link-map-file-known/)