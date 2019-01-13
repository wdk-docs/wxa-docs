文件系统

文件系统有两类文件：代码包文件和本地文件。
文件系统管理接口

通过 wx.getFileSystemManager() 可以获取到全局唯一的文件系统管理器，所有文件系统的管理操作通过 FileSystemManager 来调用。

var fs = wx.getFileSystemManager()

代码包文件

代码包文件指的是在项目目录中添加的文件。由于代码包文件大小限制，代码包文件适用于放置首次加载时需要的文件，对于内容较大或需要动态替换的文件，不推荐用添加到代码包中，推荐在小游戏启动之后再用下载接口下载到本地。
访问代码包文件

代码包文件的访问方式是从项目根目录开始写文件路径。 image.png
修改代码包文件

代码包内的文件无法在运行后动态修改或删除，修改代码包文件需要重新发布版本。
本地文件

本地文件指的是小程序被用户添加到手机后，会有一块独立的文件存储区域，以用户维度隔离。即同一台手机，每个微信用户不能访问到其他登录用户的文件，同一个用户不同 appId 之间的文件也不能互相访问。 本地文件沙盒.png

本地文件的文件路径均为以下格式：

{{协议名}}://文件路径

    其中，协议名在 iOS/Android 客户端为 "wxfile"，在开发者工具上为 "http"，开发者无需关注这个差异，也不应在代码中去硬编码完整文件路径。

本地临时文件

本地临时文件只能通过调用特定接口产生，不能直接写入内容。本地临时文件产生后，仅在当前生命周期内有效，重启之后即不可用。因此，不可把本地临时文件路径存储起来下次使用。如果需要下次在使用，可通过 FileSystemManager.saveFile() 或 FileSystemManager.copyFile() 接口把本地临时文件转换成本地存储文件或本地用户文件。
示例

wx.chooseImage({
  success: function (res) {
    var tempFilePaths = res.tempFilePaths // tempFilePaths 的每一项是一个本地临时文件路径
  }
})

本地缓存文件

本地存储文件只能通过调用特定接口产生，不能直接写入内容。本地缓存文件产生后，重启之后仍可用。本地缓存文件只能通过 FileSystemManager.saveFile() 接口将本地临时文件保存获得。
示例

fs.saveFile({
  tempFilePath: '', // 传入一个本地临时文件路径
  success(res) {
    console.log(res.savedFilePath) // res.savedFilePath 为一个本地缓存文件路径
  }
})

    本地缓存文件是最初的设计，1.7.0 版本开始，提供了功能更完整的本地用户文件，可以完全覆盖本地缓存文件的功能，如果不需要兼容低于 1.7.0 版本，可以不使用本地缓存文件。

本地用户文件

本地用户文件是从 1.7.0 版本开始新增的概念。我们提供了一个用户文件目录给开发者，开发者对这个目录有完全自由的读写权限。通过 wx.env.USER_DATA_PATH 可以获取到这个目录的路径。
示例

// 在本地用户文件目录下创建一个文件 a.txt，写入内容 "hello, world"
const fs = wx.getFileSystemManager()
fs.writeFileSync(`${wx.env.USER_DATA_PATH}/hello.txt`, 'hello, world', 'utf8')

读写权限
接口、组件 	读 	写
代码包文件 	有 	无
本地临时文件 	有 	无
本地缓存文件 	有 	无
本地用户文件 	有 	有