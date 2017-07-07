# FAQ 常见问答


#### 我需要一个开放端口吗？

它 __并不是必需的__， 你可以在无需开放端口的情况下使用ZeroNet。
但假如想要创建一个新网站，我们强烈推荐完成端口开放的设置。

在ZeroNet启动时，它会试图通过[UPnP](https://wikipedia.org/wiki/Universal_Plug_and_Play)在路由器上启用一个开放端口。假如此操作不成功，你需要手动设置：

- 试着打开你的路由器管理界面 [http://192.168.1.1](http://192.168.1.1)
或者 [http://192.168.0.1](http://192.168.0.1)
- 找到类似 "UPnP 设置" 的地方，启用它，保存设置后重启路由器。

假如此方法无效，你可能需要尝试一下“端口转发”选项。这个选项在每个路由器上的设置不尽相同。 [这是YouTube上的一个教程。](https://www.youtube.com/watch?v=aQXJ7sLSz14) 你需要进行转发的端口号是15441。


---


#### ZeroNet是匿名的吗？

它不会比BT下载匿名多少，但隐私程度 (找出是谁发送了某条评论/创建了某个站点) 会随着整个ZeroNet网络规模和用户节点数的增加而变得更高。

ZeroNet在设计上也支持额外的匿名网络: 你很容易通过洋葱路由（Tor）来在使用ZeroNet时隐藏你的IP。


---


#### 如何在洋葱浏览器（Tor borwser）中使用ZeroNet？

假如你想要用洋葱路由，我们推荐您在洋葱浏览器中浏览ZeroNet：

- 打开洋葱浏览器（Tor borwser）
- 打开网址 `about:preferences#advanced`
- 点击 `设置...`
- 输入 `127.0.0.1` 并把这个地址加入到 **不通过代理访问**
- 在浏览器打开 http://127.0.0.1:43110  来浏览ZeroNet

假如你看到了空白页面：
 - 点击 NoScript按钮 (在工具栏上找找看) 
 - 选择"暂时允许本页面上的所有Javascript"
 - 刷新页面

---


#### 如何配合Tor来使用ZeroNet？

假如你想要隐藏你的IP，请使用最新版本的ZeroNet，然后在ZeroHello的每个连接都启用Tor。

在Windows上Tor已经和ZeroNet集成。 对于其他操作系统 [请看这里的指南](https://www.torproject.org/docs/installguide.html),
编辑你的torrc配置文件，把 `# ControlPort 9051` 前面的井号去掉，然后重启Tor服务和ZeroNet。

> __提示：__ 你可以在ZeroNet的 [连接状态页](http://127.0.0.1:43110/Stats) 页确认你连接到的用户IP地址和ZeroNet网站。

> __提示：__ 假如出现“连接错误”提示，请把ZeroNet升级到最新版本，并且确保Tor的版本也足够新(至少ver 0.2.7.5以上)


---


#### 在Linux下如何配合Tor使用ZeroNet？

把Tor 升级到最新版本(至少ver0.2.7.5以上), 按照 [这个教程](https://www.torproject.org/docs/debian.html.en) 。以Debian系统为例:

 - `echo 'deb http://deb.torproject.org/torproject.org jessie main'>> /etc/apt/sources.list.d/tor.list`
 - `gpg --keyserver keys.gnupg.net --recv 886DDD89`
 - `gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | apt-key add -`
 - `apt-get update`
 - `apt-get install tor`

编辑配置以启用控制协议:

 - `mcedit /etc/tor/torrc`
 - 把`ControlPort 9051` 和 `CookieAuthentication 1` (大约在 57行) 前面的`#` 号去掉
 - `/etc/init.d/tor restart`
 - 执行 `usermod -a -G debian-tor [yourlinuxuser]`命令，以给你自己添加上读取授权cookie的权限<br>(假如不是Debian发行版，你可能需要查看文件所属的用户组 `ls -al /var/run/tor/control.authcookie`)
 - 退出并重新登录你的系统账号以应用权限变更操作。

> __提示：__ 你可以通过输入命令 `echo 'PROTOCOLINFO' | nc 127.0.0.1 9051` 来查看Tor是否已经正确运行

> __提示：__ 你也可以不用修改torrc文件来使用Tor来浏览ZeroNet，例如比较旧版的Tor, 通过加上参数来运行 `zeronet.py --tor disable --proxy 127.0.0.1:9050 --disable_udp`, 不过这样就无法和.onion节点进行通信。


---


#### 假如我的网络运营商屏蔽了Tor，我要怎么使用ZeroNet？

ZeroNet目前还不支持 [Tor 混淆流量(pluggable transport)](https://www.torproject.org/docs/pluggable-transports.html.en)模块 。在被审查的网络下安全低使用ZeroNet最简单方法是启动Tor浏览器，进行正确的配置以让他工作，并且打开混淆流量（pluggable transport），然后配置ZeroNet以通过Tor代理收发数据包，例如给ZeroNet加上启动参数 `--tor_controller 127.0.0.1:9151 --tor_proxy 127.0.0.1:9150` 或者把启动参数写在 zeronet.conf 里：

```
[global]
tor_controller = 127.0.0.1:9151
tor_proxy = 127.0.0.1:9150
```


---


#### 我能够在多台设备上使用同一个用户名吗？

是的，不过你要把用户信息文件 `data/users.json` 拷贝到新的机器上。


---


#### 如何创建一个那种“很怪异”的网站地址？

使用 [vanitygen](https://bitcointalk.org/index.php?topic=25804.0) 来产生一个key。获得key之后，新建一个 `data/1YourPublicKey...tCkBzAXTKvJk4uj8` 目录.。然后往目录里面放上文件。

然后你就可以浏览 [http://127.0.0.1:43110/1YourPublicKey...tCkBzAXTKvJk4uj8/](http://127.0.0.1:43110/1YourPublicKey...tCkBzAXTKvJk4uj8/)了。用鼠标向左拖拽 `0` 按钮，并且对你这个网站地址的所有权进行签名确认。

---


#### 如何创建一个.bit域名？

你可以通过 [Namecoin](https://namecoin.info/)来创建和注册域名。
你可以在浏览器的图形界面中管理这个域名，也可以用 [在线命令行界面](http://www.christopherpoole.net/registering-a-bit-domain-with-namecoin.html).

在域名注册完毕后，你必须修改你的域名记录文件：在里面插入一个标识块，就像这样：

```
{
...
    "zeronet": {
        "": "1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr",
        "blog": "1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8",
        "talk": "1TaLk3zM7ZRskJvrh3ZNCDVGXvkJusPKQ"
    },
...
}
```
"" 表示顶级域名，而其他的是子域名。

> __提示：__ 你可以用通过 [shapeshift.io](https://shapeshift.io/)用比特币（Bitcoin）或者其他的数字加密货币来购买Namecoin。 

> __提示：__ 其他注册.bit域名的渠道: [domaincoin.net](https://domaincoin.net/), [peername.com](https://peername.com/), [dotbit.me](https://dotbit.me/)

> __提示：__ 你可以通过[namecha.in](http://namecha.in/)证明对域名的所有权 ,例如： [zeroid.bit](http://namecha.in/name/d/zeroid)。

> __提示：__ 只能域名只能包括 [小写字母，数字和 -短横线符号](http://wiki.namecoin.info/?title=Domain_Name_Specification_2.0#Valid_Domains)。

> __提示：__ 想要让ZeroHello 把你的域名显示成你注册的那种形式而不是一大串长长的Bitcoin地址的话，请把域名key添加到 content.json文件中。 ([例子](https://github.com/HelloZeroNet/ZeroBlog/blob/master/content.json#L6))


---


#### 我能通过产生的地址/私钥来接收比特币吗？

是的，这是一个标准的比特币地址。注册时产生给你的私钥是WIF格式的，你可以在大部分比特币客户端中导入私钥。

> __提示：__ 不建议在你站点的这个比特币地址中存入太高的金额，因为你每次修改站点的时候都需要输入你的私钥，这有时候不安全。


---


#### 假如某个人在网站发布了有害内容，它会影响到访问者吗？

ZeroNet系统是有沙盒权限设计的，就像普通因特网网站一样。你可以控制自己浏览的东西。假如你发现了可疑内容，你可以立即停止访问该网站。


---


#### 是否有可能在远程机器安装ZeroNet服务并在本机运行？
是的，你可以重命名 __plugins/disabled-UiPassword__ 目录为 __plugins/UiPassword__ 来启用he UiPassword 插件，然后在远程计算机上用下列参数启动Zeronet： <br>`zeronet.py --ui_ip "*" --ui_password anypassword`
这会把ZeroNet的浏览界面暴露给所有访问者，所以你要确保通过密码设置，只有你才能访问这个页面。

> __提示：__ 你可以通过参数`--ui_restrict ip1 ip2`来限制连接者的IP，以便于只有你能访问远程ZeroNet服务。

> __提示：__ 可以在 `zeronet.conf` 配置文件（假如没有，创建一个）中修改 `[global]`, `ui_password = anypassword` 来修改你的访问密码。


---


#### 有什么办法来查看ZeroNet使用的网络带宽？

ZeroNet总的收发数据量情况可以在ZeroNet的侧边栏看到。<br>(你可以用鼠标向左拖拽右上角的那个 `0` 按钮来打开侧边栏)

> __提示：__ 可以在这里看到每个链接的手法数据量: [http://127.0.0.1:43110/Stats](http://127.0.0.1:43110/Stats)


---


#### 假如有两个人用同一个密钥来修改同一个网站内容会怎样?

content.json文件是有时间戳的。客户端会接受那个比较新的版本。


---


#### ZeroNet使用了比特币的区块链技术吗?

不。ZeroNet 只用上了比特币在地址方面的加密技术和内容的签名/验证技术。 
用户身份鉴别基于比特币 [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) 格式。

Namecoin的区块链技术是用于域名注册的。


---


#### ZeroNet 是不是只支持只含有HTML, CSS的静态网站？

ZeroNet是设计成支持动态、实时更新的网站的。你也可以通过它来存放各种文件类型。
(VCS 库，你自己的瘦客户端，数据库等等)


---


#### 我要怎么创建一个新的ZeroNet站点？

[看这里。](/using_zeronet/create_new_site/)

---


#### 它是怎么工作的？

- 当你想要访问一个新网站的时候，它通过BitTorrent网络来获得别的访问者的IP地址。
- 然后首先下载一个叫做 __content.json__的文件，这个文件记录了所有别的文件名，哈希值和站点拥有者的加密签名。
- 通过站点的__地址__和__content.json__文件里记载的站点拥有者的__签名__来__验证__你下载的这个文件的确是真的。
- __下载其他文件__ (html, css, js...) 并且通过__content.json__文件里记载SHA512哈希值来验证每个文件的真伪。
- 你浏览过的网站内容会存储在你的电脑上，并且以后会__提供给别的浏览者__.
- 假如站点拥有者（那个拥有该站点地址的私钥的人） __修改了__ 网站，他会签署一份新的content.json文件，然后 __分发给目前的用户节点__。每个用户收到并且通过签名来验证了新的content.json文件真实性后，他们会 __下载和自动更新新文件__ 并把新的网站内容继续分发给以后的浏览者。

想知道更多：
 [一个ZeroNet示例站点的分析](/using_zeronet/sample_sites/),
 [关于ZeroNet工作机制的幻灯片](https://docs.google.com/presentation/d/1_2qK1IuOKJ51pgBvllZ9Yu7Au2l551t3XBgyTSvilew/pub)
