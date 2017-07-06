# content.json的结构

每一个ZeroNet站点都有一个 `content.json`文件. ([示例content.json文件](https://github.com/HelloZeroNet/ZeroTalk/blob/master/content.json))

这个文件会记录一张记载有所有站点上的文件和用你的私钥签名后的列表。这种设计是为了防止文件非法篡改（也就是说，只有你，或者你信任的人，才能更新站点的内容）

这里是目前支持的keys:


# 自动生成


---

### address

你的站点地址

**举例**: 1TaLk3zM7ZRskJvrh3ZNCDVGXvkJusPKQ


---


### address_index

The site's address BIP32 sub-key index  of BIP32 种子。在你克隆一个网站时自动生成。你可以从BIP32种子来还原推算出站点的私钥。

**Example**: 30926910

---


### cloned_from

克隆来源的那个站点的地址

**Example**: 1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8

---


### clone_root

克隆来源站点的那个站点的顶层子目录

**Example**: template-new


---


### files

当浏览你的站点时，会自动下载的那些文件的大小和SHA512哈希值。在执行 `zeronet.py siteSign siteaddress privatekey`操作时，自动生成。

**Example**:
```json
    "css/all.css": {
      "sha512": "869b09328f07bac538c313c4702baa5276544346418378199fa5cef644c139e8",
      "size": 148208
```


---


### files_optional

当浏览你的站点时，可选下载的那些文件的大小和SHA512哈希值。在执行 `zeronet.py siteSign siteaddress privatekey`操作时，自动生成。

**Example**:
```json
    "data/myvideo.mp4": {
      "sha512": "538c09328aa52765443464135cef644c144346418378199fa5cef61837819538",
      "size": 832103
```



---


### modified

content.json 的最近修改日期

**Example**: 1425857522.076


---


### sign (已废用)

content.json 文件内容的ECDSA签名 (按keys排序， without whitespace and the `sign` and `signers_sign` nodes)。
这是为了版本向后兼容保留下来的。很快就会在新版本中移除。

**Example**:
```json
  "sign": [
    43117356513690007125104018825100786623580298637039067305407092800990252156956,
    94139380599940414070721501960181245022427741524702752954181461080408625270000
  ],
```


---


### signers_sign

Possible signers address for the root content.json signed using the site address private key. (Multisig possibility)

**Format of the signed string**: [number_of_signers_required]:[signer address],[signer address]

**Example**: <small>HKNDz9IUHcBc/l2Jm2Bl70XQDL9HYHhJ2hUdg8AMyunACLgxyXBr7EW1/ME4hGkaFZSFmIxlInmxH+BrMVXbnLw=</small>

*Another Example*:
```
signs_required: 1:1PcxwuHYxuJEmM4ydtB1vbiAY6WkNgsz9G,1CK6KHY6MHgYvmRQ4PAafKYDrg1ejbH1cE
signed message: MEUCIQDuz+CzOVvFkv1P2ra9i5E1p1G0/1cOGecm7GpLpMLhuwIgBIbCL0YHXD1S2+x48QS5VO/rISrkdLiUR+o+x1X0y1A=
```
The above signed message is signed using the address, "1PcxwuHYxuJEmM4ydtB1vbiAY6WkNgsz9G"

---


### signs

content.json 文件内容的ECDSA签名 (keys sorted, without whitespace and the `sign` and `signers_sign` nodes).

**Example**:
```json
  "signs": {
    "1TaLk3zM7ZRskJvrh3ZNCDVGXvkJusPKQ": "G6/QXFKvACPQ7LhoZG4fgqmeOSK99vGM2arVWkm9pV/WPCfc2ulv6iuQnuzw4v5z82qWswcRq907VPdBsdb9VRo="
  },
```


----


### zeronet_version

生成 content.json 文件的ZeroNet版本号。

**Example**: 0.2.5


---


# Settings


### background-color

装饰器的背景色

**Example**: #F5F5F5


---


### cloneable

假如为**true**，则允许该站点被克隆

为了让你的站点结构能够被正确完整地克隆，你应该为了清洁启动而附上空的数据文件（例如：没有任何帖子记录的数据文件）。为此，你应该在这些空数据文件和目录前加上**-default** 前缀。
在克隆的过程中，假如文件和目录有一个**-default**前缀，则它会被跳过（翻译注：个人觉得是保留下来）。然后这个前缀会在这些文件中移除。

---


### description

你的站点的描述。在ZerHello的网站列表中作为标题来显示

**举例**: 我的去中心化BBS demo


---


### domain

你的站点的Namecoin 域名。假如用户开启了ZeroName插件，ZeroHello 会把Namecoin域名作为跳转链接。

**举例**: Blog.ZeroNetwork.bit




---


### ignore

在签署文件列表时，忽略部分文件的通配表达式


**举例**: `((js|css)/(?!all.(js|css))|data/users/.*)` (忽略所有JS和CSS 文件，除了all.js与all.css。并且不添加data/users/ 目录)


---


### includes

包含另一个content.json文件

**举例**:

```json
{
  "data/users/content.json": {
    "signers": [ # Possible signers address for the file
      "1LSxsKfC9S9TVXGGNSM3vPHjyW82jgCX5f"
    ],
    "signers_required": 1 # 为了确认一个文件的真实性而需要的有效签名**数量**  (允许多签名)
    "files_allowed": "data.json", # 通过include包含进来的文件类型的通配表达式
    "includes_allowed": false, # 是否允许Nested includes
    "max_size": 10000, # 通过include包含进来的最大文件大小 (按字节数计算)
  }
}
```


---


### merged_type

特殊的合并型站点的数据源

**举例**: `ZeroMe`


---


### optional

指定可选下载文件的通配表达式

**Example**: `(data/mp4/.*|updater/.*)` (在data/mp4 和updater目录中的文件都是可选下载的)


---


### signs_required

为了确认一个文件的真实性而需要的有效签名**数量**  (允许多签名)


**举例**: 1


---


### title

站点标题。在浏览器标题栏显示，也会在ZeroHello里显示。

**举例**: ZeroTalk


----


### translate

需要被翻译的文件. (`languages` 目录里有翻译用的json文件)

**Example**: ["index.html", "js/all.js"]


----


### favicon

站点的logo。你可以手动设置它，以取代ZeroNet提供的默认站点logo。

**举例**: myfavicon.ico


----


### user_contents

本目录允许的用户内容的规则列表


json节点                | 描述
                  ---  | ---
**cert_signers**       | 接受的域名和其有效签署者的地址
**permission_rules**   | 在域名通过认证和有授权的前提下，接受的文件名和总的目录大小。
**permissions**        | 每用户的许可. (false = 拉黑用户)

**举例**:
```json
  "user_contents": {
    "cert_signers": {
      "zeroid.bit": [ "1iD5ZQJMNXu43w1qLB8sfdHVKppVMduGz" ]
    },
    "permission_rules": {
      ".*": {
        "files_allowed": "data.json",
        "max_size": 10000
      },
      "bitid/.*@zeroid.bit": { "max_size": 40000 },
      "bitmsg/.*@zeroid.bit": { "max_size": 15000 }
    },
    "permissions": {
      "bad@zeroid.bit": false,
      "nofish@zeroid.bit": { "max_size": 100000 }
    }
  }
```

----


### viewport

viewport 元标签的内容. (用于想做移动版适配的页面)

**例如**: width=device-width, initial-scale=1.0
