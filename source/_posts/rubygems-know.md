---
title: RubyGems系列之RubyGems初识
date: 2018-09-28 16:18:49
tags: ["Ruby"]
---
转载请注明来源：http://supermokey.com/2018/09/28/rubygems-know/
### 一. 基础理解
RubyGems简称gems，它是一个用于对Ruby组件进行打包的Ruby打包系统。使用它可以方便地下载安装Ruby的软件包到用户的系统。**一个软件包叫做 一个gem，它包含了一个打包的Ruby Application或者一个library**。

### 二. RubyGems安装
1. 首先在[这里](https://rubygems.org/pages/download)下载最新的安装包
2. 解压，然后进入解压的文件夹
3. 执行ruby setup.rb(前提是安装了ruby)
4. 安装完成

### 三. gem命令
**1.gem install**   
用于安装一个gem，例如：
```ruby
gem install xcodeproj  #从本地目录安装或者从远程服务器
gem install xcodeproj -v 1.6.0   #安装特定版本的package
gem install rake --remote  #只从远程服务器获取安装
```
**2.gem  list**   
查看已经安装的gem，例如：
```ruby
gem list --local
gem list  #和上面的命令行等价
gem list test #列出包含gem名称包含test的gem
```
**3.gem update**   
用户更新一个gem，例如：
```ruby
gem update --system #更新gem自身
gem update --system 2.7.6 #更新gem到指定的版本
gem update #更新所有已经安装的gem包
gem update rake   #更新某个gem
```
**4.gem search**  
搜索特定的gem，例如：
```ruby
gem search log --both   #列出本地和远程服务器中名称包含log的gem
gem search log --remote   #只列出远程服务器中名称包含log的gem
```
**5.gem uninstall**  
卸载gem，例如：
```ruby
gem uninstall rake  #卸载rake
```
**6.gem environment**
查看RubyGems的信息

### 四. gem的结构
一个gem的结构如下：
freewill/
|------ bin/
|       |------ freewill
|------ lib/
|       |------- freewill.rb
|------ test/
|       |------ test_freewill.rb
|------ README
|------ Rakefile
|------ freewill.gemspec
其中：
* bin文件夹里面包含了可执行文件，它将会加载到用户的PATH中
* lib文件夹包含了gem的源代码
* test/spec文件夹包含了测试，这取决于开发者使用的测试框架
* Rakefile：每个gem通常都会包含一个Rakefile,rake程序使用它来进行自动化测试、生成代码和执行其他的任务
* README：当安装gem时，将自动为用户生成文档，大多数情况下包含RDoc文档，也有一些包含YARD文档
* freewill.gemspec：该spec文件包含了该gem的信息，例如名字、测试信息、platform、版本号等，更多可以参见：[这里](https://guides.rubygems.org/specification-reference/)

### 五. 总结
这里只是对RubyGems有一个简单的了解，也方便后续命令的查看和实用。后续会继续深入理解学习。