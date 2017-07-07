# ZeroFrame CLI 参考文档



# Wrapper

_These commands handled by wrapper frame and does not sent to UiServer using websocket_


#### wrapperConfirm _message, [button_caption]_
按下确认键的时跳出提示消息

参数                   | 描述
                  ---  | ---
**message**            | 想要提示的消息
**button_caption** (可选) | 确认键的显示文本 (默认为 OK)

**返回值**: 假如点击了按钮，返回True

**举例：**
```coffeescript
# 删除站点
siteDelete: (address) ->
	site = @sites[address]
	title = site.content.title
	if title.length > 40
		title = title.substring(0, 15)+"..."+title.substring(title.length-10)
	@cmd "wrapperConfirm", ["Are you sure you sure? <b>#{title}</b>", "Delete"], (confirmed) =>
		@log "Deleting #{site.address}...", confirmed
		if confirmed
			$(".site-#{site.address}").addClass("deleted")
			@cmd "siteDelete", {"address": address}
```


---


#### wrapperInnerLoaded

适用于页面URL的 windows.location.hash 。 当页面已经加载完成，要跳转到特定定位点时，该事件被触发。


---


#### wrapperGetLocalStorage
**返回**: 浏览器对本页面的本地存储内容

**举例：**
```coffeescript
@cmd "wrapperGetLocalStorage", [], (res) =>
	res ?= {}
	@log "本地存储值:", res
```



---

#### wrapperGetState
**返回值**: 浏览器的当前历史的状态对象

---

#### wrapperNotification _type, message, [timeout]_
显示一条消息

参数                   | 描述
                  ---  | ---
**type**               | 可能的值: info, error, done 
**message**            | 你想要弹出的消息
**timeout** （可选）   | 在多少秒后自动隐藏消息（可选的）（以毫秒计） 

**返回值：**: None

**举例：**
```coffeescript
@cmd "wrapperNotification", ["done", "Your registration has been sent!", 10000]
```


---

#### wrapperOpenWindow _url, [target], [specs]

跳转，或者打开新弹出窗口


参数               | 描述
              ---  | ---
