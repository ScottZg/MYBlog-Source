# 前言
在群里看到WebDriverAgent这个东西，出于好奇，便开始百度+谷歌，最终对其有了简单的了解。也对自动化测试也有了一个初步的了解。接下来你看到的是对WebDriverAgent的一些介绍。    
# 正文
WebDriverAgent简称WDA。它是由Facebook推出的一款移动端测试框架。它是在iOS客户端实现了一个WebDriver的Server，借助这个server，我们可以远程控制iOS设备进行测试。  
它更多的是测试人员进行自动化测试用的，所以在 [testerhome](https://testerhome.com/topics/4904) 上有一些文章对其进行了介绍。具体的文章链接可以参见文末的附录。
## 原理
据官方介绍，它是通过链接XCTest.framework和调用苹果的API直接在设备上执行命令。在网上看到一张图：
![图片来源于网络](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/WDA/wda_theory.png)
先忽略这张图，我们可以先看一个Xcode自带的软件：Accessibility Inspector。直接打开Xcode，然后点击菜单的Xcode-->Open Developer Tool即可找到该软件。这个软件按照名称来理解的话简单可以理解为可用性检查员。通过选择不同的设备(Mac、Simulator、iPhone)，可以对其进行检查，例如我直接选择我的mac，然后选择微信那个进程，你会发现该软件的首页是Quicklook，也就是对微信的一个概览。你可以点击页面上的类似瞄准器的按钮，当你选中之后，颜色会变成蓝色，这个时候将鼠标移动到微信界面上，你会发现使用它可以检查微信mac版本的各个元素，并且可以查看各个元素的属性，如果该元素具有Actions，你还可以进行点击操作。也就是说你完全不用直接去操作微信，而直接使用Accessibility Inspector进行操作即可。基于此，我们可以利用查看的层级结构，进行UI自动化测试。更多的关于Accessibility可以参见[iOS Accessibility Tutorial](https://www.raywenderlich.com/142058/ios-accessibility-tutorial)。对这个的理解我现在只停留在对工具的使用，后续会继续进行学习。    
也就是基于Accessibility、XCTest.framework和UIAutomation.framework，才实现了对APP的各种操作，而WDA就是通过在被测设备上安装了WebDriverAgentRunner，然后通过WDA的一些API进行了服务器和客户端交互。基于API进行编写业务测试脚本，最后将脚本运行实现自动化测试的过程。   
## 特性
在WDA的[Github](https://github.com/ScottZg/WebDriverAgent)上也给出了WDA的特性：   
1.支持真机 &&模拟器   
在模拟器上运行还是比较方便的，在真机上需要进行证书配置、进行端口转发。这个下面会介绍到。  
2.实现了大多数的[WebDriver Spec](https://w3c.github.io/webdriver/)      
3.实现了部分[Mobile JSON Wire Protocol Spec](https://github.com/SeleniumHQ/mobile-spec/blob/master/spec-draft.md)  
如果想进一步了解Mobile JSON Wire Protocol Spec是什么，可以查看附录的1链接      
4.支持[USB连接](https://github.com/facebook/WebDriverAgent/wiki/USB-support)设备   
USB连接设备的时候，需要代理转发，这里用到了mobiledevice(或者使用usbmuxd)方法很简单直接进行端口代理即可（可参见本条特性的外链）。   
5.提供了一个Inspector，可以很直观地查看当前设备的状态以及元素tree。  
6.方便上手，它可以直接通过Xcode启动和调试  
7.Unsupported yet，but works with tvOS & OS X

## 安装
WDA的安装很方便，只需要执行下面几个步骤就搞定(前提是你安装了各种环境支持，npm,node,carthage等)：
第一步：下载工程，下载链接点[这里](https://github.com/facebook/WebDriverAgent)。
第二步：在解压后的工程根目录执行下面脚本：
```
./Scripts/bootstrap.h
```
该操作会通过Carthage安装所有的依赖、使用npm构建inspector包。
第三步：打开WebDriverAgent.xcodeproj,开启WebDriverAgentRunner test即可。
## 使用
选中要执行的设备，按照安装的第三步执行，即可启动WDA，执行UITestingUITests，当console输出如下信息则说明启动成功：
```
2018-06-27 15:56:14.352191+0800 WebDriverAgentRunner-Runner[9119:362446] +[CATransaction synchronize] called within transaction
2018-06-27 15:56:14.477644+0800 WebDriverAgentRunner-Runner[9119:362446] Running tests...
objc[9119]: Class VCWeakObjectHolder is implemented in both /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/System/Library/PrivateFrameworks/AVConference.framework/Frameworks/ViceroyTrace.framework/ViceroyTrace (0x12bfce4d0) and /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/System/Library/PrivateFrameworks/AVConference.framework/AVConference (0x12b0fae38). One of the two will be used. Which one is undefined.
2018-06-27 15:56:16.007610+0800 WebDriverAgentRunner-Runner[9119:362446] Continuing to run tests in the background with task ID 1
Test Suite 'Selected tests' started at 2018-06-27 15:56:16.612
Test Suite 'WebDriverAgentRunner.xctest' started at 2018-06-27 15:56:16.613
Test Suite 'UITestingUITests' started at 2018-06-27 15:56:16.613
Test Case '-[UITestingUITests testRunner]' started.
    t =     0.00s Start Test at 2018-06-27 15:56:16.614
    t =     0.00s Set Up
2018-06-27 15:56:16.617879+0800 WebDriverAgentRunner-Runner[9119:362446] Built at Jun 26 2018 18:43:04
2018-06-27 15:56:16.708704+0800 WebDriverAgentRunner-Runner[9119:362446] ServerURLHere->http://172.19.156.187:8100<-ServerURLHere
```
关键是最后一句话，显示了Server 的URL，直接将其拷贝，然后在浏览器打开即可。
地址后面添加/status可以查看当前设备（你使用的哪个设备运行，就是哪个设备的状态）的状态。  
地址后添加/inspector即可打开inspector，查看元素，界面如下：
![](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/WDA/inspector.png)  
到这里，你就可以按照WDA提供的API进行脚本编写了，API地址为https://github.com/facebook/WebDriverAgent/wiki/Queries 。里面有很多命令，按照命令即可编写shell脚本进行自动化测试。例如从APP中回到主屏幕的操作(相当于点击了home button)：
```shell
curl -X POST -H "Content-Type: application/json" -d "" http://localhost:8100/wda/homescreen
```
可以先看一下API提供的语句：
```shell
curl -X GET $JSON_HEADER $DEVICE_URL/status
```
其中：
$JSON_HEADER代表：' -H "Content-Type: application/json" '   
$DEVICE_URL代表刚才的ServerURL。    
但是这种方式写起来比较麻烦，在网上又进行了查找，最终发现了[facebook-wda](https://github.com/openatx/facebook-wda)非官方提供，但是很好用，安装方式也比较简单，直接执行命令（前提是安装了python以及pip）：
```shell
pip install --pre facebook-wda
```
然后就可以使用python进行脚本编写：
```python
#coding:utf-8
import wda
driver = wda.Client('http://172.13.156.187:8100')
# setting_session = driver.session('com.apple.Preferences')
# setting_session(text=u'通用',className='Cell').tap()
#点击home button
driver.home()
```
这样写起来看着就比较方便了。简单做一个对比：
![](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/WDA/wda-normal-compare.png)  
WDA提供的API操作有很多，直接查看相关文档即可。另外，当时风靡一时的跳一跳python脚本执行，就是基于WDA进行的。想自己尝试的可以查看附录2链接。亲测可用。有一点需要注意的是在wechat_jump_game项目中，你要把对应的config.json文件拷贝到这个根目录，这样wecaht_jump_auto_iOS.py脚本才可以读到这个配置。

#结束语
WDA的使用还有很多方面，网易游戏团队[ATX](https://github.com/NetEaseGame/ATX)测试框架进行iOS测试就是使用的WDA，另外比较知名的[appium](http://appium.io/)也采用的WDA。有兴趣可以对其进行研究学习。
在学习的过程中发现了testerhome这个网站，应该是QA同学比较钟爱的网站，里面有各种文章以及问答，大多数都是关于测试技术相关，强烈推荐给QA同学。

# 附录
1.[作为移动测试人员，你应该知道的移动自动化测试协议 Mobile JSON Wire Protocol Specification](https://testerhome.com/topics/924)
2.[基于python+WebDriverAgent的跳一跳小程序高分教程](https://juejin.im/post/5a52d510f265da3e290c0970)
3.[iOS测试 WebDriverAgent简介](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/WDA/inspector.png)
4.[iOS自动化实践——WebDriverAgent(一)](https://diaojunxian.github.io/2016/07/22/iOS%E8%87%AA%E5%8A%A8%E5%8C%96%E5%AE%9E%E8%B7%B5%E2%80%94%E2%80%94WebDriverAgent-%E4%B8%80/)
5.[iOS自动化实践——WebDriverAgent(二)](https://diaojunxian.github.io/2016/07/24/iOS%E8%87%AA%E5%8A%A8%E5%8C%96%E5%AE%9E%E8%B7%B5%E2%80%94%E2%80%94WebDriverAgent-%E4%BA%8C/)

6.[iOS自动化实践——WebDriverAgent(三)](https://diaojunxian.github.io/2016/07/26/iOS%E8%87%AA%E5%8A%A8%E5%8C%96%E5%AE%9E%E8%B7%B5%E2%80%94%E2%80%94WebDriverAgent-%E4%B8%89/)