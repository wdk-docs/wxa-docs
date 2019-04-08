模块化

小游戏提供了 CommonJS 风格的模块 API，可以通过 module.exports 和 exports
导出模块，通过 require 引入模块。假设代码包有如下结构，src/util
目录下的是一些在游戏中会反复用到的方法，res/image
目录下则是游戏中会用到一些图片。

├── game.js ├── game.json ├── src \| └── util \| ├── drawLogo.js \| └──
… └── res ├── image \| ├── logo.png \| └── … └── music └── bgm.mp3

其中，drawLogo.js 模块封装的是一个用来把 logo 画到指定位置的方法。

module.exports = function (canvas, x, y) { var image = new Image()
image.onload = function () { var context = canvas.getContext(‘2d’)
context.drawImage(image, x, y) } image.src = ‘res/image/logo.png’ }

注意，当用加载本地的图片、音频、视频资源时，必须写从代码包根目录开始的绝对路径。如果写以
drawLogo.js 所在目录的相对路径，则会导致系统找不到资源文件，加载失败。

image.src = ‘../../res/image/logo.png’

在 game.js 中 require drawLogo，就可以调用 drawLogo 模块导出的方法。

var drawLogo = require(‘./src/util/drawLogo’) var canvas =
wx.createCanvas() drawLogo(canvas, 40, 40)
