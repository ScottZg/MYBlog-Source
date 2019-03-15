## 前言
iPhone 6s和iPhone 6s Plus为多点触摸界面带来了强大的3D触摸新维度。这项新技术可以感知用户按下显示屏的深度，让他们比以往任何时候都更能使用你的应用程序和游戏。更多关于3D Touch的介绍可以参见[这里](https://developer.apple.com/ios/3d-touch/)。  

## 正文
接下来会介绍一下所有关于3D Touch开发的一些内容。  

### 0.判断3D Touch是否可用
先判断设备是否支持3D Touch，这里主要用到的类是：[UITraitCollection](https://developer.apple.com/documentation/uikit/uitraitcollection?language=objc)。在iOS9之后，可以使用该类判断设备是否支持3D Touch，苹果官方说明如下：  

> ### 3D Touch and Trait Collections
>
> Starting in iOS 9, you can use this class to check whether the device on which your app is running supports 3D Touch. Read the value of the [`forceTouchCapability`](https://developer.apple.com/documentation/uikit/uitraitcollection/1623515-forcetouchcapability?language=objc) property on the trait collection for any object in your app with a trait environment. For information about trait environments, see [`UITraitEnvironment`](https://developer.apple.com/documentation/uikit/uitraitenvironment?language=objc). For the possible values of the force touch capability property, see the [`UIForceTouchCapability`](https://developer.apple.com/documentation/uikit/uiforcetouchcapability?language=objc) enumeration.
>
> Because a user can turn off 3D Touch in Settings, check the value of the [`forceTouchCapability`](https://developer.apple.com/documentation/uikit/uitraitcollection/1623515-forcetouchcapability?language=objc) property in your implementation of the [`traitCollectionDidChange:`](https://developer.apple.com/documentation/uikit/uitraitenvironment/1623516-traitcollectiondidchange?language=objc)method, and adjust your code paths according to the property’s value.

主要是使用了forceTouchCapability属性，该属性的枚举值包括：  

```objective-c
//未知
UIForceTouchCapabilityUnknown = 0,
//不可用
UIForceTouchCapabilityUnavailable = 1,
//可用
UIForceTouchCapabilityAvailable = 2
```

用户在使用APP的时候也有可能在设置中关闭3D Touch，这个时候可以实现traitCollectionDidChange:代理方法去监听是否改变：(在VC中实现UITraitEnvironment协议)  

```objective-c
#pragma mark - UITraitEnvironment
- (void)traitCollectionDidChange:(UITraitCollection *)previousTraitCollection {
    if (previousTraitCollection.forceTouchCapability == UIForceTouchCapabilityAvailable) {
        [self showAlertWithStrig:@"3D Touch已关闭"];
    }else if (previousTraitCollection.forceTouchCapability == UIForceTouchCapabilityUnavailable) {
        [self showAlertWithStrig:@"3D Touch已打开"];
    }
}
```
这里注意：**拿到的traitcollection是previousTraitCollection**。  

### 1.Home screen quick action API（主屏幕交互）
该API主要用于添加应用程序图片的快捷方式，以预测并加速用户与应用的互动。  

#### 1.1.用例
![Demo](https://raw.githubusercontent.com/ScottZg/MarkDownResource/feature/addrubyimagefile/3DTouch/hsqa-demo.png)

#### 1.2.代码实例
两种方法实现该特性，直接使用代码开发，或者直接在Info.plist文件配置。  

##### 1.2.1.Static quick actions
直接在application:didFinishLaunchingWithOptions:方法中处理：  

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
     
    NSMutableArray *shortCutItemArr = [NSMutableArray arrayWithCapacity:4];
    UIApplicationShortcutIcon *icon1 = [UIApplicationShortcutIcon iconWithType:UIApplicationShortcutIconTypeSearch];
    UIApplicationShortcutItem *shortItem1 = [[UIApplicationShortcutItem alloc] initWithType:@"com.zhanggui.Demo.search" localizedTitle:@"搜索" localizedSubtitle:@"搜索想要的电影" icon:icon1 userInfo:nil];
    [shortCutItemArr addObject:shortItem1];
    [UIApplication sharedApplication].shortcutItems = shortCutItemArr;
    return YES;
}
```
设置shortcutItems即可。  

##### 1.2.2.Dynamic quick actions
直接使用Info.plist文件配置：  

```xml
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>取票码</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.zhanggui.Demo.getTicket</string>
        <key>UIApplicationShortcutItemUserInfo</key>
        <dict>
            <key>key2</key>
            <string>value2</string>
        </dict>
    </dict>
</array>
```

关于key值的介绍，可以参见[Info.plist Keys and Values](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009247)。  
处理点击元素监听：   

```objective-c
- (void)application:(UIApplication *)application performActionForShortcutItem:(UIApplicationShortcutItem *)shortcutItem completionHandler:(void (^)(BOOL))completionHandler {
    NSLog(@"%@",shortcutItem);
}
```

在AppDelegate中实现协议application:performActionForShortcutItem:completionHandler:方法即可。  

#### 1.3.核心类说明
UIApplicationShortcutItem：3D Touch弹框中每一条操作元素。    
UIApplicationShortcutIcon：操作元素的icon。    

##### 1.3.1.UIApplicationShortcutItem

```objective-c
#if USE_UIKIT_PUBLIC_HEADERS || !__has_include(<UIKitCore/UIApplicationShortcutItem.h>)
//
//  UIApplicationShortcutItem.h
//  UIKit
//
//  Copyright © 2015-2018 Apple Inc. All rights reserved.
//

#import <Foundation/Foundation.h>
#import <UIKit/UIKitDefines.h>

NS_ASSUME_NONNULL_BEGIN

@class UIImage;

typedef NS_ENUM(NSInteger, UIApplicationShortcutIconType) {
    UIApplicationShortcutIconTypeCompose,
    UIApplicationShortcutIconTypePlay,
    UIApplicationShortcutIconTypePause,
    UIApplicationShortcutIconTypeAdd,
    UIApplicationShortcutIconTypeLocation,
    UIApplicationShortcutIconTypeSearch,
    UIApplicationShortcutIconTypeShare,
    UIApplicationShortcutIconTypeProhibit       NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeContact        NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeHome           NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeMarkLocation   NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeFavorite       NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeLove           NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeCloud          NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeInvitation     NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeConfirmation   NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeMail           NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeMessage        NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeDate           NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeTime           NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeCapturePhoto   NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeCaptureVideo   NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeTask           NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeTaskCompleted  NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeAlarm          NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeBookmark       NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeShuffle        NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeAudio          NS_ENUM_AVAILABLE_IOS(9_1),
    UIApplicationShortcutIconTypeUpdate         NS_ENUM_AVAILABLE_IOS(9_1)
} API_AVAILABLE(ios(9.0)) API_UNAVAILABLE(tvos) API_UNAVAILABLE(macos);

UIKIT_EXTERN API_AVAILABLE(ios(9.0)) API_UNAVAILABLE(tvos) API_UNAVAILABLE(macos)
@interface UIApplicationShortcutIcon : NSObject <NSCopying>

// Create an icon using a system-defined image.
+ (instancetype)iconWithType:(UIApplicationShortcutIconType)type;

// Create an icon from a custom image.
// The provided image named will be loaded from the app's bundle
// and will be masked to conform to the system-defined icon style.
+ (instancetype)iconWithTemplateImageName:(NSString *)templateImageName;

@end

UIKIT_EXTERN API_AVAILABLE(ios(9.0)) API_UNAVAILABLE(tvos) API_UNAVAILABLE(macos)
@interface UIApplicationShortcutItem : NSObject <NSCopying, NSMutableCopying>

- (instancetype)init NS_UNAVAILABLE;
- (instancetype)initWithType:(NSString *)type localizedTitle:(NSString *)localizedTitle localizedSubtitle:(nullable NSString *)localizedSubtitle icon:(nullable UIApplicationShortcutIcon *)icon userInfo:(nullable NSDictionary<NSString *, id <NSSecureCoding>> *)userInfo NS_DESIGNATED_INITIALIZER;
- (instancetype)initWithType:(NSString *)type localizedTitle:(NSString *)localizedTitle;

// An application-specific string that identifies the type of action to perform.
@property (nonatomic, copy, readonly) NSString *type;

// Properties controlling how the item should be displayed on the home screen.
@property (nonatomic, copy, readonly) NSString *localizedTitle;
@property (nullable, nonatomic, copy, readonly) NSString *localizedSubtitle;
@property (nullable, nonatomic, copy, readonly) UIApplicationShortcutIcon *icon;

// Application-specific information needed to perform the action.
// Will throw an exception if the NSDictionary is not plist-encodable.
@property (nullable, nonatomic, copy, readonly) NSDictionary<NSString *, id <NSSecureCoding>> *userInfo;

@end

UIKIT_EXTERN API_AVAILABLE(ios(9.0)) API_UNAVAILABLE(tvos) API_UNAVAILABLE(macos)
@interface UIMutableApplicationShortcutItem : UIApplicationShortcutItem

// An application-specific string that identifies the type of action to perform.
@property (nonatomic, copy) NSString *type;

// Properties controlling how the item should be displayed on the home screen.
@property (nonatomic, copy) NSString *localizedTitle;
@property (nullable, nonatomic, copy) NSString *localizedSubtitle;
@property (nullable, nonatomic, copy) UIApplicationShortcutIcon *icon;

// Application-specific information needed to perform the action.
// Will throw an exception if the NSDictionary is not plist-encodable.
@property (nullable, nonatomic, copy) NSDictionary<NSString *, id <NSSecureCoding>> *userInfo;

@end

NS_ASSUME_NONNULL_END

#else
#import <UIKitCore/UIApplicationShortcutItem.h>
#endif
```
常用方法：  
创建一个UIApplicationShortcutItem：   

```objective-c
- (instancetype)initWithType:(NSString *)type localizedTitle:(NSString *)localizedTitle localizedSubtitle:(nullable NSString *)localizedSubtitle icon:(nullable UIApplicationShortcutIcon *)icon userInfo:(nullable NSDictionary<NSString *, id <NSSecureCoding>> *)userInfo NS_DESIGNATED_INITIALIZER;
 
- (instancetype)initWithType:(NSString *)type localizedTitle:(NSString *)localizedTitle;
```
其中：  

- type代表：必传，app定义的主屏幕快速操作类型，**可以用于确定某元素点击**。
- localizedTitle：展示的title
- localizedSubtitle：sub title
- icon：该item左侧或者右侧的icon，类型为UIApplicationShortcutIcon
- userInfo:用户自定义的一些信息

##### 1.3.2.UIApplicationShortcutIcon
icon有两种获取方式：一种是使用系统自带的类型，一种是用户自定义（根据图片名）。  

###### 1.3.2.1 系统自带类型
```objective-c
// Create an icon using a system-defined image.
+ (instancetype)iconWithType:(UIApplicationShortcutIconType)type;
```
系统自带类型包括以下几种：   

![系统自带icon类型](https://raw.githubusercontent.com/ScottZg/MarkDownResource/feature/addrubyimagefile/3DTouch/iconType.png)

###### 1.3.2.1 用户自定义
```objective-c
// Create an icon from a custom image.
// The provided image named will be loaded from the app's bundle
// and will be masked to conform to the system-defined icon style.
+ (instancetype)iconWithTemplateImageName:(NSString *)templateImageName;
```
用户自定义的图片格式规定如下：  
>square, single color, and 35x35 points（正方形、单色、35*35大小）

如果图片找不到，则展示为原点。效果可自己测试一下。   

#### 1.4.使用方式
1. 直接使用Info.plist文件进行配置  
2. 直接编写代码  

当同时使用两者时，会进行叠加，并且Info.plist配置中的元素会优先于AppDelegate中配置的元素展示。  

#### 1.5.个数限制
**常规情况下，最多可以自定义4个Home screen quick action。在APP未上线之前，可以看到自定义的4个,多余的将会被忽略。在APP上线之后，将会有一个系统自带的item：分享 “APP名称”。总共不超过5个。**  

#### 1.6.使用场景
能够进行快捷操作的事件，比如微信的扫一扫，微信支付、我的电影票等。  

### 2.UIKit peek and pop API(预览和跳转)
UIKit的peek和pop API允许开发者在维护用户上下文的同时，在应用中提供对附加内容的轻松访问。使用peek quick actions可以为应用的触摸和按住操作提供按下的替换。  
peek：当用户点击特定的view，会提供一个额外的预览视图。  
pop：确认查看该内容，并且导航到该内容详情。  
在iOS9以及以后的SDK中，为UIViewController提供了注册3D Touch和取消注册3D Touch的新方法，它们是：  
```objective-c
// Registers a view controller to participate with 3D Touch preview (peek) and commit (pop).
- (id <UIViewControllerPreviewing>)registerForPreviewingWithDelegate:(id<UIViewControllerPreviewingDelegate>)delegate sourceView:(UIView *)sourceView NS_AVAILABLE_IOS(9_0);
- (void)unregisterForPreviewingWithContext:(id <UIViewControllerPreviewing>)previewing NS_AVAILABLE_IOS(9_0);
```

在注册方法中，sourceView就是要添加3D Touch的view。调用该注册方法的时候，它做了三件事：  

- 注册调用该方法的控制器参与3D Touch的预览（preview）和执行（commit）
- 从接收方的视图层级结构中，将sourceView指定为响应强按压的视图
- 指定委托对象，当用户进行强按压时依次请求peek和pop，该委托对象用于协调操作（vc实现了peek和pop代理方法）

>You can designate more than one source view for a single registered view controller, but you cannot designate a single view as a source view more than once.

你可以在一个vc中指定多个sourceView，但是不能同一个view指定为sourceView多次。  
注册方法返回的上下文对象的生命周期由系统管理。如果你需要指明取消注册该vc，把该context对象传给unregisterForPreviewingWithContext:。如果开发者不取消注册，系统会在改VC释放的时候自动取消注册。  
Demo中的注册方法实现如下：  
```ojective-c
if ([self.imageView respondsToSelector:@selector(traitCollection)]) {
    if([self.traitCollection respondsToSelector:@selector(forceTouchCapability)]) {
        if (self.traitCollection.forceTouchCapability == UIForceTouchCapabilityAvailable) {
            [self registerForPreviewingWithDelegate:(id)self sourceView:self.imageView];
        }
    }
}
```
注册完成之后，需要实现UIViewControllerPreviewingDelegate，该代理主要有两个方法：  
```objective-c
- (void)previewingContext:(id<UIViewControllerPreviewing>)previewingContext commitViewController:(UIViewController *)viewControllerToCommit {
    ImageViewController *imageVC = [[UIStoryboard storyboardWithName:@"Main" bundle:nil] instantiateViewControllerWithIdentifier:@"ImageVC"];
    [self presentViewController:imageVC animated:YES completion:nil];
}
- (UIViewController *)previewingContext:(id<UIViewControllerPreviewing>)previewingContext viewControllerForLocation:(CGPoint)location {
    ImageViewController *imageVC = [[UIStoryboard storyboardWithName:@"Main" bundle:nil] instantiateViewControllerWithIdentifier:@"ImageVC"];
    imageVC.preferredContentSize = CGSizeMake(0.0, 300);
    return imageVC;
}
```
第一个方法用于处理commit操作，也就是确认操作，确认3D Touch执行操作。  
第二个方法在用户按下源视图显示peek时调用，实现此方法用以返回预览视图控制器。（这里返回的是ImageViewController）。如果此处返回nil，将会禁用预览。此处还会使用到**previewingContext.sourceReact**，该属性主要是在sourceView坐标系中，矩形响应用户的3D触摸，当矩形周会内容模糊时，矩形在视觉上保持清晰，具体可参见视频：  [https://github.com/ScottZg/MarkDownResource/blob/feature/addrubyimagefile/3DTouch/sourcReact.MP4](https://github.com/ScottZg/MarkDownResource/blob/feature/addrubyimagefile/3DTouch/sourcReact.MP4)

#### 2.1.快速操作 
可以使用该API进行快速操作需求开发，例如微信重压聊天列表中的某个cell，就可以弹出快速操作：不再关注、删除。这些快速操作很简单：在预览的VC中添加下面的代码即可。实例代码如下：  
```objective-c
- (NSArray<id<UIPreviewActionItem>> *)previewActionItems {
    NSMutableArray *arrItem = [NSMutableArray arrayWithCapacity:2];
    UIPreviewAction *cancel = [UIPreviewAction actionWithTitle:@"取消" style:UIPreviewActionStyleDefault handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"cancel");
    }];
    UIPreviewAction *ok = [UIPreviewAction actionWithTitle:@"删除" style:UIPreviewActionStyleDestructive handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        self.imageView.image = nil;
    }];
    
    UIPreviewAction *select = [UIPreviewAction actionWithTitle:@"选中" style:UIPreviewActionStyleSelected handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"selected");
    }];
