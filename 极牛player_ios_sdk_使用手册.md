# 极牛player iOS SDK 使用手册
***
##SDK 概述
牛直播SDK为iOS平台开发使用的SDK，为iOS开发者提供简单、快捷的接口和开源调用示例，帮助开发者实现iOS平台上的多媒体播放应用。不仅极大降低开发门槛，同时支持客户快速在多个平台发布产品。
***
## GNMoviePlayer SDK功能说明
- 与系统播放器MPMoviePlayerController接口一致，可以无缝快速切换至GNMoviePlayer；
- 本地全媒体格式支持, 并对主流的媒体格式(mp4, avi, wmv, flv, mkv, mov, rmvb 等 )进行优化；
- 支持广泛的流式视频格式, HLS, RTMP, HTTP Rseudo-Streaming 等；
- 实现快速满屏播放，为用户带来更快捷优质的播放体验；
- 版本适配支持iOS 7.0以上版本；
- 业内一流的H.265解码；


***
## 内容摘要
---
- [工程环境](#1)
	* [运行环境](#1.1)
	* [添加工程](#1.2)
	* [添加支持库](#1.3)
- [SDK使用示例](#2)
	* [入口类](#2.1)
	* [调用](#2.2)
- [特性说明](#3)
- [主要接口说明](#4)
	* [播放](#4.1)
	
## 工程环境<h2 id = "1">
---
### 运行环境<h3 id = "1.1">
GNMoviePlayer iOS SDK可运行于 iPhone/iPod Touch/iPad，支持 iOS 7.0 及以上版本; 
### 添加工程<h3 id = "1.2">
- SDK压缩包，将其中的GNLivePush.framework添加到Xcode工程中，具体步骤为：
- 选中应用的Target，进入项目配置页面（General）
- 在Embedded Binaries的列表中添加GNLivePush.framework。

### 添加支持库<h3 id = "1.3">
添加四个系统库：

- VideoToolbox.framework
- libstdc++.6.tbd 或者libstdc++.6.dylib
- libbz2.tbd 或者 libbz2.dylib
- libz.tbd 或者 libz.dylib

## SDK使用示例<h2 id="2">
***
在使用`GNMoviePlayer`时，引入头文件

	#import <GNLivePush/GNLivePush.h>
	
### 入口类<h3 id = "2.1">
- GNPlayer的入口类为**GNMoviePlayer**，包含了播放功能，因此极牛所有的播放功能都需要通过这个类来完成，比如设置播放的属性，开始播放暂停以及停止等。

### 调用<h3 id = "2.2">
初始化player 初始化需要几个步骤：

- 准备需要视频播放的UIView；
- 新建player；
- 设置room_id；
- 调用prepareToPlay开始播放。由于已经设置shouldAutoplay为TRUE，则prepare完成后立即开始播放

```
    GNMoviePlayer *_player;
    UIView *videoView;
    _videoView = [[UIView alloc] init];
    _videoView.frame = CGRectMake( 0, 0,  self.view.bounds.size.width,  self.view.bounds.size.height);;
    _videoView.backgroundColor = [UIColor lightGrayColor];
    _player =    [[GNMoviePlayer alloc] initWithContentRoomId:@"请输入您的room_id"];
    _player.controlStyle = MPMovieControlStyleNone;
    [_player.view setFrame: _videoView.bounds];  // player's frame must match parent's
    [_videoView addSubview: _player.view];
    _videoView.autoresizesSubviews = TRUE;
    _player.view.autoresizingMask = UIViewAutoresizingFlexibleWidth|UIViewAutoresizingFlexibleHeight;
    _player.shouldAutoplay = TRUE;
    _player.scalingMode = MPMovieScalingModeAspectFit;
    [_player prepareToPlay];
```

设置监听 当前提供了四个Notification监听，分别可以获取如下信息：

- MPMediaPlaybackIsPreparedToPlayDidChangeNotification，完成Prepared;
- MPMoviePlayerPlaybackStateDidChangeNotification，播放器状态改变；
- MPMoviePlayerPlaybackDidFinishNotification，播放完成；
- MPMoviePlayerLoadStateDidChangeNotification，数据加载状态改变； 

```
[[NSNotificationCenter defaultCenter]addObserver:self
                                        selector:@selector(handlePlayerNotify:)
                                            name:(MPMediaPlaybackIsPreparedToPlayDidChangeNotification)
                                          object:nil];
[[NSNotificationCenter defaultCenter]addObserver:self
                                        selector:@selector(handlePlayerNotify:)
                                            name:(MPMoviePlayerPlaybackStateDidChangeNotification)
                                          object:nil];
[[NSNotificationCenter defaultCenter]addObserver:self
                                        selector:@selector(handlePlayerNotify:)
                                            name:(MPMoviePlayerPlaybackDidFinishNotification)
                                          object:nil];
[[NSNotificationCenter defaultCenter]addObserver:self
                                        selector:@selector(handlePlayerNotify:)
                                            name:(MPMoviePlayerLoadStateDidChangeNotification)
                                          object:nil];
```
销毁播放器 播放器在stop中完成内存释放，new一次GNMoviePlayer，需要stop一次。如果两者未配对调用，将引发内存泄露。

	  [_player stop];
	  
## 特性说明<h2 id = "3">
***
- 支持h.264/h.265/aac/mp3编码格式；
- 支持rtmp/hls/http-flv直播；
- 支持hls和http点播，封装格式为mp4/flv/ts；

## 主要接口说明<h2 id = "4">
***
以下为播放主要接口说明：

### 播放<h3 id = "4.1">

- `- (instancetype)init:(NSString *)room_id;`

		描述：初始化，设置room_id;

		参数： 播放房间的Room_id;

		说明：

    	在初始化播放器的时候，同时设置播放房间的room_id;

- `- (void)play;`

		描述： 播放当前视频
		
		参数： 无
		
		说明：
		
		play的调用逻辑：
		
		如果已经调用pause暂停了正在播放的视频，则重新开始启动播放视频。如果当前状态为播放，则不发生变化。
		

- `- (void)pause;`

	    描述： 暂停播放当前视频
		
		参数： 无
		
		说明：
		
		pause调用逻辑：

		如果当前视频播放已经暂停，调用该方法将不产生任何效果。
    	重新回到播放状态，需要调用play方法。
    	如果调用pause方法后视频暂停播放，此时播放器状态处于CBPMoviePlaybackStatePaused。
    	播放器内部监听了UIApplicationWillEnterForegroundNotification通知，该通知发生时如果视频仍然在播放，将自动调用pause暂停当前视频播放。

		后台播放逻辑：

    	需要APP有后台执行权限，在工程Info.plist中添加后台运行模式，设置为audio。具体是添加UIBackgroundModes项，值为audio。
    	当用户点击home按钮后，播放器进入后台继续读取数据并播放音频。
    	当APP回到前台后，音频继续播放。图像渲染内容保持和音频同步。
    	如果在开启后台运行模式后，需要切换后台暂停，需要监听相关事件并主动调用pause操作。

- `- (void)stop;`

    	描述： 结束当前视频的播放。

    	参数： 无

    	说明：

    	stop调用逻辑：

        调用stop结束当前播放，如果需要重新播放该视频，需要调用prepareToPlay方法。
        调用stop方法后，播放器开始进入关闭当前播放的操作，操作完成将发送MPMoviePlayerPlaybackDidFinishNotification通知。

	
	