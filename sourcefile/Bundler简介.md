### Bundler介绍
Bundle 通过追踪和安装所需的gem以及gem版本，为Ruby项目提供了一致的环境。

### Gemfile
开发过程中经常会看到Gemfile，它是一个用来描述Ruby程序gem依赖项的文件。它里面记录了要想执行Ruby code需要的gem依赖。  
一般把Gemfile放到根目录下面，例如Rails，把Gemfile放到了和Rakefile同一级的目录下。

### 常见命令
#### bundle init
该命令会初始化一个Gemfile，执行结束会再当前目录创建一个Gemfile，然后默认文件中的内容如下：
```ruby
# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

gem "rails"
```
接下来就可以把自己需要的gem添加到上述文件中，例如上面添加的
#### bundle install
执行bundle install，会将Gemfile中依赖的gem下载：
```shell
Using bundler 1.17.1
Using mini_portile2 2.3.0
Using nokogiri 1.8.5
Bundle complete! 1 Gemfile dependency, 3 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```
并且会生成一个Gemfile.lock，里面内容如下：
```ruby
GEM
  remote: https://rubygems.org/
  specs:
    mini_portile2 (2.3.0)
    nokogiri (1.8.5)
      mini_portile2 (~> 2.3.0)

PLATFORMS
  ruby

DEPENDENCIES
  nokogiri

BUNDLED WITH
   1.17.1
```
这里记录了gem以及gem依赖的版本。为了开发环境统一，把Gemfile和Gemfile.lock添加到git版本控制。这样能确保工程其他开发者使用的相同的三方代码版本。
#### bundle exec 
在bundle的上下文中执行命令。确保Gemfile中指定的gem在Ruby环境中都可用。例如我们在执行pod install的时候，有可能不同开发者安装的cocoapods版本也不同，可能会因为版本不一致产生异常，这个时候可以在Gemfile中指定cocoapod版本，这样如果版本不一致，会提示用户bundle install安装指定版本的cocoapod，此时执行pod install的时候可以使用下列命令代替：
```shell
bundle exec pod install
```

想要了解更多关于Bundler的知识，可以访问[bundler.io](https://bundler.io/)，只要是关于bundler的命令，你都可以在搜索中查询，而且文档十分丰富。
### 使用Bundler创建rubygem
Bundler也可以快速方便地创建gems。创建一个标准的gem project使用下面命令：
```shell
bundle gem my_gem
```
my_gem为gem名称。执行该命令之后，会创建一个rubygem工程骨架。

### 参考文献
[Bundler官网](https://bundler.io/)
[Bundler入门](https://bundler.io/v1.17/#getting-started)

