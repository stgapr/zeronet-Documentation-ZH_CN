# ZeroNet 网络协议

 - 所有信息都用 [MessagePack](http://msgpack.org/)进行编码
 - 每一个request都有三个参数:
    * `cmd`: request的命令
    * `req_id`: request的标识ID (简单地说，一个一次性数值), 客户端在回应命令的时候需要包括它
    * `params`: request带的参数
 - 示例request: `{"cmd": "getFile", "req_id": 1, "params:" {"site": "1EU...", "inner_path": "content.json", "location": 0}}`
 - 示例response: `{"cmd": "response", "to": 1, "body": "content.json content", "location": 1132, "size": 1132}`
 - 示例error response: `{"cmd": "response", "to": 1, "error": "Unknown site"}`


# 握手
每个连接都从发送一个到目标网络地址的request——也就是握手请求——开始：

参数                 | 描述
                 --- | ---
**crypt**            | 空，或者为None， 只在response中用到
**crypt_supported**  | 连接可用的加密方法的数列，列出了客户端支持的加密方法
**fileserver_port**  | 客户端的文件服务端口
**onion**            | (只在Tor上使用) 客户端的onion地址
**protocol**         | 客户端使用的协议版本 (v1 或者 v2)
**port_opened**      | 客户端的端口打开状态
**peer_id**          | (不在Tor上使用) 客户端的peer_id
**rev**              | 客户端的版本修订号
**version**          | 客户端的版本号
**target_ip**        | 客户端的IP地址

目标接着通过`crypt_supported`来在socket上初始化加密，返回：

返回值          | 描述 
                 --- | ---
**crypt**            | 使用的加密方法
**crypt_supported**  | 连接可用的加密方法的数列，列出了服务端支持的加密方法
**fileserver_port**  | 服务端的文件服务端口
**onion**            | (只在Tor上使用) 服务端的onion地址
**protocol**         | 服务端使用的协议版本 (v1 或者 v2)
**port_opened**      | 服务端的端口打开状态
**peer_id**          | (不在Tor上使用) 服务端的peer_id
**rev**              | 服务端的版本修订号
**version**          | 服务端的版本号
**target_ip**        | 服务端的IP地址

> **注意：** .onion连接不会使用任何加密算法，因为Tor网络默认自带了传输加密。
> **注意：** 假如你确定远程客户端也支持的话，可以在握手前启用SSL连接来保证安全性。

**例子**:

发送请求：

```json
{
  "cmd": "handshake",
  "req_id": 0,
  "params": {
    "crypt": None,
    "crypt_supported": ["tls-rsa"],
    "fileserver_port": 15441,
    "onion": "zp2ynpztyxj2kw7x",
    "protocol": "v2",
    "port_opened": True,
    "peer_id": "-ZN0056-DMK3XX30mOrw",
    "rev": 2122,
    "target_ip": "192.168.1.13",
    "version": "0.5.6"
  }
}
```

返回：

```
{
 "protocol": "v2",
 "onion": "boot3rdez4rzn36x",
 "to": 0,
 "crypt": None,
 "cmd": "response",
 "rev": 2092,
 "crypt_supported": [],
 "target_ip": "zp2ynpztyxj2kw7x.onion",
 "version": "0.5.5",
 "fileserver_port": 15441,
 "port_opened": False,
 "peer_id": ""
}
```

# 用户请求

#### getFile _site_, _inner_path_, _location_, _[file_size]_
从客户端请求文件

参数                | 描述 
                 --- | ---
**site**             | 站点地址 (例如: 1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**inner_path**       | 用站点相对路径引用的文件位置
**location**         | 要求从某个字节位置开始传输文件 (一个请求最多发送 512 字节，所以你需要多次request来获得大文件s)
**file_size**        | 所需文件的总大小（可选参数）

**返回**:

返回值              | 描述 
                 --- | ---
**body**             | 对方所需的文件数据
**location**         | 发出去的这些数据的最后一位字节在文件中的位置
**size**             | 发送出的数据大小


---


#### ping
看看客户端是否仍然在线

**返回**:

返回值               | 描述 
                 --- | ---
**body**             | Pong


---


#### pex _site_, _peers_, _need_
和客户端交换用户
用户标识按照6字节来打包(4字节的 IP 地址——使用inet_ntoa ；再加上2字节端口号)

参数                | 描述 
                 --- | ---
**site**             | 网站地址 (例如：1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**peers**            | 请求者拥有的用户列表 (打包后)
**need**             | 请求者需要的用户数

**Return**:

返回值          | 描述 
                 --- | ---
**peers**            | 回应者拥有的用户列表 (打包后)


---

#### update _site_, _inner_path_, _body_
更新站点文件

参数                | 描述 
                 --- | ---
**site**             | 网站地址 (例如：1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**inner_path**       | 用站点相对路径引用的文件位置
**body**             | 被上传文件的完整内容

**Return**:

返回值          | 描述 
                 --- | ---
**ok**               | 成功更新文件的感谢信息

---

#### listModified _site_, _since_
列出 content.json files 自从给定时间以来的的变动。这个命令一般用来获得该站点用户提交带来的内容更新。


参数                | 描述 
                 --- | ---
**site**             | 网站地址 (例如：1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**since**            | 自从这个时间戳之后的 content.json文件（变动）.

**Return**:

返回值          | 描述 
                 --- | ---
**modified_files**   | （json数据）键: content.json inner_path<br>值: 上一次修改时间

**例如**:

```json
> zeronet.py --silent peerCmd 127.0.0.1 15441 listModified "{'site': '1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8', 'since': 1497507030}"
{
  "to": 1,
  "cmd": "response",
  "modified_files": {
    "data/users/1NM9k7VJfrb1UWw5agAvyRfSn3ws1wTJ5U/content.json": 1497579272,
    "data/users/1QEfmMwKVxgR4rkREbdJYjgUmF3Zy8pwHt/content.json": 1497565986,
    "data/users/16NS3rBdW9zpLmLSQoD8nLTtNVsRFtVBhd/content.json": 1497575039,
    "data/users/1CjXarXgvcNeCJ2nMQxUi4DRFWp3GEur2W/content.json": 1497513808,
    "data/users/1L5rGDgTs4W2V7gekSvJNhKa7XaHkVwotD/content.json": 1497615798,
    "data/users/1LWuc6JBhUGrKEAh1aPrPU85dEMcKmg3pS/content.json": 1497594716,
    "data/users/1KdnTJVBGzEZrJppFZtzfG9chukuMv8xSb/content.json": 1497584640,
    "data/users/1GMNmr2bDPbT4c8yVnyCoDHke52CNCdqAa/content.json": 1497614188,
    "data/users/1GRm9rED83Tkfi3iWS9m3LWHiRpPZehWLd/content.json": 1497827772,
    "data/users/12Ugp53jiMdvj1Kxa1w7c2LcXUBdGPs1oK/content.json": 1497692901,
    "data/users/1F6BMqittjWUStzUbRXm2kG2GQ3RdBLqFQ/content.json": 1497571485,
    "data/users/1GgNo3CmxPd7n2pMSF3uyqf1XHvgtTUqCe/content.json": 1497560829,
    "data/users/16nArdxrSaNThNp83kL8E6NLL9WD98iUne/content.json": 1497627929,
    "data/users/16CAJkbfNRxNJq4aKdrZ2MSYFfFGvQ8JPi/content.json": 1497664899,
    "data/users/1DrBS2sTD3BX5BBxG8eqYsxXSvGt9kc5HE/content.json": 1497632000,
    "data/users/19sggoAZ4hcorrrfWoFWP9rwfpVsL29cnZ/content.json": 1497928134,
    "data/users/1NYpJupegoTXL4cFpkNdLNJ4XaAhTNhPe1/content.json": 1497535771,
    "data/users/1R67TfYzNkCnh89EFfGmXn5LMb4hXaMRQ/content.json": 1497691787,
    "data/users/1C9HXUYFSVafLxanwkaFPZRcRgCEGsj2Cn/content.json": 1497572833,
    "data/users/1LgoHzNGWeijeZbJ8a1YgGjMCnjaM4BWG/content.json": 1497620232,
    "content.json": 1497623639
  }
}
```

---


#### getHashfield _site_
获得客户端下载的 [所选文件ID](#optional-file-id).

参数                | 描述 
                 --- | ---
**site**             | 站点地址 (例如：1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)

**Return**:

返回值          | 描述 
                 --- | ---
**hashfield_raw**    | 可选的文件ID。已经被编码过，编码方法是 `array.array("H", [1000, 1001..]).tostring()`

**举例**:
```json
> zeronet.py --silent peerCmd 192.168.1.13 15441 getHashfield "{'site': '1Gif7PqWTzVWDQ42Mo7np3zXmGAo3DXc7h'}
{
  'to': 1,
  'hashfield_raw': 'iG\xde\x02\xc6o\r;...',
  'cmd': 'response'
}
```

---


#### setHashfield _site_, _hashfield_raw_
设置请求方具有的 [可选文件ID](#optional-file-id) 列表

参数                | 描述 
                 --- | ---
**site**             | Site address (例如：1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**hashfield_raw**    | Optional file ids encoded using `array.array("H", [1000, 1001..]).tostring()`

**Return**:

返回值          | 描述 
                 --- | ---
**ok**               | Updated


---


#### findHashIds _site_, _hash_ids_
询问客户端是否知道某个用户拥有所需的文件的hash_ids

参数                | 描述 
                 --- | ---
**site**             | 站点地址 (例如：1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr)
**hash_ids**         | 由需要客户端查找的、所选文件id组成的数组。

**Return**:

返回值          | 描述 
                 --- | ---
**peers**            | （json形式）键: 所选文件id<br>值: ipv4 用户列表，已经被编码，编码方法为  `socket.inet_aton(ip) + struct.pack("H", port)`
**peers_onion**      | （json形式）键: 所选文件id<br>值: onion 用户列表，已经被编码，编码方法为 `base64.b32decode(onion.replace(".onion", "").upper()) + struct.pack("H", port)`

**举例**:
```json
> zeronet.py --silent peerCmd 192.168.1.13 15441 findHashIds "{'site': '1Gif7PqWTzVWDQ42Mo7np3zXmGAo3DXc7h', 'hash_ids': [59948, 29811]}"
{
  'to': 1,
  'peers': {
    29811: [
      'S&9\xd3Q<',
      '>f\x94\x98N\xa4',
      'gIB\x90Q<',
      '\xb4\xady\xf7Q<'
    ],
    59948: [
      'x\xcc>\xf6Q<',
      'S\xa1\xddkQ<',
      '\x05\xac\xe8\x8dQ<',
      '\x05\xc4\xe1\x93Q<',
      'Q\x02\xed\nQ<'
    ]
  },
  'cmd': 'response',
  'peers_onion': {
    29811: ['\xc7;A\xce\xbc\xd9O\xe2w<Q<'],
    59948: ['\xc7;A\xce\xbc\xd9O\xe2w<Q<']
  }
}
```

---


# 可选文件ID
文件hash值的前四位的整型值
```
>>> int("ea2c2acb30bd5e1249021976536574dd3f0fd83340e023bb4e78d0d818adf30a"[0:4], 16)
59948
```
