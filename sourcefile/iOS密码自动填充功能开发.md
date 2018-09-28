# 引言

在《iPhone User Guide for iOS 11.4》这本书中，介绍了iOS11的新特性。其中在Safari的章节中，介绍了Fill in forms。也就是当你在网页登录、注册以及购买的时候，用户可以通过键盘来填充网页的表单、或者是Safari自己填充（前提是你开启了AutoFill）。

AutoFill功能打开方式：设置 → Safari → 自动填充（AutoFill）。使用AutoFill有两种功能：

1.保存你的密码：你在网页中输入密码，Safari会询问您是否为你登录的网站保存密码，点击YES即可。

2.为新账号提供建议密码：点击密码区域，点击提示密码，点击使用建议的密码，就可以使用建议密码

![img](http://wiki.maoyan.com/download/attachments/23529588/autofill.png?version=3&modificationDate=1533529724000&api=v2&effects=border-simple,blur-border)

上图为注册B站的流程，打开Safari，然后打开该网站，就可以进行注册，注册在输入新密码的时候，会有提示密码，点击提示密码就会弹出图（1）所示，Safari会为你生成一个建议密码，然后你使用建议密码即可（2），再次登录时直接选中已经存储的账号密码即可。

这里还有一个很不错的功能点：当你登录与该网站关联的APP时，系统会自动帮你填充账号密码，达到两步登录效果（点击提示即可自动输入账号密码，然后点击登录即可登录）

如果想查看已经保存的密码，可以直接打开设置 → 账号和密码 → 应用与网站密码，即可查看当前存储的密码:

![img](http://wiki.maoyan.com/download/attachments/23529588/setting.png?version=1&modificationDate=1533533567000&api=v2&effects=border-simple,blur-border)

接下来就简单实现一个类似上述功能的DEMO：在网页登录之后，在APP可以直接进行Password AutoFill。会从四个方面进行介绍：

1. 如何展示出来QuickType Bar
2. 保证正确的UI展示
3. 让QuickType Bar显示正确的信息
4. 针对第三方服务进行认证

## 如何展示出来QuickType Bar

所谓QuickType Bar，就是你使用输入框调起键盘的时候键盘上面的Bar，要想展示出来QuickType Bar，首先要满足的一个条件就是：**在设置的账户和密码中必须要有保存的账号密码**。

如下图：

![img](http://wiki.maoyan.com/download/attachments/23529588/quicktypebar.png?version=1&modificationDate=1533541232000&api=v2)

左侧为在账号和密码中没有任何账号和密码的情况，右侧为保存了一个账号和密码的情况。也就是说只要在系统的“账号与密码”中有保存数据，QuickType Bar就会出现一个锁，点击即可选择当前已经保存的账号和密码，达到填充输入效果。

只要使用的UITextField或者是UITextView，都是可以在没有开发任何代码的情况下展示QuickType Bar。当然如果你使用的不是这两个控件，那么需要实现<UITextInput>协议。实现此协议之后也可以使用该特性。问题来了：苹果系统如何知道你在“账号与密码”中保存的内容如何自动填充到你自己开发的页面表单中呢？这里就用到了textContentType：

textContentType表示文本输入区所期望的语义。举个例子你可以执行输入框的textContentType为：UITextContentTypeEmailAddress，这样当你输入内容的时候，系统可以在某些情况下自动选择适当的键盘。默认情况下，这个属性是nil。

系统在iOS11之前包含的contentType有：（注意iOS系统版本限制）

在iOS11新增了两个UITextContentTypeUsername 和 UITextContentTypePassword ，只需要设置textfield的这个属性即可：

这样系统便会根据你所这只的ContentType将账号和密码按照你的设置填入相应的输入框内。如果你的账号是邮箱，可以将contentType设置为UITextContentTypeUsername，把键盘的keyboardType设置为：UIKeyboardTypeEmailAddress。

## 保证正确的UI展示

我们只要设置了两个输入框的textContentType，就可以将指定的信息填入指定的textfield中。但是有的时候有这种情况：在两个输入框内容均填写之后，登录按钮才可以点击。这种情况下，我们需要处理一下UI操作。从点击QuickType Bar的钥匙图标到UITextField填充内容，你会发现没有任何的视图声明周期调用。所以在视图生命周期处理UI展示明显是不可行的。你可以通过UITextFieldDidChange代理方法进行处理。这里就不再赘述。

## 让QuickType Bar显示正确的信息

直接看一下第一张图的5，那么问题来了，QuickType Bar怎么知道它要展示的是特定APP的账号和密码呢？这里就需要将app和网页进行关联。下面详细介绍一下。

### APP设置

首先我们需要对APP进行设置：

![img](http://wiki.maoyan.com/download/attachments/23529588/image2018-8-6_17-24-20.png?version=1&modificationDate=1533547460000&api=v2&effects=border-simple,blur-border)

先打开Associated Domains，你可以直接在<https://developer.apple.com/>登录，然后进入Certificates, Identifiers & Profiles，选择你的Identifiers，点击编辑，勾选**Associated Domains**。

完成操作之后，按照上面的格式进行添加即可。webcredentials:网站地址。添加完成之后，APP设置完成。

### 服务器设置

创建一个名为**apple-app-site-association**的json文件(不要添加json后缀)，然后里面填入的信息如下：

**apps内容格式为TeamId.Bundle Identifier**。

然后将此json文件放到域名根目录，或者.well-known目录下面，只要能够使用如下网址能够访问到即可：

这里是某站的访问链接：<https://www.bilibili.com/.well-known/apple-app-site-association>。它的格式是[Universal Links格式](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html?utm_source=revxblog#//apple_ref/doc/uid/TP40016308-CH12-SW2)，并没有按照但依然可以正确使用。why？？？。

到这里就完成了服务端的设置。

完成以上两步，便可实现Password AutoFill功能，并且能够在QuickTypeBar展示。

但是按照现有的方式，只有我们在Safari登录之后并且存储密码，才能在APP中使用。（如果你知道网址，可以手动的在“账号与密码”中添加）。现在了解到的做这个功能的我看到的只有B站。下面是其简单的原理图：

![img](http://wiki.maoyan.com/download/attachments/23529588/appanwebsite.png?version=1&modificationDate=1533547333000&api=v2&effects=border-simple,blur-border)

## 针对第三方服务进行认证

针对第三方账号登录的情况，苹果推荐使用Safari View Controller,应该是使用该Controller加载三方验证登录，然后通过Safari View Controller将数据进行保存吧。大部分APP如果有三方登录，直接是唤起三方APP（如微信），然后在微信执行操作再返回自己的APP，这种方式个人理解暂时无法完成此项功能。

具体实现大家自行研究。

# 总结

使用此方法的确可以大大降低用户的输入操作成本，但是要现在Safari登录（手机Safari和Mac Safari）就显得比较尴尬了。Mac的也可以进行同步，但是只不过需要时间。不过如果用户将自己的账号密码保存到了系统中的“账号与密码”中，这种操作还是十分方便的。

现阶段的同步只是将Safari的登录信息同步到了APP，据说iOS12可以将APP的信息同步到网站，而且移动APP支持注册时填写推荐密码（textContentType新增了newPassword。当前只有Safari支持推荐密码）。不过现阶段还没有升级到iOS12，感兴趣的可以查看WWDC2018，里面有对这一块的介绍。

# 附

1.[WWDC2017关于Password AutoFill的介绍](https://developer.apple.com/videos/play/wwdc2017/206/)

2.[Implementing AutoFill Credential Provider Extensions](https://developer.apple.com/videos/play/wwdc2018/721)

3.[Automatic Strong Passwords and Security Code AutoFill](https://developer.apple.com/videos/play/wwdc2018/204)

4.[Support Universal Links](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html?utm_source=revxblog)

 