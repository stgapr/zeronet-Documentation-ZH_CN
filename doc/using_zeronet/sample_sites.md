# ZeroNet演示站点

## ZeroHello

ZeroNet的入口站。

 - 列出了你所有关注的站点的名称，用户数，最新修改日期。
 - 进行站点操作: 升级，暂停，继续，删除。
 - 可以对别人的站点系统进行克隆，从而快速生成一个你的博客/论坛
 - 假如有新的ZeroNet版本，你只需要轻轻点击就能升级。

![ZeroHello](/img/zerohello.png)

地址: [1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D](http://127.0.0.1:43110/1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D)

[源代码](https://github.com/HelloZeroNet/ZeroHello)

---

## ZeroBoard

一个简单的信息公告板的demo，展示了动态内容分发。

 - 通过用户的auth_key随机生成动态的头像
 - 实时的信息更新

它是怎么工作的？

 - 用户发送出了你的消息，通知到了拥有该站点的私钥的守护程序
 - 该守护程序修改 `message.json` 文件，使用手中的私钥签署它，然后立即公布分发给其他的用户。
 - 站点的变动到达了用户的电脑上，该条信息就会显示在你的屏幕上。

![ZeroBoard](/img/zeroboard.png)

地址: [1Gfey7wVXXg1rxk751TBTxLJwhddDNfcdp](http://127.0.0.1:43110/1Gfey7wVXXg1rxk751TBTxLJwhddDNfcdp)

[源代码](https://github.com/HelloZeroNet/ZeroBoard)


---

## ZeroBlog

简单的博客系统

 - 内嵌内容编辑器
 - Markdown格式支持
 - 代码高亮
 - 只需要通过站点界面就能完成签名和分发步骤。

它是怎样工作的？

 - 你通过网站界面来编辑 `data.json`
 - 你按下 `签署并分发新内容` 按钮时，网站会请求你的私钥 (私钥就是你在 [用zeronet.py siteCreate 命令来创建新网站的时候会跳出来的那串东西](create_new_site/))
 - 你的 ZeroNet 客户端签名了你的新文章，然后把这些改动发布给别的用户。
 - 你的网站永远在线，只要这里有一个用户 (你的网站的曾经的访问者)的电脑开着。

![ZeroBlog](/img/zeroblog.png)

地址: [1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8](http://127.0.0.1:43110/1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8) or [blog.zeronetwork.bit](http://127.0.0.1:43110/blog.zeronetwork.bit)

[源代码](https://github.com/HelloZeroNet/ZeroBlog)


---

## ZeroTalk

去中心化的P2P论坛

 - 主题内容创建，修改，删除
 - 主题投票系统
 - 只需要联系站点拥有者一次，假如你需要网站的修改权限。
 - 评论等内容修改会直接推送给别的用户。
 - 只有你能对你发送的内容进行控制，别人无法篡改删除。
 - 新评论会实时展现

它是怎样工作的？

 - 为了在这个站点上发布消息，你必须认证自己的注册身份，使用网站独有的身份验证系统或者ZeroID。
 - 你认证了自己的身份后，就可以向别的用户推送你发出的内容了(例如信息，主题帖子，投票) 。

![ZeroTalk](/img/zerotalk.png)

地址: [1TaLkFrMwvbNsooF4ioKAY9EuxTBTjipT](http://127.0.0.1:43110/1TaLkFrMwvbNsooF4ioKAY9EuxTBTjipT) or [talk.zeronetwork.bit](http://127.0.0.1:43110/talk.zeronetwork.bit)

[源代码](https://github.com/HelloZeroNet/ZeroTalk)

---

## ZeroMail

端对端的加密分发、P2P信息传输系统。为了提高隐私性，它采用了一个类似比特信（BitMessage）的技术方案，不会在传输过程中暴露邮件的接收者。

 - 使用 ECIES 来进行加密信息交换, 信息通过AES256 加密算法加密
 - 当你第一次访问这个网站，他会把你的公钥加入数据库中，以便于别人可以发送信息给你
 - 每个人都可以接触到所有邮件（但除非是发给自己的邮件，否则都是加密无法解开的）。这解决了通过追踪邮件流动来定位到收件人的安全隐患。
 - 为了降低邮件的传输性能开销的同时提高解密速度，我们选择使用了AES加密和每次生成一个新IV参数的策略


![ZeroTalk](/img/zeromail.png)

地址: [1MaiL5gfBM1cyb4a8e3iiL8L5gXmoAJu27](http://127.0.0.1:43110/1MaiL5gfBM1cyb4a8e3iiL8L5gXmoAJu27) or [mail.zeronetwork.bit](http://127.0.0.1:43110/mail.zeronetwork.bit)

[源代码](https://github.com/HelloZeroNet/ZeroMail)

---

## ZeroChat

一个已经成型的演示网站，教授了如何通过ZeroNet来使用100行以内的Python代码架起一个无需服务器的、SQL后端的、实时传输信息的P2P聊天应用。

 - 可用ZeroID完成身份验证
 - 用SQL数据库储存信息
 - 实时发送信息，实时接收到别人的消息
 - 在信息到达时，自动显示在屏幕上。

![ZeroChat](/img/zerochat.png)

地址: [1AvF5TpcaamRNtqvN1cnDEWzNmUtD47Npg](http://127.0.0.1:43110/1AvF5TpcaamRNtqvN1cnDEWzNmUtD47Npg)

 ZeroBlog的使用教程:
 [第一部分](http://127.0.0.1:43110/Blog.ZeroNetwork.bit/?Post:43:ZeroNet+site+development+tutorial+1),
 [第二部分](http://127.0.0.1:43110/Blog.ZeroNetwork.bit/?Post:46:ZeroNet+site+development+tutorial+2)

---

## ZeroMe

去中心化的、类似推特的P2P社交网络

 - 在ZeroMe用户注册系统存储用户信息
 - 在叫做Hub的MergeSite中存储帖子和评论
 - 可以选择上传图像
 - 实时的信息流
 
![ZeroMe](/img/zerome.png)

地址: [1MeFqFfFFGQfa1J3gJyYYUvb5Lksczq7nH](http://127.0.0.1:43110/1MeFqFfFFGQfa1J3gJyYYUvb5Lksczq7nH)

[源代码](https://github.com/HelloZeroNet/ZeroMe)

---

## ReactionGIFs

文件可选下载功能的演示网站。上面的视频只有在你点击的时候才会下载到你的电脑上。

![ReactionGIFs](/img/reactiongifs.jpg)

地址: [1Gif7PqWTzVWDQ42Mo7np3zXmGAo3DXc7h](http://127.0.0.1:43110/1Gif7PqWTzVWDQ42Mo7np3zXmGAo3DXc7h)

[源代码](https://github.com/HelloZeroNet/ReactionGIFs)
