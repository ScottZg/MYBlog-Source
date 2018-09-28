转载请注明来源：[https://www.cnblogs.com/zhanggui/p/9720818.html](https://www.cnblogs.com/zhanggui/p/9720818.html)
#### 一. 前言
我们可以在rubygems.org中下载安装他人创建的gem。现在，我们尝试一下自己创建一个gem，然后发布，供他人使用。

#### 二. 制作gem
这里开始创建一个zhola的gem，主要作用是输出Hello world。首先创建一个如下tree的文件夹:
![tree](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/gems/foldertree.png)
创建完成之后，在zhola.rb中填写如下代码：
```ruby
class Zhola
    def self.hi
        puts "Hello world!"
    end
end
```
然后设置一下gemspec文件：
```ruby
Gem::Specification.new do |s|
    s.name          = 'zhola'
    s.version       = '0.0.1'
    s.date          = '2018-09-28'
    s.summary       = 'Zhanggui first gem'
    s.description   = '第一次创建gem文件'
    s.authors       = ["Zhanggui"]
    s.email         = 'scottzg@126.com'
    s.files         = ["lib/zhola.rb"]
    s.homepage      = 'http://rubygems.org/gems/zhola'
    s.license       = 'MIT'
end
```
这里的homepage为即将发布到的page。   
然后在当前文件夹下执行build命令：
```ruby
gem build zhola.gemspec
```
执行成功的结果为：
```
 Successfully built RubyGem
  Name: zhola
  Version: 0.0.1
  File: zhola-0.0.1.gem
```
执行成功之后，便会在文件夹中创建zhola-0.0.1.gem文件。此时gem制作成功。
#### 三. 安装创建的gem
在刚刚创建的gem文件目录下执行命令：
```ruby
gem install ./zhola-0.0.1.gem
```
输出的结果为：
```
Successfully installed zhola-0.0.1
Parsing documentation for zhola-0.0.1
Done installing documentation for zhola after 0 seconds
1 gem installed
```
通过上述命令将刚才创建的gem安装到本机。   

#### 四. 查看是否安装成功
打开终端，然后使用irb命令进行测试：
```ruby
zhola irb --simple-prompt --noecho
>> require "zhola"
>> Zhola.hi
Hello world!
>>
```
当终端输出Hello world！，说明已经可以正常使用刚才创建的gem。

#### 五. 将gem发布到Rubygems.org
首先，需要注册一个账号，直接在https://rubygems.org/ 注册即可，然后打开终端，在终端输入：
```ruby 
curl -u ***** https://rubygems.org/api/v1/api_key.yaml  # ***** 为你的用户名
```
紧接着输入密码，回车之后便会得到一个api_key。  
接着在gem文件夹中输入下面的指令：
```ruby
 gem push zhola-0.0.1.gem   # push后面为生成的gem名称
```
push成功之后终端显示：
```
Pushing gem to https://rubygems.org...
Successfully registered gem: zhola (0.0.1)
```
此时说明已经发布到rubygems成功，其他人直接使用:
```ruby
gem install zhola
```
即可使用刚才发布的gem。 该gem的homepage为：https://rubygems.org/gems/zhola 。

#### 六. 总结
至此，我们就已经发布了一个gem到rubygems。如果觉得上面的这种方式比较麻烦，需要创建文件夹、创建gemspec等等，也可以直接使用bundle进行创建。这个后面有时间在介绍。