//    组操作
    UIPreviewAction *add = [UIPreviewAction actionWithTitle:@"增加" style:UIPreviewActionStyleDefault handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"增加");
    }];
    UIPreviewAction *update = [UIPreviewAction actionWithTitle:@"更新" style:UIPreviewActionStyleDefault handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"更新");
    }];
    UIPreviewActionGroup *group = [UIPreviewActionGroup actionGroupWithTitle:@"更多操作" style:UIPreviewActionStyleDefault actions:@[add,update]];
    [arrItem addObject:cancel];
    [arrItem addObject:ok];
    [arrItem addObject:select];
    [arrItem addObject:group];
    return arrItem;
}
```
这里主要实现了previewActionItems方法，用来设置预览的Action元素，这里涉及到的类有：     
* UIPreviewAction：预览的Action，当用户在支持3D Touch并且在peek视图下向上滑动，即可看到这些Action。
* UIPreviewActionItem：协议，包含了一个只读的title。
* UIPreviewActionGroup：操作组，可以设置一个操作组进行操作，例如二次确认。

##### 2.1.1.UIPreviewAction
具体的操作元素，包括title、类型以及事件处理。其中style有以下三种类型：  

```
typedef NS_ENUM(NSInteger,UIPreviewActionStyle) {
    UIPreviewActionStyleDefault=0,  //默认
    UIPreviewActionStyleSelected,  //选中
    UIPreviewActionStyleDestructive,  //销毁：红色
} NS_ENUM_AVAILABLE_IOS(9_0);
```
##### 2.1.2.UIPreviewActionItem
协议，包含了一个属性title。代表操作action的title。   

#### 2.1.3.UIPreviewActionGroup
操作组，支持一个元素多个操作，具体可以参见下面的小视频：[https://github.com/ScottZg/MarkDownResource/blob/feature/addrubyimagefile/3DTouch/group.MP4](https://github.com/ScottZg/MarkDownResource/blob/feature/addrubyimagefile/3DTouch/group.MP4)

### 3.Web view peek and pop API
在网页中，对于网页中的链接，peek和pop是默认支持的，可以通过设置allowsLinkPreview进行开启或关闭： 
WKWebView:  
```objective-c
/*! @abstract A Boolean value indicating whether link preview is allowed for any
 links inside this WKWebView.
 @discussion The default value is YES on Mac and iOS.
 */
