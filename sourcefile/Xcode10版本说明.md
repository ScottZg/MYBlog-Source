### 前言
这里主要介绍一下Xcode10 版本主要更新的内容。
随着iOS12的发布，Xcode10已经可以从Mac App Store下载。
Xcode10包含了iOS12、watchOS 5、macOS10.14以及tvOS 12的SDK。另外，开发者可以从Xcode中看到当前Deployment Target最低为iOS8，所以后续开发过程中个人觉得可以忽略iOS8之前的一些特性。
要想安装Xcode10，mac系统版本必须是macOS 10.13.6或者更高。这里对主要的一些变更做一些介绍（并非所有）。

### 新特性
* NSData类型的数据可以直接导出来。例如一张图片转成data之后，在快速浏览框可以直接将data导出，最终保存成包含二进制的文件。

  ![Export](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/export-data.gif)

* 新建的schemes默认被所有用户共享。如果想建一个私人的scheme，在Manage Schemes中不选择Shared即可。

  ![scheme-manager](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/scheme-manage.gif)

* 快捷键“Ctrl+0”可以打开弹出scheme选择，“Ctrl+Shift+0”可以弹出运行设备选择。当弹框出来的时候，可以直接使用键盘上的上下左右选择，然后点击回车即可选中。

  ![快捷键](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/shortcut.gif)

* Library控件库的位置从原来的右下角移动到了顶部，默认选中一个控件往StoryBoard拖得时候Library控件库会关闭，按住Option键可以在拖动控件的时候保持Library库不消失：

  ![Option-Library](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/option-library.gif)

* Library的位置从底部移动到了Inspector的上方window中。现在变得更加灵活，想Spotlight Search那样可以随意拖动，当元素拖动的时候，该Library框就会消失。但是如果按住Option键拖动，该弹框就不会消失。

### 现有问题
如果打开存储在iCloud Drive中保存的Xcode工程或者workspaces，或者是为存储在iCloud Drive中的工程通过git(其他version管理工具)切换分支，可能会导致Xcode终止运行。

### 已解决的问题
* 新建的文件.h默认包含NS_ASSUME_NONNULL_BEGIN 和NS_ASSUME_NONNULL_END。这两个宏之间的代码，所有指针对象都会假定为nonull，所以我们在开发的时候只需要指定那些nullable的对象即可。

  ![默认非空](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/micro.gif)

### 弃用的东西
* C++库libstdc++在Xcode8的时候已经弃用，在Xcode10以及之后将不再支持(iOS target)。C++工程必须迁移到libc++。除了修改c++标准库的build setting，开发者还应该查看一下硬编码的linker flags和target的依赖，删除libstdc++(包括-lstdc++、-lstdc++6.0.9、libstdc++6.0.9.tbd和libstdc++6.0.9.dylib)的引用。项目中依赖的针对libstdc++构建的静态归档文件也需要针对libc++重新构建。(也就是说我们需要检查一下Target的Build Phases，里面的Link Binary With Libraries如果包含上述提到的库，需要删除，并且移除DrivedData)。
* 移除了对Subversion的支持。
* Xcode10是最后一个支持Swift3的版本。通过打开Project>Edit> Convert>To Current Swift Syntax..来将Swift3的代码转成swift4.2。
* macOS 10.14SDK不再支持32位程序。如果开发者需要编译i386，使用Xcode9.4或者更早版本的Xcode。

### 设备
使用Xcode10运行老版本Xcode构建的WatchKit app，将会报错：“TheWatchKit app has an invalid stub executable”。解决办法：Clean build文件夹，然后从新运行。

### 文档查看
* 按住option查看Quick Help的弹框展示内容变成了单列布局格式，并且选中查看的方法在弹框中展示的背景颜色和当前Xcode主题颜色一致：(当前为黑色主题，弹框的注释内容也是黑色)

  ![Quick Help](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/quickhelp.png)
  
### 模拟器
* 原来在模拟器的Edit菜单有专门的Get PasteBoard && Send PasteBoard来做复制粘贴同步，现在在该菜单下有一个专门的item来处理复制粘贴操作：AutoMatically Sync PasteBoard。也就是说只要使用这个功能即可实现粘贴复制在mac和模拟器数据的同步。

  ![模拟器数据自动同步](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/automatic-pasteboard.png)

### 编辑器
* 支持多cursor编辑。快捷键：shift+control+点击(如果想取消某个cursor，则再次点击即可)

  ![multi-cursor](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/multi-cursor.gif)

* 不连续选中操作支持：control+shift+点击

  ![不连续选中操作](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/xcode10_featrure/selection.gif)