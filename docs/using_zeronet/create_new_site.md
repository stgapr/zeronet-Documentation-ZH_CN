# 创建一个新的ZeroNet站点

## 简单方法: 使用浏览器里的web界面

 * 在ZeroHello里点击 **⋮** > **"创建新的空站点"** 菜单项。
 * 你会被 **重定向** 到一个全新的网页，只有你能修改它!
 * 你可以搜索和修改你的站点内容，在 **data/[你的站点地址]** 目录
 * 每次修改后，你需要在浏览器中打开你的站点，向左拖拽页面右上角的 "0" 标志以打开管理侧栏，然后按下面的 **签名** 和 **发布** 按钮。

## 手工方法：使用命令行

### 1. 创建站点的初始结构

* 假如ZeroNet正在运行，关闭它。
* 定位到ZeroNet的安装/运行路径，然后执行

```bash
$ zeronet.py siteCreate
...
- Site private key: 23DKQpzxhbVBrAtvLEc2uvk7DZweh4qL3fn3jpM3LgHDczMK2TtYUq
- Site address: 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
...
- Site created!
$ zeronet.py
...
```

- 这样会在目录 ```data/13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2```下，生成你的站点的初始文件。

> __Note:__
> 对于使用安装包的Windows用户：必须定位到ZeroBundle/ZeroNet目录，然后运行`"../Python/python.exe" zeronet.py siteCreate`

### 2. 新建/修改页面

* 在文件夹的这个地方 ```data/[你的站点地址]``` (例如 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2) 修改或者创建新的网页文件
* 修改完毕之后，运行以下命令：

```bash
$ zeronet.py siteSign 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
- Signing site: 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2...
Private key (input hidden):
```

* 输入你创建这个站点时的私钥。这一步的目的是签署所有文件，以便于别的用户在浏览时确保是站点的所有者——是你做了这些变更。

### 3. 发布页面更改

* 为了通知别的用户你已经对页面进行了更改，你需要执行以下命令：

```bash
$ zeronet.py sitePublish 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
...
Site:13DNDk..bhC2 Publishing to 3/10 peers...
Site:13DNDk..bhC2 Successfuly published to 3 peers
- Serving files....
```

* 大功告成！你已经成功地签署（验证身份）和发布了你修改后的页面。
* 你的网站可以通过这样来浏览了 ```http://localhost:43110/13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2```


**下一步：** [ZeroNet 开发者文档](/site_development/getting_started/)
