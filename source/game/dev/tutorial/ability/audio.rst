音频播放

小游戏内只有一种音频播放的方式，即使用 InnerAudioContext 来播放。 使用
InnerAudioContext 播放

通过 wx.createInnerAudioContext() 接口可以创建一个音频实例
innerAudioContext ，通过这个实例可以播放音频。

var audio = wx.createInnerAudioContext() audio.src = url // src 可以设置
http(s) 的路径，本地文件路径或者代码包文件路径 audio.play()

在 iOS
系统上，默认遵循静音键设置。如果希望在静音时也能播放声音，可以设置
obeyMuteSwitch 为 false。

audio.obeyMuteSwitch = false

自动播放和循环播放

设置 autoplay 和 loop
属性可以自动播放和循环播放音频，一般适用于背景音乐。

var bgm = wx.createInnerAudioContext() bgm.autoplay = true bgm.loop =
true bgm.src = url

回到前台时恢复背景音乐

当小游戏被隐藏到后台时，所有音频会被暂停，并在回到前台之前都不能再播放成功。

回到前台之后，被暂停的音频不会自动继续播放，如果小游戏有背景音乐的话，需要监听回到前台事件，并在收到回到前台事件之后调用背景音乐继续播放。

wx.onShow(function () { bgm.play() })

处理音频中断事件

音频中断事件指的是在游戏期间，音频被系统打断时触发的事件。音频中断事件分为中断开始和中断结束事件，分别使用
wx.onAudioInterruptionBegin() 和 wx.onAudioInterruptionEnd() 来监听。

以下事件会触发音频中断开始事件：接到电话、闹钟响起、系统提醒、收到微信好友的语音/视频通话请求。被中断之后，小游戏内所有音频会被暂停，并在中断结束之前都不能再播放成功。

中断结束之后，被暂停的音频不会自动继续播放，如果小游戏有背景音乐的话，需要监听音频中断结束事件，并在收到中断结束事件之后调用背景音乐继续播放。

wx.onAudioInterruptionEnd(function () { bgm.play() })

如果小游戏的逻辑强依赖音乐的播放，则需要在音频开始中断的时候暂停游戏

wx.onAudioInterruptionBegin(function () { // 暂停游戏 })

兼容性说明

目前两个平台完全支持的音频格式有
mp3、aac、wav。其他格式存在系统差异，不保证支持。 最佳实践
复用已有的音频实例

对于相同的音效，应该复用已有的音频实例，而不是重新创建一个音频实例。
及时销毁不需要的音频实例

如果一个音频不再需要使用了，可以调用 InnerAudioContext.destroy()
接口提前销毁这个实例。 Android 同时播放的音频数量限制

由于系统限制，在 Android 上最多同时播放 10
个音频，超过的部分会做有损处理，对开发者来说不感知，但开发者应尽量避免同时播放过多音频。
