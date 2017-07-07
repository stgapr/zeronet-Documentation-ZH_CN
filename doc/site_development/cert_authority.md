# 认证授权

不需要密码的账户？ 为我颁发授权证书？你意识到Zeronet的ID系统看起来如此怪异而格格不入。在这个部分，你将要学到ZeroNet是如何实现用户的认证和信息授权的。

## 认证授权是干什么用的?

在 ZeroNet中，所有的东西都用比特币key来签名。一个认证意味着为一个比特币地址提供了独特而可记忆的名字 。认证了的授权信息（或者ID提供者）证明了一个比特币地址和一个容易辨认的短名字之间的对应关系。

## 认证格式

### 主体

认证的主体部分包括了一个比特币地址、一个协议类型和一个可记忆的用户名。

```
[比特币地址]#[协议类型]/[用户名]
```

**例如:**

```
1H28iygiKXe3GUMcD77HiifVqtf3858Aft#web/hellozeronet
```

- 比特币地址: `1H28iygiKXe3GUMcD77HiifVqtf3858Aft`
- 协议类型： `web`
- 用户名： `hellozeronet`

**一般规则：**

比特币地址、协议类型和用户名**不可以** 包含这些字符 `#`, `@` or `/`

只有 0-9 和 a-z 能够用在用户名里。所有的英文字母**都必须** 是小写的。除此之外的字符**不可以**成为用户名的一部分。一个用户名**不可以** 过长，用户名**必须**足够清晰可读，并且**不和**用户地址标识混淆。 

一个用户名 **必须不与**所有已注册的用户名重复，它必须是唯一的。

### 认证

认证签名算法通过签名的密码学私钥为认证信息主体生成了一个明确的比特币签名。

**摘抄自源代码：**

```python
sign = os.popen("python zeronet.py --debug cryptSign %s#bitmsg/%s %s 2>&1" % (auth_address, user_name, config.site_privatekey)).readlines()[-1].strip()
```

### 认证

看看ZeroID的源代码，我们就知道认证授权信息是存储在公共数据库里的。

```python
data["users"][user_name] = "bitmsg,%s,%s" % (auth_address, sign)
```

**例子**

```
"hellozeronet": "web,1H28iygiKXe3GUMcD77HiifVqtf3858Aft,HA2A+iKekECD3hasrsN8IrR86BnXQ63kPH+9A85JLO9hLUpRJTBn62UfnuuF92B9CIc6+EewAIqzIn9UoVq2LPA="
```

认证授权可以通过各种格式来存储，但必须包含以下字段：

- 比特币地址： `1H28iygiKXe3GUMcD77HiifVqtf3858Aft`
- 协议类型: `web`
- 用户名: `hellozeronet`
- 认证签名: `HA2A+iKekECD3hasrsN8IrR86BnXQ63kPH+9A85JLO9hLUpRJTBn62UfnuuF92B9CIc6+EewAIqzIn9UoVq2LPA=`

## 在 `content.json`里的用法

站点拥有者可以选择哪些认证授权方是可信的。

例如 Blue Hub，接收通过ZeroID来签署的认证授权。它的这一规则在它的 `data/users/content.json`里写明了

-  ID 提供者有一个可读的短名字: `zeroid.bit`
- ID提供者的公钥摘要是: `1iD5ZQJMNXu43w1qLB8sfdHVKppVMduGz`

```json
"user_contents": {
  "cert_signers": {
   "zeroid.bit": [
    "1iD5ZQJMNXu43w1qLB8sfdHVKppVMduGz"
   ]
  }
}
```

每一个用户都在他的比特币目录里的清单文件中留下了授权信息，例如`data/users/1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj/content.json` 是这样的:

```json
{
 "address": "1BLueGvui1GdbtsjcKqCf4F67uKfritG49",
 "cert_auth_type": "web",
 "cert_sign": "HPiZsWEJ5eLnspUj8nQ75WXbSanLz0YhQf5KJDq+4bWe6wNW98Vv9PXNyPDNu2VX4bCEXhRC65pS3CM7cOrjjik=",
 "cert_user_id": "nofish@zeroid.bit",
 "files": {
  "data.json": {
   "sha512": "8e597412a2bc2726ac9a1ee85428fb3a94b09f4e7a3f5f589119973231417b15",
   "size": 21422
  }
 },
 "inner_path": "data/users/1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj/content.json",
 "modified": 1492458379,
 "signs": {
  "1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj": "G8kaZIGAstsiWLVY20e2ogJQi4OO+QuwqJ9GTj3gz7YleST/jst7RQH7hDn0uf8BJMBjFs35H3LPhNHHj4jueh8="
 }
}
```

Site specific:

- 期望的站点地址: `"address": "1BLueGvui1GdbtsjcKqCf4F67uKfritG49"`
- 期望的文件路径: `"inner_path": "data/users/1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj/content.json"`

认证信息:

- ID 提供者: `zeroid.bit`
- 用户名: `nofish`
- 用户的比特币地址: `1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj`
- 登录类型: `web`
- ID提供者提供的签名: `HPiZsWEJ5eLnspUj8nQ75WXbSanLz0YhQf5KJDq+4bWe6wNW98Vv9PXNyPDNu2VX4bCEXhRC65pS3CM7cOrjjik=`

### 认证过程

1. 验证算法读取 `data/users/content.json` 来确定用户内容应该发送到哪个站点页面上。

2. 验证算法读取 `data/users/content.json` 来查找ID提供者的公钥摘要。

3. 已知用户的比特币地址、访问类型和用户名，验证算法就可以拼接出授权信息的主体。

4. 验证算法从ID提供者那里检查签名——通过`data/users/content.json`里记录的公钥，来检查授权信息的授权真实性。

5. 验证算法读取用户的公钥，然后检查用户发送内容的授权真实性。

## 认证授权机制的优点和局限之处

- 认证授权机制提供了一个用于替代用户公钥摘要key的、容易辨识记忆的名字。这也有助于缓和垃圾邮件和垃圾信息。

- 用户不需要给出他的私密信息——例如密码。而且，每个用户只需要授权一次。

- 认证授权机制已经被ZeroNet的开发者大力推荐。站点的拥有者可以选择信任部分认证授权，以便于通过用户内容的质量来筛选谁能发言/谁不能发言。

- 认证授权机制有助于维护用户名数据库。

- ZeroID 不需要撤销或者更新授权。

## 我能够不使用认证授权吗？

一般来说，只有在你向别人的网站上发布东西时才会需要你的认证授权。假如只是修改自己的网站，那么不需要进行这一步骤。