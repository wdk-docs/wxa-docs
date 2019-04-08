快速上手
========

安装开发工具
------------

前往 开发者工具下载页面 ，根据自己的操作系统下载对应的安装包进行安装。

你的第一个小游戏
----------------

新建项目选择小程序项目，选择代码存放的硬盘路径。
目前小游戏不提供公开注册，可点击体验小游戏使用无 AppID 模式。
给你的项目起一个好听的名字，最后，勾选“建立游戏快速启动模板”（注意：你要选择一个空的目录才会有这个选项），点击确定，你就得到了你的第一个小游戏了。

点击顶部菜单编译就可以在 IDE 预览你的第一个小游戏。

真机预览
--------

点击工具上的编译按钮，可以在工具的左侧模拟器界面看到这个小游戏的表现。点击预览按钮，通过微信的扫一扫在手机上体验你的第一个小游戏。

文件结构
--------

小游戏只有以下两个必要文件：

::

   ├── game.js
   └── game.json

1. game.js 小游戏入口文件
2. game.json 配置文件

配置
----

小游戏开发者通过在根目录编写一个 game.json
文件进行配置，开发者工具和客户端需要读取这个配置，完成相关界面渲染和属性设置。

+-----------------+-----------------+-----------------+-----------------+
| key             | 数据类型        | 说明            | 默认值          |
+=================+=================+=================+=================+
| deviceOrientati | String          | 支持的屏幕方向  | portrait        |
| on              |                 |                 |                 |
+-----------------+-----------------+-----------------+-----------------+
| showStatusBar   | Boolean         | 是否显示状态栏  | false           |
+-----------------+-----------------+-----------------+-----------------+
| networkTimeout  | Number          | 网络请求的超时时间，单位：毫秒 | 60000 |
+-----------------+-----------------+-----------------+-----------------+
| networkTimeout. | Number          | wx.request      | 60000           |
| request         |                 | 的超时时间，单位：毫秒 |          |
+-----------------+-----------------+-----------------+-----------------+
| networkTimeout. | Number          | wx.connectSocke | 60000           |
| connectSocket   |                 | t               |                 |
|                 |                 | 的超时时间，单位：毫秒 |          |
+-----------------+-----------------+-----------------+-----------------+
| networkTimeout. | Number          | wx.uploadFile   | 60000           |
| uploadFile      |                 | 的超时时间，单位：毫秒 |          |
+-----------------+-----------------+-----------------+-----------------+
| networkTimeout. | Number          | wx.downloadFile | 60000           |
| downloadFile    |                 | 的超时时间，单位：毫秒 |          |
+-----------------+-----------------+-----------------+-----------------+
| workers         | String          | 多线程 Worker   | 无              |
|                 |                 | 配置项，详细请参考 |              |
|                 |                 |                 |                 |
|                 |                 | Worker文档      |                 |
+-----------------+-----------------+-----------------+-----------------+

deviceOrientation
-----------------

值 说明 portrait 竖屏 landscape 横屏

示例配置
--------

{ “deviceOrientation”: “portrait”, “networkTimeout”: { “request”: 5000,
“connectSocket”: 5000, “uploadFile”: 5000, “downloadFile”: 5000 } }

wx API
------

你只能使用 JavaScript 来编写小游戏。小游戏的运行环境是一个
绑定了一些方法的 JavaScript VM。不同于浏览器，这个运行环境没有 BOM 和
DOM API，只有 wx API。接下来我们将介绍如何用 wx API
来完成创建画布、绘制图形、显示图片以及响应用户交互等基础功能。 创建
Canvas

调用 wx.createCanvas() 接口，可以创建一个 Canvas 对象。

var canvas = wx.createCanvas()

此时创建的 canvas 是一个上屏
Canvas，已经显示在了屏幕上，且与屏幕等宽等高。

console.log(canvas.width, canvas.height)

在整个小游戏代码中首次调用 wx.createCanvas() 创建的是上屏
Canvas，之后调用则创建的是离屏 Canvas。如果你的项目中使用了官方提供的
Adapter 即 weapp-adapter.js（关于什么是 Adpater 请参考官方教程
Adapter），那么你此时创建的会是一个离屏 Canvas。因为在 weapp-adapter.js
已经调用了一次 wx.createCanvas()，并把返回的 canvas
作为全局变量暴露出来。假设你的项目目录结构如下：

├── game.js ├── weapp-adapter.js └── game.json

在 weapp-adapter.js 中已经调用了一次 wx.createCanvas()，并把返回的
canvas 作为全局变量暴露出来。

// weapp-adapter canvas = wx.createCanvas()

如果你在 require weapp-adapter.js 之后再调用
wx.createCanvas()，那么创建的 Canvas 会是一个离屏的
Canvas，因为此时已经不是对该 API 的首次调用。

