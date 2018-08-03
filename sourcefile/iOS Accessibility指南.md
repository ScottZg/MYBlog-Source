# iOS Accessibility 指南
开发者经常会为用户开发一些令人充满惊喜的App。但是，开发者真的为每一个潜在的用户都做适配了么？是否每个人都可以真正使用你的APP呢？
设计APP、产品或者任何类型的服务，都要考虑到所有用户，包括视力、运动、学习或者听力有障碍的人。
Apple不断地向开发者提供持续更新的工具，以便在设计的时候考虑到辅助功能。
在这个指南中，你将从一个已经开发完成的APP入手，让其变得更易于访问。你将会学习到以下知识点：
1. 如何使用VoiceOver     
2. 如何使用Accessibility Inspector    
3. 如何在UIKit中使用accessibility元素    
4. 如果让你的APP为残疾人士提供更好的用户体验    

本指南使用的是Swift语言，如果你对Swift了解不多，可以先学习Swift，然后再阅读。

> 注意：你需要一台物理设备来进行VoiceOver实验--这个accessibility特性不支持模拟器

## 开始
在学习过程中你用到的Demo APP是一个类似食谱的App，它展示了不同难度级别的食谱，而且可以对你做过的食物进行评分。
你可以从[这里](https://koenig-media.raywenderlich.com/uploads/2016/09/Recipe_starter.zip)下载这个Demo APP。在你运行到真机之前，你需要配置相关的签名。(具体配置就不在多说了，如果你想在模拟器运行，则不用配置)。
运行起来之后，可以先对此工程进行简单的了解。下面是对几个类的介绍：
1. RecipeInstrucationViewController.swift 点击列表的详情页    
2. InstructionViewModel.swift是RecipeInstructionsViewController的数据源。    
3. RecipeCell.swift 是rootviewController的表格中的cell。    
4. Recipe.swift是model对象，代表一个recipe。    
5. InstructionCell.swift是详情页的cell，一个checkbox+可添加删除线的label。当checkbok选中时，label添加删除线    
6. RecipeListViewController.swift是根视图，展示列表    

了解了本工程的大体结构之后，我们就开始添加一些accessibility特性。

## 为什么是Accessibility？
开始编码之前，我们首先要理解accessibility的好处：
1. 无论你是使用的KIF框架还是进行Xcode中的UI测试，设计可访问性的应用程序都可以更轻松地编写功能测试。
2. 你还可以通过让更多的用户使用你的APP来扩大市场和用户客群。
3. 如果你为政府机构工作，则需要进行508合规性检测，其中规定所有用户都必须可以使用此APP以及其中的功能。
4. 此外，如果你做的APP给用户提供了帮助，哪怕微不足道，也会令你很有成就感。

## Enabling VoiceOver
VocieOver screen-reading 工具可以让用户在不看屏幕的情况下和你的软件进行交互，它是专门为有视觉障碍的用户而设计的。
VoiceOver是UI和用户touch输入的链接，它提供了不同组件的可听性描述以及应用程序中所采取操作的可听性反馈。
要想使用VoiceOver，首先打开设置app，然后通用-->辅助功能(Accessibility)-->VoiceOver(旁白)，打开即可。
## 如何使用VoiceOver
打开了VoiceOver，接下来对一些操作手势进行简单的学习：
* 单击。你可以在屏幕上点击任一点，VoiceOver都会根据项目的可访问属性说出识别信息，后面会详细介绍
* 双击将执行选中的选项。
* 用力下按可以对表格进行滑动操作

## 使用VoiceOver
在设置中打开VoiceOver之后，运行食谱APP到手机，然后你可以按照上面的操作对该APP进行简单操作，你会发现一些问题：
1. 图像的描述并不准确，只是读出了图片的名称
2. 并没有说出每个食谱的困难等级，导致此功能对于使用VoiceOver的用户无效。

接下来就来解决这两个问题。

## Accessibility（辅助功能）属性
为了支持可访问性，辅助功能属性是你必须要实现的核心组件。
VoiceOver使用这些属性为用户提供有关应用中显示元素的可听信息。
辅助功能有四个属性：
1. Label：一种是被控件或者视图的方法，例如“后退按钮” 和 “食谱图像”
2. Traits：描述元素的状态、行为或用法。按钮的特征可能是”已选中“
3. Hint（提示）：描述元素完成的操作，例如：”显示配方详情“
4. Frame：元素在屏幕中的frame，格式为CGRect。VoiceOver会说出CGRect的内容
5. Value：元素的值，想想一下使用进度条或者滑动条，当前的value可能会读作：”5 out of 100“

大多数的UIKit组件都具有这些属性，你需要对属性进行相关的设置使VoiceOver的内容更有意义。

## Accessibility Inspector
在修复上面所说的两个问题时，你将会用到Xcode的一个工具：Accessibility Inspector。它可以做的事情有：
* 通过运行你的APP找到常见的辅助功能问题。
* 允许你在检查模式下检查UI元素的辅助功能属性
* 提供所有辅助元素的实时预览，而无需离开你的应用
* 支持所有平台：macOS、iOS、watchOS和tvOS

要想打开该工具，直接在Xcode菜单，然后Xcode-->Open Developer Tool-->Accessibility Inspector:
![how open Accessibility Inspector](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/accessibility/openaccessibilityInspector.png)
它的主界面是这样的：
![主界面](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/accessibility/interface.png)   
在Target Chooser处，你可以选择不同的设备以及不同的APP。将Recipes工程使用模拟器运行，然后使用该工具选择模拟器。
### Inspection Pointer
选中Inspection Pointer,接下来将鼠标移动到模拟器，你会看到类似VoiceOver一样的选框，你可以使用该工具进行元素查看以及操作。
Inspection Deatil框会展示选中元素的一些信息以及操作:
![详情框内容展示](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/accessibility/detail.png)
### Inspector Audit
确定你的模拟器还在运行，并且当前展示的是食谱的列表，在inspector中，点击Audit 图标，然后点击Run audit。你会立即发现inspector已经找到一些缺少描述的元素。当你点击一个警告，模拟器中对应的元素会高亮。这种情况下，和cell关联的图片视图没有描述:
![图片没有描述](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/accessibility/imagenodesc.png)  
如果你点击 ？号的小按钮，你会得到一些建议来如何解决这个问题，你只需要按照它的建议做一些调整即可。
![question mark image](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/accessibility/questionmark.png)
你（如果是QA同学）可以点击eye图标然后创建一个截图，方便向RD提交bug:
![截图](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/accessibility/screenshot.png)

### Inspector Settings
该工具还允许你测试下面的Accessibility设置：
1.Invert Color 反转颜色
2.Reduce Transparency  降低透明度
3.Reduce Motion 减少动画
4.Change font size 改变字体大小
你不再需要返回到设置APP中来设置这些特性。直接在Accessibility Inspector中操作即可。
尽管Accessibility Inspector可以做的事情很多，但是你还是应该手动去测试VoiceOver，来看看用户使用的时候究竟如何表现。

## 给Recipe APP添加可访问性操作
还看一下刚才的图片没有accessibility label。打开Xcode，然后打开RecipeCell.swift，然后添加下面的代码在文件的底部：

```swift 
extension RecipeCell {
    func applyAccessibility(_ recipe: Recipe) {
        foodImageView.accessibilityTraits = UIAccessibilityTraitImage
        foodImageView.accessibilityLabel = recipe.photoDescription
        
    }
}
```

这段代码添加了确实的辅助功能属性：
1. accessibilityTraits采用了表征可访问性元素的特性掩码。在这种情况下，UIAccessibilityTraitImage表示它是一个图像
2. accessibilityLabel 是用来描述元素。这里用了recipe.photoDescription，它代表对图片的内容描述。可以在VoiceOver中读出。

在RecipeCell中找到configureCell( _ : )，然后添加下面一行代码到方法的结尾：

```swift
applyAccessibility(recipe)
```

然后在真机上运行，（打开VoiceOver）再次点击图像，你就会听到读的内容(为photoDescription)。
这样不是更好吗？不是听到的image，而是提供了详细的图片描述，用户可以想象这种食物，而不是对图片内容一无所知。

## 难以接触的Label
接下来处理一下食谱难度的label。你需要使这些label可以访问，并且更新它的属性信息，提供有意义的描述。
在RecipeCell.swift中，添加下面的代码到applyAccessibility( _ : ):

```swift
        difficultyLabel.isAccessibilityElement = true
        difficultyLabel.accessibilityTraits = UIAccessibilityTraitNone
        difficultyLabel.accessibilityLabel = "Difficult Label"
        switch recipe.difficulty {
        case .unknown:
            difficultyLabel.accessibilityValue = "Unkown"
        case .rating(let value):
            difficultyLabel.accessibilityValue = "\(value)"
        }
```

下面是对上述代码的一些介绍：
1. isAccessibilityElements是一个标识，当为true的时候，该item对accessibility可见。对于大多数的UIKit元素，默认值为true，但是对于UILabel，默认值是false
2. accessibilityTraits表示可访问元素。因为不需要交互，只需要将其设置为UIAccessibilityTraitNone即可。
3. 接下来，你就需要设置VoiceOver读出来的内容了。"Difficulty Level"告知用户这个元素是做什么的
4. accessibilityValue是设置困难等级，使元素变得更加有意义

接下来打开VoiceOver并重新编辑运行，此时点击困难等级，便会阅读”Difficulty Level 1/2/3/4/5“。

## 再次检查警告
每次你公开新的辅助功能元素时，再次运行一次。你会发现如下警告：
![字体大小警告](https://raw.githubusercontent.com/ScottZg/MarkDownResource/master/accessibility/dynamicfont.png)
用户将无法改变label字体的大小。点击？号按钮，按照其提供的建议修改即可。
在RecipeCell.swift中的applyAccessibility( _ : )方法底部，添加如下代码：

```swift
difficultyLabel.font = UIFont.preferredFont(forTextStyle: .body)
difficultyLabel.adjustsFontForContentSizeCategory = true
```

设置preferedFont为body意味着将Label的font设置为正文样式，大小和字体的细节取决于辅助功能设置。adjustsFontForContentSizeCategory是iOS10的新特性，表示当用户更改文本内容大小时，字体应自动更新。
其他的功能自己可以使用真机进行更改查看。其中的Invert Color为反转颜色。具有敏感性、视力不佳或者在某些情况下色盲的用户，可以使用此功能。
此时，使用Inspector工具提供的改变字体大小功能便可以调整字体的大小。

## Recipe Detail Screen
接下来看一下食谱的详情页面。你会发现下面的问题：
1. 返回按钮并没有很好的描述出navigation buttton,导致用户不知道按钮是做什么用的，设置为返回button可能会更好。
2. 点击表情，VoiceOver读出的内容无法描述出来按钮具体是做什么的。
3. checkbox也没有表达出来相应的意思。相比于内容阅读，用户更关心的是当前的状态是什么含义。

打开RecipeInstructionsViewController.swift，然后添加下面的代码到viewDidLoad中(assert(recipe != nil)后面)：

```swift
backButton.accessibilityLabel = "back"
backButton.accessibilityTraits = UIAccessibilityTraitButton
```
此时，VoiceOver将会说出”back button“，而不是”left arrow button“。
还是在这个文件中，使用下面的代码替换isLikedFoot( _ : ):

```swift
  func isLikedFood(_ liked: Bool) {
    if liked {
        likeButton.setTitle("😍", for: .normal)
        likeButton.accessibilityLabel = "like"
        likeButton.accessibilityTraits = UIAccessibilityTraitButton
        didLikeFood = true
    } else {
        likeButton.setTitle("😖", for: .normal)
        likeButton.accessibilityLabel = "Dislike"
        likeButton.accessibilityTraits = UIAccessibilityTraitButton
        didLikeFood = false
    }
  }
```
这样喜欢和不喜欢的按钮便有了比较明显的含义。再次运行可以查看。
接下来就是checklist。打开InstructionCell.swift，然后查看shouldStrikeThroughText( _ : )，替换if strikeThrough为下面代码：

```swift
 checkmarkButton.isAccessibilityElement = false
    if strikeThrough {
        descriptionLabel.accessibilityLabel = "Complete \(text)"
   attributeString.addAttribute(NSAttributedStringKey.strikethroughStyle, value: 2, range: NSMakeRange(0, attributeString.length))
    }else {
        descriptionLabel.accessibilityLabel = "UnComplete \(text)"
    }
```
接下来点击步骤，便可以清楚地告知用户是否完成步骤。

# 结束
这里只是对Accessibility做了一个入门的介绍，告诉用户工具的使用以及简单的关于Accessibility的属性的赋值操作。

原文整体内容翻译自[iOS Accessibility Tutorial: Getting Started](https://www.raywenderlich.com/142058/ios-accessibility-tutorial)，但是里面的内容有所调整。有些内容不代表原作观点。

# 参考
1.[Making Your iOS App Accessible](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/iPhoneAccessibility/Making_Application_Accessible/Making_Application_Accessible.html#//apple_ref/doc/uid/TP40008785-CH102-SW4)
2.[iOS Accessibility Tutorial: Getting Started](https://www.raywenderlich.com/142058/ios-accessibility-tutorial)