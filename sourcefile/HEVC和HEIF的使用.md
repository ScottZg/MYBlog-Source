## 前言

## 正文
### 什么是HEVC？
HEVC全称High Efficiency Video Coding。它是高效率视频编码，是符合行业标准的下一代视频编码技术。它是H.264的继承者。
### Apple为什么如此努力地提交一个新编解码器？
苹果想添加一些新的功能特性，但是H.264已经不能满足其需求。比如想要启用新功能，例如4k以及更大的帧尺寸。

苹果在iOS11已经默认启动了HEVC电影和HEIF图像存储。PhotoKit这个Framework将给播放提供数据源，也就是HEVC内容

### 什么是HEIF?
HEIF（读作heef）全称High Efficiency Image File(Format),是高频率图片格式，它是一种静止图像和图像序列的现代容器格式



PhotoKit包含了PhotosUI和Photos

PHAssetResourceManager获取原始资产，它会以原生格式提供给你
AVCapturePhotoOutPut可以提供livePhoto

## 总结
















## 参考
1.[Working with HEIF and HEVC](https://developer.apple.com/videos/play/wwdc2017/511)
2.[High Efficiency Image File Format](https://developer.apple.com/videos/play/wwdc2017/513)