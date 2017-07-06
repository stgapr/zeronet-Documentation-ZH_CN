# 概览

ZeroNet 可以让你创造静态或者是动态的站点。

即使ZeroNet无法运行PHP或者Ruby这样的脚本语言作为后端，你也可以用ZeroNet的API（叫做ZeroFrame）来创造动态网站。另外，JavaScript (以及CoffeeScript)和SQL数据库是ZeroNet内建支持的！

## ZeroChat 示例程序

在这个示例程序中，我们演示了如何架其一个P2P的、去中心化的、无后端无服务器的聊天网站——通过不到100行Python代码。

* [在 ZeroBlog上阅读](http://127.0.0.1:43110/Blog.ZeroNetwork.bit/?Post:99:ZeroChat+tutorial)
* [在 Medium.com上阅读](https://decentralize.today/decentralized-p2p-chat-in-100-lines-of-code-d6e496034cd4)

## ZeroNet 调试模式

ZeroNet带 `--debug` 参数，会让她以调试模式启动。这样有助于站点开发。

让ZeroNet使用调试模式启动的命令行: `python zeronet.py --debug`

### 调试模式的特点：

- 自动的 CoffeeScript -> JavaScript 转换 (这个文档中的所有粒子和示例网站都是用 [CoffeeScript](http://coffeescript.org/)写的)
- 调试信息会显示在命令行界面中
- 在文件被修改的时候，不需要重新ZeroNet就能在UiRequest, UiWebsocket, FileRequest时自动重新加载资源文件   (需要GNU/Linux的 [PyFilesystem](http://pyfilesystem.org/) )
- `http://127.0.0.1:43110/Debug` 调试信息和交互式Python命令行会定位到最后的出错代码位置 (我们使用了强大的 Werkzeug 调试器 - 需要 [Werkzeug](http://werkzeug.pocoo.org/))
- `http://127.0.0.1:43110/Console` 可以开启一个交互的Python命令行 (需要 [Werkzeug](http://werkzeug.pocoo.org/))

### 额外的特性 (只在你拥有的站点上起作用)

 - 合并 CSS 文件: 站点路径里的所有CSS文件都会合并成一个`all.css`. 平时只需要包含这个文件在网站文件清单中。 假如你想要同时保留其他的CSS文件以方便于网站开发调试，你也可以在  `content.json`的 ignore key中加入这些css文件。这样它们不会在发布站点时随之发布。 (例如: 在 `content.json` 添加以下内容：`"ignore": "(js|css)/(?!all.(js|css))"` 这样就会忽视除了all.js和all.css所有CSS和JS文件)
 - 合并 JS 文件:: 站点路径里的所有JS文件都会合并成一个`all.jss`. 假如你使用了 CoffeeScript 编译器 (已经在Windows版本的发行包里包含) ，它会自动转换 `.coffee` 为 `.js`。
 - 控制哪些文件要合并为 all.css/all.js: 在css/js目录的子目录里的文件最先合并，然后是那些css/js 目录里的文件，按照顺序来合并(例如：01_a.css, 02_a.css等)