**url**            | 已打开页面的URL
**target** (可选)  | 目标窗口名称
**specs** (可选)   | 打开的窗口的特定属性 (参见[window.open specs](http://www.w3schools.com/jsref/met_win_open.asp))

**举例：**
```coffeescript
@cmd "wrapperOpenWindow", ["https://zeronet.io", "_blank", "width=550,height=600,location=no,menubar=no"]
```

---


#### wrapperPermissionAdd _permission_

为网站请求新的授权


参数             | 描述
             --- | ---
**permission**   | 授权名 (eg. Merger:ZeroMe)


---

#### wrapperPrompt _message, [type]_

用户输入时的提示文本

参数                | 描述
               ---  | ---
**message**         | 想显示的消息
**type** （可选）   | 输入类型 (默认： text)

**返回值：**: 输入的文本

**举例：**
```coffeescript
# 提示输入密钥
@cmd "wrapperPrompt", ["输入你的密钥：", "password"], (privatekey) =>
    $(".publishbar .button").addClass("loading")
    # 签署 content.json，然后并且把请求发送给服务器
    @cmd "sitePublish", [privatekey], (res) =>
        $(".publishbar .button").removeClass("loading")
        @log "发送结果：", res
```


---

#### wrapperPushState _state, title, url_
改变URL，并且在浏览器历史记录中添加新的一行。 参加: [pushState JS 方法](https://developer.mozilla.org/en-US/docs/Web/API/History_API#The_pushState()_method)

参数           | 描述
               ---  | ---
**state**           | 状态的js对象
**title**           | 页面标题
**url**             | 页面URL

**返回值**: None

```coffeescript
@cmd "wrapperPushState", [{"scrollY": 100}, "Profile page", "Profile"]
```


---

#### wrapperReplaceState _state, title, url_
改变URL，然而不在浏览器历史记录中添加新的一行。 参见: [replaceState JS 方法](https://developer.mozilla.org/en-US/docs/Web/API/History_API#The_replaceState()_method)

参数                | 描述
               ---  | ---
**state**           | 状态的js对象
**title**           | 页面标题
**url**             | 页面URL

**返回值**: None

```coffeescript
@cmd "wrapperReplaceState", [{"scrollY": 100}, "Profile page", "Profile"]
```


---

#### wrapperSetLocalStorage _data_
为该网站设置浏览器的本地存储数据


参数                  | 描述
                  ---  | ---
**data**               | 你想要保存的任何数据结构

**返回值**: None

**举例：**
```coffeescript
Page.local_storage["topic.#{@topic_id}_#{@topic_user_id}.visited"] = Time.timestamp()
Page.cmd "wrapperSetLocalStorage", Page.local_storage
```


---

#### wrapperSetTitle _title_
设置浏览器标题

参数                   | 描述
                  ---  | ---
**title**              | 新的浏览器窗口/标签页标题

**返回值**: None

**举例：**
```coffeescript
Page.cmd "wrapperSetTitle", "newtitle"
```

---


#### wrapperSetViewport _viewport_

设置viewport 元标签的内容 (在适配移动版网页时有用)


参数                | 描述
               ---  | ---
**viewport**        | viewport元标签内容

**返回值**: None

**举例：**
```coffeescript
# 提示输入私钥
@cmd "wrapperSetViewport", "width=device-width, initial-scale=1.0"
```


---



# UiServer

UiServer 对于ZeroNet的意义，和LAMP（Linux Apache mySQL和PHP）对普通网站的意义差不多。

UiServer 实现了所有后端处理工作 (例如在数据库中查询，处理文件等），这些API让你能创建动态网站。



#### certAdd _domain, auth_type, auth_user_name, cert_
为当前用户添加新的认证

参数                 | 描述
                 --- | ---
**domain**           | Certificate issuer domain
**auth_type**        | 注册时的认证类型
**auth_user_name**   | 注册时的用户名
**cert**             | 证书主体，即为授权站点方签署的 `auth_address#auth_type/auth_user_name` 

**返回值**: "ok", "Not changed" 或 {"error": error_message}

**举例：**
```coffeescript
@cmd "certAdd", ["zeroid.bit", auth_type, user_name, cert_sign], (res) =>
	$(".ui").removeClass("flipped")
	if res.error
		@cmd "wrapperNotification", ["error", "#{res.error}"]
```


---


#### certSelect _accepted_domains_

显示认证选择器

参数                | 描述
                 --- | ---
**accepted_domains** | List of domains that accepted by site as authorization provider

**返回值**: None

**举例：**
```coffeescript
@cmd "certSelect", {"accepted_domains": ["zeroid.bit"]}
```


---


#### channelJoin _channel_

请求一个站点事件的通知消息

参数        | 描述
        --- | ---
**channel** | 要加入的频道

**返回值**: None

**Channels**:

 - **siteChanged** (joined by default)<br>Events: peers_added, file_started, file_done, file_failed

**举例**   
```coffeescript
# Wrapper websocket connection ready
onOpenWebsocket: (e) =>
	@cmd "channelJoinAllsite", {"channel": "siteChanged"}

# Route incoming requests and messages
route: (cmd, data) ->
	if cmd == "setSiteInfo"
		@log "Site changed", data
	else
		@log "Unknown command", cmd, data
```

**事件数据(event data)示例**
```json
{
	"tasks":0,
	"size_limit":10,
	"address":"1RivERqttrjFqwp9YH1FviduBosQPtdBN",
	"next_size_limit":10,
	"event":[ "file_done", "index.html" ],
	[...] # Same as siteInfo return dict
}

```


---


#### dbQuery _query_
在SQL缓存上运行一个查询语句

参数                | 描述
                 --- | ---
**query**            | SQL语句

**返回值**: <list> 查询结果

**举例：**
```coffeescript
@log "更新用户信息...", @my_address
Page.cmd "dbQuery", ["SELECT user.*, json.json_id AS data_json_id FROM user LEFT JOIN json USING(path) WHERE path='#{@my_address}/data.json'"], (res) =>
	if res.error or res.length == 0 # Db not ready yet or No user found
		$(".head-user.visitor").css("display", "")
		$(".user_name-my").text("Visitor")
		if cb then cb()
		return

	@my_row = res[0]
	@my_id = @my_row["user_id"]
	@my_name = @my_row["user_name"]
	@my_max_size = @my_row["max_size"]
```



---


#### fileDelete _inner_path_
删除一个文件

参数        | 描述
             --- | ---
**inner_path**   | The file you want to delete

**返回值**: 假如成功，返回"ok"；否则返回error message


---


#### fileGet _inner_path, [required], [format], [timeout]_
获取文件内容

参数                    | 描述
                    --- | ---
**inner_path**          | 想获取的文件
**required** （可选）   | 假如文件不存在，继续尝试，并且等待该文件 (默认: True)
**format** （可选）     | 返回的文件的编码形式 (text 或 base64) （默认： text）
**timeout** （可选）    | 超时时长 (默认： 300)

**返回值**: <string> 文件内容


**举例：**
```coffeescript
# 在ZeroTalk里赞一个话题
submitTopicVote: (e) =>
	if not Users.my_name # Not registered
		Page.cmd "wrapperNotification", ["info", "Please, request access before posting."]
		return false

	elem = $(e.currentTarget)
	elem.toggleClass("active").addClass("loading")
	inner_path = "data/users/#{Users.my_address}/data.json"

	Page.cmd "fileGet", [inner_path], (data) =>
		data = JSON.parse(data)
		data.topic_votes ?= {} # Create if not exits
		topic_address = elem.parents(".topic").data("topic_address")

		if elem.hasClass("active") # Add upvote to topic
			data.topic_votes[topic_address] = 1
		else # Remove upvote from topic
			delete data.topic_votes[topic_address]

		# Write file and publish to other peers
		Page.writePublish inner_path, Page.jsonEncode(data), (res) =>
			elem.removeClass("loading")
			if res == true
				@log "File written"
			else # Failed
				elem.toggleClass("active") # Change back

	return false
```


---


#### fileList _inner_path_
目录里的文件列表

参数             | 描述
             --- | ---
**inner_path**   | 想要列出文件列表的目录

**返回值**: 目录里的文件列表 (递归的)


---


#### fileQuery _dir_inner_path, query_
Simple json file query command

参数                | 描述
                 --- | ---
**dir_inner_path**   | 想要的文件的通配符
**query**            | 查询语句命令 （可选）

**返回值**: <list> 匹配的内容

**查询例子**

 - `["data/users/*/data.json", "topics"]`: Returns all topics node from all user files
 - `["data/users/*/data.json", "comments.1@2"]`: Returns `user_data["comments"]["1@2"]` value from all user files
 - `["data/users/*/data.json", ""]`: Returns all data from users files
 - `["data/users/*/data.json"]`: Returns all data from users files (same as above)

**举例：**
```coffeescript
@cmd "fileQuery", ["data/users/*/data.json", "topics"], (topics) =>
	topics.sort (a, b) -> # Sort by date
		return a.added - b.added
	for topic in topics
		@log topic.topic_id, topic.inner_path, topic.title
```


---


#### fileRules _inner_path_
返回文件的规则。

参数                 | 描述
                 --- | ---
**inner_path**       | 文件路径

**返回值**: <list> 匹配的结果

**示例结果**

```json
{
	"current_size": 2485,
	"cert_signers": {"zeroid.bit": ["1iD5ZQJMNXu43w1qLB8sfdHVKppVMduGz"]},
	"files_allowed": "data.json",
	"signers": ["1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj"],
	"user_address": "1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj",
	"max_size": 100000
}
```

**举例：**
```coffeescript
@cmd "fileRules", "data/users/1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj/content.json", (rules) =>
	@log rules
```


---


#### fileWrite _inner_path, content_

写入文件内容


参数          | 描述
               --- | ---
**inner_path**     | 想写入的文件路径
**content_base64** | 想写入的文件内容 (base64编码)

**返回值**: 假如写入成功，返回"ok"。否则返回 error message

**举例：**
```coffeescript
writeData: (cb=null) ->
	# json格式，utf8编码
	json_raw = unescape(encodeURIComponent(JSON.stringify({"hello": "ZeroNet"}, undefined, '\t')))
	# 转换为base64并发送
	@cmd "fileWrite", ["data.json", btoa(json_raw)], (res) =>
		if res == "ok"
			if cb then cb(true)
		else
			@cmd "wrapperNotification", ["error", "File write error: #{res}"]
			if cb then cb(false)
```

_提示：_  假如想要写入那些不在content.json里列出的文件，你必须在  `data/sites.json`中在想操作的站点那一块写入一行`"own": true`


---



#### serverInfo

**Return:** <dict> 服务端的所有消息

**举例：**
```coffeescript
@cmd "serverInfo", {}, (server_info) =>
	@log "服务端信息:", server_info
```

**示例返回值**
```json
{
	"debug": true, # 调试模式
	"fileserver_ip": "*", # 绑定的文件服务IP
	"fileserver_port": 15441, # 绑定的文件服务端口
	"ip_external": true, # 启用被动模式
	"platform": "win32", # OS
	"ui_ip": "127.0.0.1", # 绑定的UiServer 
	"ui_port": 43110, # UiServer端口 (web)
	"version": "0.2.5" # ZeroNet版本号
}
```




---


#### siteInfo

**返回值**: <dict> 本站点的所有信息

**举例：**
```coffeescript
@cmd "siteInfo", {}, (site_info) =>
	@log "Site info:", site_info
```

**示例返回值**
```json
{
	"tasks": 0, # 目前正在下载的文件数
	"size_limit": 10, # 该站点的磁盘大小限额
	"address": "1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr", # 站点地址
	"next_size_limit": 10, # 站点文件数限额（？）
	"auth_address": "2D6xXUmCVAXGrbVUGRRJdS4j1hif1EMfae", # 当前用户的比特币地址
	"auth_key_sha512": "269a0f4c1e0c697b9d56ccffd9a9748098e51acc5d2807adc15a587779be13cf", # （该字段已经废弃，请勿使用）
	"peers": 14, # 连接到该站点的用户节点数
	"auth_key": "pOBdl00EJ29Ad8OmVIc763k4", #（该字段已经废弃，请勿使用）
	"settings":  {
		"peers": 13, # 为了排序用的用户节点编号
		"serving": true, # 是否帮助站点进行内容分发
		"modified": 1425344149.365, # 所有站点文件的最新修改时间
		"own": true, # 是否是自己的站点
		"permissions": ["ADMIN"], # 站点权限
		"size": 342165 # 站点总大小（以字节计）
	},
	"bad_files": 0, # 需要下载的文件
	"workers": 0, # 下载并发数
	"content": { # Root content.json
		"files": 12, # Number of file, detailed file info removed to reduce data transfer and parse time
		"descrption": "This site",
		"title": "ZeroHello",
		"signs_required": 1,
		"modified": 1425344149.365,
		"ignore": "(js|css)/(?!all.(js|css))",
		"signers_sign": null,
		"address": "1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr",
		"zeronet_version": "0.2.5",
		"includes": 0
	},
	"cert_user_id": "zeronetuser@zeroid.bit", # Currently selected certificate for the site
	"started_task_num": 1, # Last number of files downloaded
	"content_updated": 1426008289.71 # Content.json update time
}
```


---


#### sitePublish _[privatekey], [inner_path], [sign]_
Publish a content.json of the site

参数                     | 描述
                     --- | ---
**privatekey** （可选）  | 用于签名的私钥 (默认为当前用户的私钥)
**inner_path** （可选）  | 想要发布的content.json的路径和名称 (默认： content.json)
**sign** （可选）        | 假如为True，则在发布前执行对content.json的签名 (默认： True)

**返回值**: 假如执行成功，返回"ok"；否则返回error message

**举例：**
```coffeescript
# 提示输入私钥
@cmd "wrapperPrompt", ["请输入你的私钥:", "password"], (privatekey) =>
	$(".publishbar .button").addClass("loading")
	# 对content.json 进行签名并发布给服务端
	@cmd "sitePublish", [privatekey], (res) =>
		$(".publishbar .button").removeClass("loading")
		@log "发布结果：", res
```


---


#### siteSign _[privatekey], [inner_path]_
Sign a content.json of the site

参数                    | 描述
                    --- | ---
**privatekey** （可选） | 用于签名的私钥 (默认为当前用户的私钥)
**inner_path** （可选） | 想要发布的content.json的路径和名称 (默认： content.json)

**返回值**: 假如执行成功，返回"ok"；否则返回error message

> __提示__
> ZeroNet会使用"stored" 作为私钥，假如这个字段定义在users.json中 (比如克隆站点的情况)

**举例：**
```coffeescript
if @site_info["privatekey"] # 定义在users.json中的私钥
	@cmd "siteSign", ["stored", "content.json"], (res) =>
		@log "Sign result", res
```


---



#### siteUpdate _address_

Force check and download changed content from other peers (only necessary if user is in passive mode and using old version of Zeronet)

参数          | 描述
          --- | ---
**address**   | 想要更新的站点地址 (除非有ADMIN许可，否则只能在当前站点上执行)

**Return:** None

**举例：**
```coffeescript
# Manual site update for passive connections
updateSite: =>
	$("#passive_error a").addClass("loading").removeClassLater("loading", 1000)
	@log "Updating site..."
	@cmd "siteUpdate", {"address": @site_info.address}
```


---


# Plugin: CryptMessage


#### userPublickey _[index]_

获取用户的站点标识key

参数               | 描述
               --- | ---
**index** （可选） | 站点内的Sub-publickey  (默认： 0)


**返回值**: base64编码过的用户标识公钥

---

#### eciesEncrypt _text, [publickey], [return_aes_key]_

使用公钥加密一段文本

参数                          | 描述
                           --- | ---
**text**                       | 要加密的文本
**publickey** （可选）       | 用户的公钥 (int) 或者base64编码过的用户公钥 (默认为0)
**return_aes_key** （可选）  | 返回加密用的AES key (默认为False)


**返回值**: 用base64编码的加密后的文本，或者[base64编码的加密后的文本，加密用的AES key]

---

#### eciesDecrypt _params, [privatekey]_

解密文本列表

参数                           | 描述
                           --- | ---
**params**                     | 要加密的文本或文本列表
**privatekey** （可选）        | 用户的私钥 (int) 或者base64编码过的用户私钥 (默认为0)


**返回值**:解码后的文本，或者解码后的文本列表（假如解码失败，返回null）

---

#### aesEncrypt _text, [key], [iv]_

使用key和iv来加密一段文本


参数                         | 描述
                         --- | ---
**text**                     | 打算用 AES来加密的文本
**key** （可选）             | Base64编码后的密码 (默认：产生一个新的)
**iv** （可选）              | Base64编码后的iv (默认：产生一个新的)


**返回值**: [base64 编码后的key, base64 编码后的iv, base64 编码后的加密过的文本]


---

#### aesDecrypt _iv, encrypted_text, key_
#### aesDecrypt _encrypted_texts, keys_

使用 IV 和 AES key来解码文本

参数                          | 描述
                           --- | ---
**iv**                         | Base64编码后的iv
**encrypted_text**             | Base64编码后的加密过的文本
**encrypted_texts**            |  [Base64编码后的iv, Base64编码后的加密过的文本] 列表对
**key**                        | 用于该文本的Base64编码后的密码
**keys**                       | 用于解码的密码列表 (每对都尝试)


**返回值**: 解码后的文本，或者解码后的文本列表


---


# 插件：消息流


#### feedFollow _feeds_

设置要处理的SQL查询

SQL查询语句结果会按行列返回:

Field          | 描述
           --- | ---
**type**       | 类型: post, article, comment, mention
**date_added** | 事件时间
**title**      | 事件的标题主要行
**body**       | 事件的第二、三简介行
**url**        | 通向事件页面的链接

参数           | 描述
           --- | ---
**feeds**      | 格式： {"query name": [SQL query, [param1, param2, ...], ...}, 参数 will be escaped, joined by `,` inserted in place of `:params` in the Sql query.

**返回值**: ok

**举例：**
```coffeescript
# 跟踪ZeroBlog的新博文
query = "
	SELECT
	 post_id AS event_uri,
	 'post' AS type,
	 date_published AS date_added,
	 title AS title,
	 body AS body,
	 '?Post:' || post_id AS url
	FROM post
"
params = [""]
Page.cmd feedFollow [{"Posts": [query, params]}]
```

---

#### feedListFollow

返回当前的关注/跟踪的消息流


**返回值**: The currently followed feeds in the same format as in the feedFollow commands


---

#### feedQuery _[limit], [day_limit]_

执行所有堆积的SQL请求


**返回值**: 所有堆积的SQL请求的结果

参数                 | 描述
                 --- | ---
**limit**            | Limit of results per followed site （默认： 10）
**day_limit**        | Return no older than number of this days （默认： 3）

---

# Plugin: MergerSite


#### mergerSiteAdd _addresses_

开始下载新的合并站点

参数                 | 描述
                 --- | ---
**addresses**        | 站点地址，或者站点地址的列表


---

#### mergerSiteDelete _address_

停止为合并站点做种，并且删除合并站点

参数                  | 描述
                  --- | ---
**address**           | 站点地址


---

#### mergerSiteList _[query_site_info]_

返回合并的站点

参数                 | 描述
                 --- | ---
**query_site_info**  | 假如为 True，则返回一个合并站点的详细信息列表



---


# Plugin: Mute

#### muteAdd _auth_address_, _cert_user_id_, _reason_

把用户添加到禁言列表中。 (对于非管理员站点，需要确认)

参数                 | 描述
                 --- | ---
**auth_address**     | 用户数据的目录名
**cert_user_id**     | 用户的认证用户名
**reason**           | 禁言理由

**返回值**: 假如被确认，则为ok 

**举例：**
```coffeescript
Page.cmd("muteAdd", ['1GJUaZMjTfeETdYUhchSkDijv6LVhjekHz','helloworld@kaffie.bit','Spammer'])
```

---

#### muteRemove _auth_address_

把一个用户从禁言用户列表中移除。 (对于非管理员站点，需要确认)

参数                | 描述
                 --- | ---
**auth_address**     | 用户数据的目录名

**返回值**: 假如被确认，则为ok

**举例：**
```coffeescript
Page.cmd("muteRemove", '1GJUaZMjTfeETdYUhchSkDijv6LVhjekHz')
```

---

#### muteList

禁言用户列表 (需要站点的管理员权限)

**返回值**: 禁言用户列表

---


# Plugin: OptionalManager

#### optionalFileList _[address]_, _[orderby]_, _[limit]_

返回可选文件的列表

参数                | 描述
                 --- | ---
**address**          | The site address you want to list optional files (default: current site)
**orderby**          | Order of returned optional files (default: time_downloaded DESC)
**limit**            | Max number of returned optional files (default: 10)

**返回值**: Database 的可选文件记录行: file_id, site_id, inner_path, hash_id, size, peer, uploaded, is_downloaded, is_pinned, time_added, time_downlaoded, time_accessed

---

#### optionalFileInfo _inner_path_

从数据库中请求可选文件的信息

参数                | 描述
                 --- | ---
**inner_path**       | 文件路径

**返回值**: Database 的可选文件记录行: file_id, site_id, inner_path, hash_id, size, peer, uploaded, is_downloaded, is_pinned, time_added, time_downlaoded, time_accessed

---

#### optionalFilePin _inner_path_, _[address]_

Pin 已经下载的可选文件(不包含自动清理可选文件)

参数                | 描述
                 --- | ---
**inner_path**       | The path of the file
**address**          | Address for the file (default: current site)

---

#### optionalFileUnpin _inner_path_, _[address]_

Remove pinning 已经下载的可选文件(包含自动清理可选文件)

参数                 | 描述
                 --- | ---
**inner_path**       | 文件路径
**address**          | 文件的来源地址(默认：当前站点)

---

#### optionalFileDelete _inner_path_, _[address]_

请求删除一个已经下载的可选文件

参数                 | 描述
                 --- | ---
**inner_path**       | 文件路径
**address**          | 文件的来源地址(默认：当前站点)

---

#### optionalLimitStats

返回目前可选文件占用的磁盘空间

**返回值**: 限额大小、已使用大小和空闲大小的状态

---


#### actionOptionalLimitSet _limit_

设置可选文件的空间限制

参数                 | 描述
                 --- | ---
**limit**            | 可选文件的最大大小限制（以GB计算，或已使用空间的百分比）

---

#### actionOptionalHelpList _[address]_

可选下载文件的自动下载列表

参数                 | 描述
                 --- | ---
**address**          | Address of site you want to list helped directories (default: current site)

**返回值**: 自动下载目录的列表和描述 （字典）

---


#### actionOptionalHelp directory, title, _[address]_

把目录添加到自动下载列表中

参数                 | 描述
                 --- | ---
**directory**        | 想要自动下载的目录
**title**            | 条目标题(在ZeroHello里显示)
**address**          | 所生效的站点 (默认是当前站点)

---

#### actionOptionalHelpRemove directory, _[address]_

从自动下载列表中移除掉某些目录

参数                 | 描述
                 --- | ---
**directory**        | 想要从自动下载列表中移除的目录
**address**          | 所生效的站点 (默认是当前站点)

---

#### actionOptionalHelpAll value, _[address]_

帮助下载（和分发）所有新上传到站点的文件
Help download every new uploaded optional file to the site

参数                 | 描述
                 --- | ---
**value**            | 开启/关闭自动下载
**address**          | 所生效的站点 (默认是当前站点)

---

# Admin commands
_(需要data/sites.json的管理员批准)_



---


#### configSet _key, value_

修改ZeroNet的配置文件 (默认是修改zeronet.conf)

 
参数                 | 描述
                 --- | ---
**key**              | 配置条目的键名
**value**            | 配置条目的值


**返回值**: ok


---



#### certSet _domain_

为当前站点设置已生成的证书

参数                | 描述
                 --- | ---
**domain**           | certificate issuer的域名

**返回值**: None


---


#### channelJoinAllsite _channel_

请求每一个站点事件的消息通知

参数                | 描述
               ---  | ---
**channel**         | 想要加入的频道 (参见channelJoin)

**返回值**: None




---


#### serverPortcheck

检查端口是否开放

**返回值**: True (端口打开) 或者 False (端口关闭)


---


#### serverShutdown

停止运行ZeroNet客户端

**返回值**: None


---


#### serverUpdate

从github上重新下载ZeroNet

**返回值**: None


---


#### siteClone _address_, _[root_inner_path]_
把站点文件克隆到新的网站。

所有带有 `-default`后缀的文件和文件夹都会被跳过。而相对地， subfixed 版本会被拷贝。


例如：假如你有一个 `data` 和一个 `data-default` 目录， `data` 目录不会被拷贝，而`data-default` 目录会被考别并且重命名为`data`。

参数                | 描述
               ---  | ---
**address**         | 想克隆的站点地址
**root_inner_path** | 新站点的源目录

**返回值**: None,  （完成时自动重定向到新站点）

---


#### siteList

**返回值**: <list> 所有已经下载的站点的站点信息列表

---


#### sitePause _address_
暂停为站点分发数据

参数                | 描述
               ---  | ---
**address**         | 打算停止为之分发数据的站点地址

**返回值**: None


---


#### siteResume _address_
继续为站点分发数据

参数                | 描述
               ---  | ---
**address**         | 打算继续为之分发数据的站点地址

**返回值**: None


