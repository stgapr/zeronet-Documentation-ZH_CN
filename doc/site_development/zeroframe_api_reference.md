# ZeroFrame API 参考文档



# Wrapper

_These commands handled by wrapper frame and does not sent to UiServer using websocket_


#### wrapperConfirm _message, [button_caption]_
按下确认键的时跳出提示消息

参数                  | 描述
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
**timeout** (optional) | 在多少秒后自动隐藏消息（可选的）（以毫秒计） 

**返回值：**: None

**举例：**
```coffeescript
@cmd "wrapperNotification", ["done", "Your registration has been sent!", 10000]
```


---

#### wrapperOpenWindow _url, [target], [specs]

跳转，或者打开新弹出窗口


参数              | 描述
                  ---  | ---
**url**                | 已打开页面的URL
**target** (可选)  | 目标窗口名称
**specs** (可选)   | 打开的窗口的特定属性 (参见[window.open specs](http://www.w3schools.com/jsref/met_win_open.asp))

**举例：**
```coffeescript
@cmd "wrapperOpenWindow", ["https://zeronet.io", "_blank", "width=550,height=600,location=no,menubar=no"]
```

---


#### wrapperPermissionAdd _permission_

为网站请求新的授权


参数        | 描述
             --- | ---
**permission**   | 授权名 (eg. Merger:ZeroMe)


---

#### wrapperPrompt _message, [type]_

用户输入时的提示文本

参数           | 描述
               ---  | ---
**message**         | 想显示的消息
**type** (optional) | 输入类型 (默认： text)

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
**state**           | State javascript object
**title**           | 页面标题
**url**             | 页面URL

**返回值**: None

```coffeescript
@cmd "wrapperPushState", [{"scrollY": 100}, "Profile page", "Profile"]
```


---

#### wrapperReplaceState _state, title, url_
改变URL，然而不在浏览器历史记录中添加新的一行。 参加: [replaceState JS 方法](https://developer.mozilla.org/en-US/docs/Web/API/History_API#The_replaceState()_method)

参数           | 描述
               ---  | ---
**state**           | State javascript object
**title**           | 页面标题
**url**             | 页面URL

**返回值**: None

```coffeescript
@cmd "wrapperReplaceState", [{"scrollY": 100}, "Profile page", "Profile"]
```


---

#### wrapperSetLocalStorage _data_
为该网站设置浏览器的本地存储数据


参数              | 描述
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

参数              | 描述
                  ---  | ---
**title**              | New browser tab title

**返回值**: None

**举例：**
```coffeescript
Page.cmd "wrapperSetTitle", "newtitle"
```

---


#### wrapperSetViewport _viewport_

 设置viewport 元标签的内容 (在适配移动版网页时有用)


参数           | 描述
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

The UiServer is for ZeroNet what the LAMP setup is for normal websites.

The UiServer will do all the 'backend' work (eg: querying the DB, accessing files, etc). This are the API calls you will need to make your site dynamic.



#### certAdd _domain, auth_type, auth_user_name, cert_
Add a new certificate to current user.

参数            | 描述
                 --- | ---
**domain**           | Certificate issuer domain
**auth_type**        | Auth type used on registration
**auth_user_name**   | User name used on registration
**cert**             | The cert itself: `auth_address#auth_type/auth_user_name` string signed by the cert site owner

**返回值**: "ok", "Not changed" or {"error": error_message}

**举例：**
```coffeescript
@cmd "certAdd", ["zeroid.bit", auth_type, user_name, cert_sign], (res) =>
	$(".ui").removeClass("flipped")
	if res.error
		@cmd "wrapperNotification", ["error", "#{res.error}"]
```


---


#### certSelect _accepted_domains_
Display certificate selector.

参数            | 描述
                 --- | ---
**accepted_domains** | List of domains that accepted by site as authorization provider

**返回值**: None

**举例：**
```coffeescript
@cmd "certSelect", {"accepted_domains": ["zeroid.bit"]}
```


---


#### channelJoin _channel_

Request notifications about sites's events.

参数   | 描述
        --- | ---
**channel** | Channel to join

**返回值**: None

**Channels**:

 - **siteChanged** (joined by default)<br>Events: peers_added, file_started, file_done, file_failed

**Example**:
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

**Example event data**
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
Run a query on the sql cache

参数            | 描述
                 --- | ---
**query**            | Sql query command

**返回值**: <list> Result of the query

**举例：**
```coffeescript
@log "Updating user info...", @my_address
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
Delete a file

参数        | 描述
             --- | ---
**inner_path**   | The file you want to delete

**返回值**: "ok" on success else the error message


---


#### fileGet _inner_path, [required], [format], [timeout]_
Get file content

参数               | 描述
                    --- | ---
**inner_path**          | The file you want to get
**required** (optional) | Try and wait for the file if it's not exists. (default: True)
**format** (optional)   | Encoding of returned data. (text or base64) (default: text)
**timeout** (optional)  | Maximum wait time to data arrive (default: 300)

**返回值**: <string> The content of the file


**举例：**
```coffeescript
# Upvote a topic on ZeroTalk
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
List of files in a directory

参数        | 描述
             --- | ---
**inner_path**   | Directory you want to list

**返回值**: List of files in the directory (recursive)


---


#### fileQuery _dir_inner_path, query_
Simple json file query command

参数            | 描述
                 --- | ---
**dir_inner_path**   | Pattern of queried files
**query**            | Query command (optional)

**返回值**: <list> Matched content

**Query examples:**

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
Return the rules for the file.

参数            | 描述
                 --- | ---
**inner_path**       | File inner path

**返回值**: <list> Matched content

**Example result:**

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

Write file content


参数          | 描述
               --- | ---
**inner_path**     | Inner path of the file you want to write
**content_base64** | Content you want to write to file (base64 encoded)

**返回值**: "ok" on success else the error message

**举例：**
```coffeescript
writeData: (cb=null) ->
	# Encode to json, encode utf8
	json_raw = unescape(encodeURIComponent(JSON.stringify({"hello": "ZeroNet"}, undefined, '\t')))
	# Convert to to base64 and send
	@cmd "fileWrite", ["data.json", btoa(json_raw)], (res) =>
		if res == "ok"
			if cb then cb(true)
		else
			@cmd "wrapperNotification", ["error", "File write error: #{res}"]
			if cb then cb(false)
```

_Note:_ to write files that not in content.json yet, you must have `"own": true` in `data/sites.json` at the site you want to write


---



#### serverInfo

**Return:** <dict> All information about the server

**举例：**
```coffeescript
@cmd "serverInfo", {}, (server_info) =>
	@log "Server info:", server_info
```

**Example return value:**
```json
{
	"debug": true, # Running in debug mode
	"fileserver_ip": "*", # Fileserver binded to
	"fileserver_port": 15441, # FileServer port
	"ip_external": true, # Active of passive mode
	"platform": "win32", # Operating system
	"ui_ip": "127.0.0.1", # UiServer binded to
	"ui_port": 43110, # UiServer port (Web)
	"version": "0.2.5" # Version
}
```




---


#### siteInfo

**返回值**: <dict> All information about the site

**举例：**
```coffeescript
@cmd "siteInfo", {}, (site_info) =>
	@log "Site info:", site_info
```

**Example return value:**
```json
{
	"tasks": 0, # Number of files currently under download
	"size_limit": 10, # Current site size limit in MB
	"address": "1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr", # Site address
	"next_size_limit": 10, # Size limit required by sum of site's files
	"auth_address": "2D6xXUmCVAXGrbVUGRRJdS4j1hif1EMfae", # Current user's bitcoin address
	"auth_key_sha512": "269a0f4c1e0c697b9d56ccffd9a9748098e51acc5d2807adc15a587779be13cf", # Deprecated, dont use
	"peers": 14, # Peers of site
	"auth_key": "pOBdl00EJ29Ad8OmVIc763k4", # Deprecated, dont use
	"settings":  {
		"peers": 13, # Saved peers num for sorting
		"serving": true, # Site enabled
		"modified": 1425344149.365, # Last modification time of all site's files
		"own": true, # Own site
		"permissions": ["ADMIN"], # Site's permission
		"size": 342165 # Site total size in bytes
	},
	"bad_files": 0, # Files that needs to be download
	"workers": 0, # Current concurent downloads
	"content": { # Root content.json
		"files": 12, # Number of file, detailed file info removed to reduce data transfer and parse time
		"描述": "This site",
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

参数                 | 描述
                      --- | ---
**privatekey** (optional) | Private key used for signing (default: current user's privatekey)
**inner_path** (optional) | Inner path of the content json you want to publish (default: content.json)
**sign** (optional)       | If True then sign the content.json before publish (default: True)

**返回值**: "ok" on success else the error message

**举例：**
```coffeescript
# Prompt the private key
@cmd "wrapperPrompt", ["Enter your private key:", "password"], (privatekey) =>
	$(".publishbar .button").addClass("loading")
	# Send sign content.json and publish request to server
	@cmd "sitePublish", [privatekey], (res) =>
		$(".publishbar .button").removeClass("loading")
		@log "Publish result:", res
```


---


#### siteSign _[privatekey], [inner_path]_
Sign a content.json of the site

参数                 | 描述
                      --- | ---
**privatekey** (optional) | Private key used for signing (default: current user's privatekey)
**inner_path** (optional) | Inner path of the content json you want to sign (default: content.json)

**返回值**: "ok" on success else the error message

> __Note:__
> Use "stored" as privatekey if its definied in users.json (eg. cloned sites)

**举例：**
```coffeescript
if @site_info["privatekey"] # Private key stored in users.json
	@cmd "siteSign", ["stored", "content.json"], (res) =>
		@log "Sign result", res
```


---



#### siteUpdate _address_

Force check and download changed content from other peers (only necessary if user is in passive mode and using old version of Zeronet)

参数     | 描述
          --- | ---
**address**   | Address of site want to update (only current site allowed without site ADMIN permission)

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

Get user's site specific publickey

参数            | 描述
                 --- | ---
**index** (optional) | Sub-publickey within site (default: 0)


**返回值**: base64 encoded publickey

---

#### eciesEncrypt _text, [publickey], [return_aes_key]_

Encrypt a text using a publickey

参数                      | 描述
                           --- | ---
**text**                       | Text to encrypt
**publickey** (optional)       | User's publickey index (int) or base64 encoded publickey (default: 0)
**return_aes_key** (optional)  | Get the AES key used in encryption (default: False)


**返回值**: Encrypted text in base64 format or [Encrypted text in base64 format, AES key in base64 format]

---

#### eciesDecrypt _params, [privatekey]_

Try to decrypt list of texts

参数                      | 描述
                           --- | ---
**params**                     | A text or list of encrypted texts
**privatekey** (optional)      | User's privatekey index (int) or base64 encoded privatekey (default: 0)


**返回值**: Decrypted text or list of decrypted texts (null for failed decodings)

---

#### aesEncrypt _text, [key], [iv]_

Encrypt a text using the key and the iv

参数                      | 描述
                           --- | ---
**text**                       | A text encrypt using AES
**key** (optional)             | Base64 encoded password (default: generate new)
**iv** (optional)              | Base64 encoded iv (default: generate new)


**返回值**: [base64 encoded key, base64 encoded iv, base64 encoded encrypted text]


---

#### aesDecrypt _iv, encrypted_text, key_
#### aesDecrypt _encrypted_texts, keys_

Decrypt text using the IV and AES key

参数                      | 描述
                           --- | ---
**iv**                         | IV in Base64 format
**encrypted_text**             | Encrypted text in Base64 format
**encrypted_texts**            | List of [base64 encoded iv, base64 encoded encrypted text] pairs
**key**                        | Base64 encoded password for the text
**keys**                       | Keys for decoding (tries every one for every pairs)


**返回值**: Decoded text or list of decoded texts


---


# Plugin: Newsfeed


#### feedFollow _feeds_

Set followed sql queries.

The SQL query should result in rows with cols:

Field          | 描述
           --- | ---
**type**       | Type: post, article, comment, mention
**date_added** | Event time
**title**      | Event's first line to be displayed
**body**       | Event's second and third line
**url**        | Link to event's page

参数      | 描述
           --- | ---
**feeds**      | Format: {"query name": [SQL query, [param1, param2, ...], ...}, 参数s will be escaped, joined by `,` inserted in place of `:params` in the Sql query.

**返回值**: ok

**举例：**
```coffeescript
# Follow ZeroBlog posts
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

Return of current followed feeds


**返回值**: The currently followed feeds in the same format as in the feedFollow commands


---

#### feedQuery _[limit], [day_limit]_

Execute all followed sql query


**返回值**: The result of the followed Sql queries

参数            | 描述
                 --- | ---
**limit**            | Limit of results per followed site (default: 10)
**day_limit**        | Return no older than number of this days (default: 3)

---

# Plugin: MergerSite


#### mergerSiteAdd _addresses_

Start downloading new merger site(s)

参数            | 描述
                 --- | ---
**addresses**        | Site address or list of site addresses


---

#### mergerSiteDelete _address_

Stop seeding and delete a merged site

参数            | 描述
                 --- | ---
**address**           | Site address


---

#### mergerSiteList _[query_site_info]_

Return merged sites.

参数            | 描述
                 --- | ---
**query_site_info**  | If True, then gives back detailed site info for merged sites



---


# Plugin: Mute

#### muteAdd _auth_address_, _cert_user_id_, _reason_

Add new user to mute list. (Requires confirmation for non-ADMIN sites)

参数            | 描述
                 --- | ---
**auth_address**     | Directory name of the user's data.
**cert_user_id**     | Cert user name of the user
**reason**           | Reason of the muting

**返回值**: ok if confirmed

**举例：**
```coffeescript
Page.cmd("muteAdd", ['1GJUaZMjTfeETdYUhchSkDijv6LVhjekHz','helloworld@kaffie.bit','Spammer'])
```

---

#### muteRemove _auth_address_

Remove a user from mute list. (Requires confirmation for non-ADMIN sites)

参数            | 描述
                 --- | ---
**auth_address**     | Directory name of the user's data.

**返回值**: ok if confirmed

**举例：**
```coffeescript
Page.cmd("muteRemove", '1GJUaZMjTfeETdYUhchSkDijv6LVhjekHz')
```

---

#### muteList

List muted users. (Requires ADMIN permission on site)

**返回值**: List of muted users


---


# Plugin: OptionalManager

#### optionalFileList _[address]_, _[orderby]_, _[limit]_

Return list of optional files

参数            | 描述
                 --- | ---
**address**          | The site address you want to list optional files (default: current site)
**orderby**          | Order of returned optional files (default: time_downloaded DESC)
**limit**            | Max number of returned optional files (default: 10)

**返回值**: Database row of optional files: file_id, site_id, inner_path, hash_id, size, peer, uploaded, is_downloaded, is_pinned, time_added, time_downlaoded, time_accessed

---

#### optionalFileInfo _inner_path_

Query optional file info from database

参数            | 描述
                 --- | ---
**inner_path**       | The path of the file

**返回值**: Database row of optional file: file_id, site_id, inner_path, hash_id, size, peer, uploaded, is_downloaded, is_pinned, time_added, time_downlaoded, time_accessed

---

#### optionalFilePin _inner_path_, _[address]_

Pin (exclude from automatized optional file cleanup) downloaded optional file

参数            | 描述
                 --- | ---
**inner_path**       | The path of the file
**address**          | Address for the file (default: current site)

---

#### optionalFileUnpin _inner_path_, _[address]_

Remove pinning (include from automatized optional file cleanup) of downloaded optional file

参数            | 描述
                 --- | ---
**inner_path**       | The path of the file
**address**          | Address for the file (default: current site)

---

#### optionalFileDelete _inner_path_, _[address]_

Query a downloaded optional file

参数            | 描述
                 --- | ---
**inner_path**       | The path of the file
**address**          | Address for the file (default: current site)

---

#### optionalLimitStats

Return currently used disk space by optional files

**返回值**: limit, used and free space statistics

---


#### actionOptionalLimitSet _limit_

Set the optional file limit

参数            | 描述
                 --- | ---
**limit**            | Max space used by the optional files in gb or percent of used space

---

#### actionOptionalHelpList _[address]_

List the auto-downloaded directories of optional files

参数            | 描述
                 --- | ---
**address**          | Address of site you want to list helped directories (default: current site)

**返回值**: Dict of auto-downloaded directories and 描述s

---


#### actionOptionalHelp directory, title, _[address]_

Add directory to auto-download list

参数            | 描述
                 --- | ---
**directory**        | Directory you want to add to auto-download list
**title**            | Title for the entry (displayed on ZeroHello)
**address**          | Address of site you want to add the auto-download directory (default: current site)

---

#### actionOptionalHelpRemove directory, _[address]_

Remove an auto-download entry

参数            | 描述
                 --- | ---
**directory**        | Directory you want to remove from auto-download list
**address**          | Address of affected site (default: current site)

---

#### actionOptionalHelpAll value, _[address]_

Help download every new uploaded optional file to the site

参数            | 描述
                 --- | ---
**value**            | Enable or Disable the auto-download
**address**          | Address of affected site (default: current site)

---

# Admin commands
_(requires ADMIN permission in data/sites.json)_



---


#### configSet _key, value_

Create or update an entry in ZeroNet config file. (zeronet.conf by default)


参数            | 描述
                 --- | ---
**key**              | Configuration entry name
**value**            | Configuration entry new value


**返回值**: ok


---



#### certSet _domain_

Set the used certificate for current site.

参数            | 描述
                 --- | ---
**domain**           | Domain of the certificate issuer

**返回值**: None


---


#### channelJoinAllsite _channel_

Request notifications about every site's events.

参数           | 描述
               ---  | ---
**channel**         | Channel to join (see channelJoin)

**返回值**: None




---


#### serverPortcheck

Start checking if port is opened

**返回值**: True (port opened) or False (port closed)


---


#### serverShutdown

Stop running ZeroNet client.

**返回值**: None



---


#### serverUpdate

Re-download ZeroNet from github.

**返回值**: None


---


#### siteClone _address_, _[root_inner_path]_
Copy site files into a new one.

Every file and directory will be skipped if it has a `-default` subfixed version and the subfixed version will be copied instead of it.


Eg. If you have a `data` and a `data-default` directory: The `data` directory will not be copied and the `data-default` directory will be renamed to data.

参数           | 描述
               ---  | ---
**address**         | Address of site want to clone
**root_inner_path** | The source directory of the new site

**返回值**: None, automatically redirects to new site on completion


---


#### siteList

**返回值**: <list> SiteInfo list of all downloaded sites


---


#### sitePause _address_
Pause site serving

参数           | 描述
               ---  | ---
**address**         | Address of site want to pause

**返回值**: None


---


#### siteResume _address_
Resume site serving

参数           | 描述
               ---  | ---
**address**         | Address of site want to resume

**返回值**: None