@property (nonatomic) BOOL allowsLinkPreview API_AVAILABLE(macosx(10.11), ios(9.0));

```
UIWebView：  

```objective-c
@property (nonatomic) BOOL allowsLinkPreview NS_AVAILABLE_IOS(9_0); // default is NO
```

默认情况下使用苹果自带的浏览器打开，这样就会跳出自己的应用。  
尝试自己对wkwebview进行注册，然后自己设置peek和pop，发现无效。  
可以使用SFSafariViewController替代WebView。  

>SFSafariViewController使用presentViewController:animated:completion:方法展示。效果和push一样。

### 4.UITouch force properties

[UITouch](https://developer.apple.com/documentation/uikit/uitouch?language=objc)类提供了两个新的属性来支持自定义实现3D Touch：  

```objective-c
// Force of the touch, where 1.0 represents the force of an average touch
@property(nonatomic,readonly) CGFloat force NS_AVAILABLE_IOS(9_0);
// Maximum possible force with this input mechanism
@property(nonatomic,readonly) CGFloat maximumPossibleForce NS_AVAILABLE_IOS(9_0);
```
具体使用可以参见[Demo](https://github.com/ScottZg/3DTouch)。  

### 参考文档
1. [Adopting 3D Touch on iPhone](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/Adopting3DTouchOniPhone/index.html#//apple_ref/doc/uid/TP40016543-CH1-SW1)
2. [https://developer.apple.com/ios/3d-touch/](https://developer.apple.com/ios/3d-touch/)
3. [registerForPreviewingWithDelegate](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621463-registerforpreviewingwithdelegat)