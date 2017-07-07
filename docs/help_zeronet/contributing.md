# 为ZeroNet贡献一份力

感谢使用ZeroNet. ZeroNet 是一个超过67名去中心化爱好者协作劳动的成果，就像你一样。 我们感谢所有向我们汇报bug、编纂和更新文档、以及提出并实现新点子和新协议的用户。假如你想要为Zeronet做出贡献，请看看下面的这些指南。

### 你不需要拥有编程技能。

实际上，这个项目的大部分贡献者并不编写代码，即使我们很喜欢为ZeroNet提供代码的网友，我们也十分欢迎别的方面的贡献者。

### 喜欢写文章?

- 写写关于ZeroNet的文章，让更多人知道它。
- 写一些教程，让别人容易上手这个软件。
- 帮助翻译ZeroNet。
- 改进和更新这个参考文档。这个文档由遍布全世界的许多社区成员编写。

### 喜欢帮助别人?

- 订阅我们的 [issue tracker on GitHub](https://github.com/HelloZeroNet/ZeroNet/issues) and help people solve problems.
- 在 [Gitter](https://gitter.im/HelloZeroNet/ZeroNet) 上加入我们，或者加入IRC频道[#zeronet @ freenode](https://kiwiirc.com/client/irc.freenode.net/zeronet) ，并解决他人的疑惑
- 持续运行ZeroNet的一个节点，让整个网络更快。

### 想要搭站点?

- 设立新的ZeroNet站点。比如创建你自己的博客。 [这真的非常简单，而且几乎是免费的](../using_zeronet/create_new_site.md)
- “内容为王！”  我们的整个网络假如没有了内容，就变得没有价值。所以我们邀请你共同带来ZeroNet的繁荣和丰富。

### 想要做研究?

- 帮助我们研究[复杂的主意](https://github.com/HelloZeroNet/ZeroNet/labels/help%20wanted).
- 加入我们关于设计新的特性和协议的讨论，例如[I2P支持](https://github.com/HelloZeroNet/ZeroNet/issues/45) 和 [DHT支持](https://github.com/HelloZeroNet/ZeroNet/issues/57).
- 你是否拥有一个 [树莓派微型电脑](https://github.com/HelloZeroNet/ZeroNet#linux-terminal), 或者一个 [C.H.I.P.掌上电脑](http://127.0.0.1:43110/Blog.ZeroNetwork.bit/?Post:94:Running+ZeroNet+on+a+$9%C2%A0computer) ，或者一个[开源路由器](https://github.com/HelloZeroNet/ZeroNet/issues/783)？试试在这些设备上运行ZeroNet，并且向我们反馈你是如何让ZeroNet在上面跑起来的。

### 想要写代码?

- 假如你会Python语言，你可以从我们Github上的[issue追踪列表](https://github.com/HelloZeroNet/ZeroNet/issues)上找一个任务，试着挑战实现它。
- 假如你自己有一些好点子想要实现，我们也十分欢迎。在你开始动工之前，请[创建一个新的讨论](https://github.com/HelloZeroNet/ZeroNet/issues/new) 让社区知道你的好点子，这样大家可以一起头脑风暴，互相分享更深入的主意，把你的点子实现得更棒。
- 请稍微注意一下代码风格，我们下面列出了一些基本要求。

 - [PEP8](https://www.python.org/dev/peps/pep-0008/)
 - 简洁的代码
 - 过早优化是万恶之源

### 想要提供资金支持?

- 可以通过 [捐赠比特币](donate.md) 来赞助 ZeroNet.


# 假如你要参与ZeroNet的开发工作，请使用以下编程风格
 - [PEP8](https://www.python.org/dev/peps/pep-0008/)
 - 简洁的代码
 - 过早优化是万恶之源

### 命名
 - 类命名: 首字母大写的驼峰命名法
 - 函数命名: 首字母小写的驼峰命名法
 - 变量命名: 全部小写，单词间使用下划线

### 变量
 - file_path: 通过程序运行路径来引用的文件 (data/17ib6teRqdVgjB698T4cD1zDXKgPqpkrMg/css/all.css)
 - inner_path: 通过站点相对路径来引用的文件 (css/all.css)
 - file_name: all.css
 - file: Python文件对象
 - privatekey: 站点的私钥 (without _)

### 源文件、目录和命名
 - 每个文件最好只有一个类
 - 源文件名和目录名最好用以类的名称来命名，例如：WorkerManager class = Worker/WorkerManager.py