require(‘./weapp-adapter’) var myCanvas = wx.createCanvas()

在 Canvas 上进行绘制
--------------------

但是由于没有在 canvas 上进行绘制，所以 canvas 是透明的。使用 2d
渲染上下文的进行简单的绘制，可以在屏幕左上角看到一个 100x100
的红色矩形。

var context = canvas.getContext(‘2d’) context.fillStyle = ‘red’
context.fillRect(0, 0, 100, 100)

通过 Canvas.getContext() 方法可以获取 2d 或 WebGL 渲染上下文
RenderingContext，调用渲染上下文的绘制方法可以在 Canvas
上进行绘制。小游戏基本上支持 2d 和 WebGL 1.0 所有的属性和方法，详情请见
RenderingContext。由于使用 WebGL
的绘制过程较为复杂，所以本文中的示例代码都以 2d
渲染上下文的绘制方法编写。

通过设置 width 和 height 属性可以改变 Canvas 对象的宽高，但这也会导致
Canvas 内容的清空和渲染上下文的重置。

canvas.width = 300 canvas.height = 300

显示图片
--------

通过 wx.createImage() 接口，可以创建一个 Image 对象。Image
对象可以加载图片。当 Image 对象被绘制到 Canvas
上时，图片才会显示在屏幕上。

var image = wx.createImage()

设置 Image 对象的 src
属性可以加载一张本地图片或网络图片，当图片加载完毕时会执行注册的 onload
回调函数，此时可以将 Image 对象绘制到 Canvas 上。

image.onload = function () { console.log(image.width, image.height)
context.drawImage(image, 0, 0) } image.src = ‘logo.png’

创建多个 Canvas
---------------

在整个小游戏运行期间，首次调用 wx.createCanvas 接口创建的是一个上屏
Canvas。在这个 canvas
上绘制的内容都将显示在屏幕上。而第二次、第三次等后几次调用
wx.createCanvas 创建的都会是离屏 Canvas。在离屏 Canvas
上绘制的内容仅仅只是绘制到了这个离屏 Canvas 上，并不会显示在屏幕上。

以如下代码为例，运行后会发现屏幕上并没有在 (0, 0) 的位置显示 100x100
的红色矩形。因为我们是在一个离屏的 Canvas 绘制的。

var screenCanvas = wx.createCanvas() var offScreenCanvas =
wx.createCanvas() var offContext = offScreenCanvas.getContext(‘2d’)
offContext.fillStyle = ‘red’ offContext.fillRect(0, 0, 100, 100)

为了让这个红色矩形显示在屏幕上，我们需要把离屏的 offScreenCanvas
绘制到上屏的 screenCanvas 上。

var screenContext = screenCanvas.getContext(‘2d’)
screenContext.drawImage(offScreenCanvas, 0, 0)

动画
----

在 JavaScript 中，一般通过 setInterval/setTimeout/requestAnimationFrame
来实现动画效果。小游戏对这些 API 提供了支持：

::

   setInterval()
   setTimeout()
   requestAnimationFrame()
   clearInterval()
   clearTimeout()
   cancelAnimationFrame()

另外，还可以通过 wx.setPreferredFramesPerSecond() 修改执行
requestAnimationFrame 回调函数的频率，以降低性能消耗。

触摸事件
--------

响应用户与屏幕的交互是游戏中必不可少的部分，小游戏参照 DOM 中的
TouchEvent 提供了以下监听触摸事件的 API：

::

   wx.onTouchStart()
   wx.onTouchMove()
   wx.onTouchEnd()
   wx.onTouchCancel()

wx.onTouchStart(function (e) { console.log(e.touches) })

wx.onTouchMove(function (e) { console.log(e.touches) })

wx.onTouchEnd(function (e) { console.log(e.touches) })

wx.onTouchCancel(function (e) { console.log(e.touches) })

全局对象
--------

window 对象是浏览器环境下的全局对象。小游戏的运行环境中没有 BOM
API，因此没有 window 对象。但是提供了全局对象
GameGlobal，所有全局定义的变量都是 GameGlobal 的属性。

console.log(GameGlobal.setTimeout === setTimeout)
console.log(GameGlobal.requestAnimationFrame === requestAnimationFrame)
// true

开发者可以根据需要把自己封装的类和函数挂载到 GameGlobal 上。

GameGlobal.render = function () { //省略方法的具体实现… }

render()

GameGlobal 是一个全局对象，本身也是一个存在循环引用的对象。

console.log(GameGlobal === GameGlobal.GameGlobal)

console.log 无法在真机上将存在循环引用的对象输出到 vConsole
中。因此真机调试时请注释 console.log(GameGlobal)
这样的代码，否则将会产生这样的错误

An object width circular reference can’t be logged
