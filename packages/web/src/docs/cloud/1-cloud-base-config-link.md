# 1. 云服务器的基础配置与连接

## 序
一次偶然的和朋友攀谈，动起了搞一个网站玩玩的心思。也算是从长久以来的在相对消极的状态中找到一些相对积极的事情做。整日除了和产品测试扯皮，努力维护一些陈旧的代码之外，很多闲暇的时间实际上多处于被内卷的麻木状态，即不知道要做什么，也不知道需要怎么做。<br />既然有了想法，去尝试弄一个网站试试，就算只是部署一个简单的博客，也算是一个新的尝试吧~<br />![00843cM2ly1gl9bqv17lnj30u00u0qiu.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/283876/1644288488172-50bf06d8-fa82-4e40-a4f6-ca6810193158.jpeg#clientId=u3e8337cf-ea80-4&crop=0&crop=0&crop=1&crop=1&from=drop&height=373&id=u3941bc92&margin=%5Bobject%20Object%5D&name=00843cM2ly1gl9bqv17lnj30u00u0qiu.jpg&originHeight=1080&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&size=222408&status=done&style=none&taskId=ua4fd88bd-ef17-461d-a2ba-d670d4f042f&title=&width=373)
## 云服务器的购入
知乎上翻一翻一般能得到相对满意的答案，恰好我今年还不满25，满足腾讯云对于「免学生认证」的要求，可以搞一搞特价服务器。实际上在买入，并进行了一段时间的了解、相关知识学习、尝试部署后，感觉对之前相对模糊的 linux, docker, nginx, 部署方面的知识清晰，熟悉了许多。我不禁想，假如在尚在大学时，有这个决心投资，而且肯费心专研学习，或许毕业时可以找到更符合心意的工作。诶<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644289655263-fde9cb81-69e8-40bd-8415-ff252612d248.png#clientId=u3e8337cf-ea80-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=602&id=u521901f0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=752&originWidth=1512&originalType=binary&ratio=1&rotation=0&showTitle=false&size=106693&status=done&style=none&taskId=ua703e406-8a31-4982-90c4-c005aba2a69&title=&width=1209.6)
## 镜像的选择
镜像选择其实还是有门道在里边的，会看到类似下图的选择（地域就选离自己所在地近的）：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644290513195-8f4e88d1-3173-4802-8c4d-1bc8fb450f82.png#clientId=u3e8337cf-ea80-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=531&id=u5c4a5c09&margin=%5Bobject%20Object%5D&name=image.png&originHeight=664&originWidth=535&originalType=binary&ratio=1&rotation=0&showTitle=false&size=22267&status=done&style=none&taskId=u469e59fd-64cb-45ed-a916-9ab24b85514&title=&width=428)<br />作为前端从业者而言，我对这些东西也不了解。只是莫名觉得 Node.js 要亲切一些，就选择了它。其实这里边是有个小坑的。<br />购买云服务器后，腾讯云会提供给你一个公网地址:<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644291691172-501db514-e89c-46b5-9387-713cd85069b6.png#clientId=u3e8337cf-ea80-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=210&id=u7d0a20a2&margin=%5Bobject%20Object%5D&name=image.png&originHeight=263&originWidth=520&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9660&status=done&style=none&taskId=ub6176aeb-8574-4d9a-9564-793de3c1557&title=&width=416)<br />假如选择的是 Node.js 镜像，访问这个公网地址，页面上会显示 hello world。我是新手，捣鼓了半天才弄大概知道是怎么一回事：

- 选择了 Node.js 镜像时，服务器的 Linux 系统里预装了 Node.js 和 Nginx
- Nginx 服务启动并对 80 端口进行配置，有关 server 的实际配置被 include 中的配置所覆盖，代理了 3000端口，root 指向了 Nodejs 所在目录的 app 文件夹下
-  Nodejs 所在目录的 app 文件夹下有个 app.js ，它就是启动了一个服务占用 3000 端口，并在网页中输出 hello world
- 但是 app.js 似乎被某个守护进程所守护，因为你就算强制 kill 占用 3000 端口的进程，再次访问公网地址，仍然可以看到 hello world，守护进程大概率是 systemctl 弄出来的，不过我没弄懂是怎么操作的。

​

因为这个守护进程没搞懂是怎么操作，加上是新手，还是从相对纯净的环境搭建起。最终还是选择了只装系统镜像，而不装应用镜像。我选的就是第一个 TencentOS Server3.1 ，其内核还是 CentOS 8。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644301339446-68df7766-fc45-4aba-b7d6-d5e076ba67cf.png#clientId=u3e8337cf-ea80-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=462&id=u0f439246&margin=%5Bobject%20Object%5D&name=image.png&originHeight=578&originWidth=1450&originalType=binary&ratio=1&rotation=0&showTitle=false&size=67501&status=done&style=none&taskId=u6be299a0-e973-4390-b836-5b8549af663&title=&width=1160)<br />**在购买时镜像的选择是不需要担心什么的，因为购入服务器后也可以重装镜像，不合适就重装完事。**<br />**​**

**​**<br />
## 本地如何操作远程服务器
我们有了远程服务器，接下来就是希望能在本地对它进行操作。<br />**​**<br />
### 给服务器绑定密钥对
**在所有操作前，先关机，然后重置密码。** 这是为了方便之后的 ssh 连接需要输入密码。而我们不知道初始密码是什么。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644312226289-a7f876e5-3bc2-4fd5-b11a-38112d394e57.png#clientId=u3e8337cf-ea80-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=332&id=uc297b27f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=415&originWidth=773&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23907&status=done&style=none&taskId=u27a9b29c-0f69-41e3-a46d-e4700c68c9a&title=&width=618.4)<br />重置密码后，不着急开机。**给服务器绑定密钥对。**<br />首先在自己服务器的控制台处 -> 密钥 -> **创建SSH密钥**。地域就选择自己购买时选择的地域，我选的是广州，所以这里选的也是广州。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644390957614-eec5a756-1376-4635-a052-c7b40de8056d.png#clientId=u36499702-5cb3-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=642&id=uc9796e14&margin=%5Bobject%20Object%5D&name=image.png&originHeight=802&originWidth=1827&originalType=binary&ratio=1&rotation=0&showTitle=false&size=81481&status=done&style=none&taskId=u8872b43e-7450-454d-ab6b-0bfdbce90e0&title=&width=1461.6)<br />点击确定之后，**网页会弹出一个下载框，会将生成的私钥给你**。记得，这个东西只会生成一次，把它给保存到本地一个地方，方便管理与后续使用。<br />然后，给你的云服务器绑定你的密钥对。方便之后不需要输入密码也能连接。<br />
<br />对于链接远程服务器，进行操作，可以参考官方教程推荐的工具。如果是腾讯云的话，也可以使用它们的网页在线工具进行命令行输入。很多人也推荐 Xshell，貌似收费了，所以很多人也用盗版的 Xshell, 方便文件传输。<br />我自己的话是一个颜值党+白嫖党，所以是不太喜欢 Xshell 的。这里的话推荐几个工具：<br />​<br />
### Tabby
Tabby 是一个有着高颜值的 Terminal 工具，它可以非常方便地配置 ssh 连接端口：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644382210022-a0304135-8d13-454a-b7fa-6ce84c4f2f63.png#clientId=u36499702-5cb3-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=632&id=ucd5dabfe&margin=%5Bobject%20Object%5D&name=image.png&originHeight=790&originWidth=1663&originalType=binary&ratio=1&rotation=0&showTitle=false&size=130232&status=done&style=none&taskId=u24af8bc6-2a02-4acb-b204-71f291d4d61&title=&width=1330.4)<br />上图中，有个添加私钥的按钮，通过它，我们无须每次连接 远程服务器时都输入密码。而私钥我们已经拿到手里了，就是之前**创建SSH密钥**后生成的私钥。<br />这款 Terminal 工具还支持 sftp, 不过操作相对来说还比较僵硬，只能上传/下载文件，但是对于文件夹的操作没那么方便。还有好处是比较轻松即可连接上远程，而且颜值高。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644392529089-881126f2-0a19-4057-bf7f-cbefe970c151.png#clientId=u36499702-5cb3-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=374&id=u336661cb&margin=%5Bobject%20Object%5D&name=image.png&originHeight=468&originWidth=1898&originalType=binary&ratio=1&rotation=0&showTitle=false&size=86748&status=done&style=none&taskId=u9dd2c8c7-bad6-4482-8cd4-5a0028dec23&title=&width=1518.4)<br />不过后续我进行了 **Windows Terminal 和 Remote-SSH** 的配置之后，就觉得 Tabby 的功能相对有些鸡肋了。除非它能把 SFTP 功能做得更好些。<br />​<br />
### SFTP
sftp主要用于文件的传输，它有个好用的地方是可以整个文件夹地进行传输。vscode 安装 sftp 插件，新建一个文件夹。在 vscode 中，快捷键 `Ctrl + shift + p`，输入 `sftp`<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644314211801-44d453bc-de5f-4ffa-bed1-48ee67b97322.png#clientId=u3e8337cf-ea80-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=373&id=u11f6209b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=466&originWidth=779&originalType=binary&ratio=1&rotation=0&showTitle=false&size=32130&status=done&style=none&taskId=ue52a31e1-d112-4bdd-b343-a6eb3fb860d&title=&width=623.2)<br />点击第一个，就会在当前项目根目录下新建 `.vscode` 文件夹并新建 `sftp.json`，进行如下配置：
```json
{
    "name": "cloud",
    "host": "175.xxx.xxx.15",
    "protocol": "sftp",
    "port": 22,
    "password": "*********",
    "username": "root",
    "remotePath": "/",
    "context": ".",
    "uploadOnSave": true
}
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644314491485-9048ea92-01f2-4e2b-8739-9e285b6e4dcc.png#clientId=u3e8337cf-ea80-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=582&id=u3d6a6128&margin=%5Bobject%20Object%5D&name=image.png&originHeight=727&originWidth=1110&originalType=binary&ratio=1&rotation=0&showTitle=false&size=72983&status=done&style=none&taskId=u5df7a71e-e1a5-4a93-a82d-6b9be14e043&title=&width=888)<br />配置项说明：

- name： 就是给这个 sftp 链接取个名字，假如项目中有多个 sftp 链接的话，方便管理
- host： 公网地址
- port： linux 系统一般都是 22 作为 ssh 服务端口
- username：用户名，一般都是 root
- password：密码
- remotePath：**想要连接远程机器目录的绝对地址**，`/`就是 linux 系统的根目录，上图左侧就是远程 linux 机器的目录结构。一般这个地址指向我们部署项目的地址。
- context：**映射到本机目录的相对地址**，默认是项目根目录 `.`，配置这个之后，我们就可以使用 sftp 插件对文件进行上传下载了。

​

初次部署项目时，建议先用类似上图的方法在想要部署在远程的目录中找到一个文件，然后 「**Edit in Local**」, 这时它会将这个文件所在的目录结构都创建好，不过不会包含其他文件，只包含这个文件，在你想要上传的那个目录下新建文件夹即可上传。(对于文件和文件夹都可以右键选择上传，下载）<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644374427933-d3662e8e-0902-4304-b79a-824547f26e54.png#clientId=u36499702-5cb3-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=815&id=uaf20746b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1019&originWidth=1513&originalType=binary&ratio=1&rotation=0&showTitle=false&size=188121&status=done&style=none&taskId=ue2472224-a96b-4098-945b-7b9e2c664e6&title=&width=1210.4)<br />

### Windows Terminal
忘了在哪篇文章中读到过，所以了解了win10商店中有个高颜值的 Terminal，而且与 WSL2 无缝契合。所以有了使用 Windows Terminal 来实现 ssh 远程连接的念头。比较是Windows自家产品，用起来应该不比 **Tabby **差。<br />说搞就搞，走起。<br />**一、在设置处 「打开JSON文件」**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644395749099-4448878d-0b6a-407f-99e9-61c9ab7483ce.png#clientId=ua99bfc06-cb5b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=658&id=u5dba0518&margin=%5Bobject%20Object%5D&name=image.png&originHeight=823&originWidth=1051&originalType=binary&ratio=1&rotation=0&showTitle=false&size=299237&status=done&style=none&taskId=u20f59d5a-2f82-4d72-b300-d15ffb49142&title=&width=840.8)<br />**二、在打开的 JSON 文件中，找到 「profiles」 字段，在这个对象下有个「list」 属性，它是一个数组类型，用于配置各种类型的命令行输入窗口：**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644396013585-227457e4-b593-4215-bc4d-c46f11623fec.png#clientId=ua99bfc06-cb5b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=658&id=u6ac02814&margin=%5Bobject%20Object%5D&name=image.png&originHeight=823&originWidth=1051&originalType=binary&ratio=1&rotation=0&showTitle=false&size=454599&status=done&style=none&taskId=ub2236e81-582e-4401-a33d-c138bb85840&title=&width=840.8)<br />**三、在 list 中加入配置对象，格式如下：**
```json
{
    "guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b8}",
    "hidden": false,
    "name": "blog",
    "commandline": "ssh -i D:\\xxxx\\xxxxx root@175.178.70.15",
}
```

- **guid**：唯一标识，可以从[https://www.guidgenerator.com/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.guidgenerator.com%2F)生成一份。
- **hidden**：是否隐藏。
- **name**：标题名，显示在窗口的标签名称。
- **commandline**：命令行内容，这里通过 windows cmd 的 ssh 连接到远程 linux 服务器。其中 **「D:\\xxxx\\xxxxx」是我们之前下载下来的私钥存放的位置，这样即可无须输入密码**

​

上边的的配置中，很重要的一点就是 **commandline **, 这是我们能无须输入密码就可连接上远程服务器的关键。灵感来自于**「通过 ssh 命令携带密钥连接到远程服务器」**，但是这里边还有个坑：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644398210996-da03d6a5-621c-4180-8748-dc23525cd8a5.png#clientId=ua99bfc06-cb5b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=265&id=u94b9d46f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=331&originWidth=727&originalType=binary&ratio=1&rotation=0&showTitle=false&size=36870&status=done&style=none&taskId=ub97cafca-8c21-45f4-bbb8-39f96746632&title=&width=581.6)<br />在尝试使用 ssh 命令连接时会报这个错误：
> Permissions for 'xxxxx' are too open.



### ERROR：Permissions for 'xxxxx' are too open.
这是由于对于这个私钥而言，权限太开放了。也就是拥有对这个私钥文件权限的用户太多了。这个通过上网查询方法可以得到解决。[win10 通过 ssh 连接云服务器失败 are too open. bad permissions.](https://www.cnblogs.com/chkhk/p/13414823.html) <br />**在解决这个问题前，建议先备份一下。因为私钥只会生成一次。**<br />在 windows 中大致解决思路是，先通过 属性 -> 安全 -> 高级 -> 禁用继承 -> 删除所有..权限条目<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644399338970-8ff90738-5c0a-4c97-9f20-4954d5c7a4d3.png#clientId=ua99bfc06-cb5b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=600&id=u67919c17&margin=%5Bobject%20Object%5D&name=image.png&originHeight=750&originWidth=1148&originalType=binary&ratio=1&rotation=0&showTitle=false&size=76328&status=done&style=none&taskId=u8ffcebad-f305-4b70-9f51-5115c37dde8&title=&width=918.4)<br />然后再在这个窗口 添加 -> 选择主体 -> 高级 -> 立即查找，找到自己登录 Windows 的账户名。添加进去。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644399554037-9d676c69-f76e-4669-bb08-9ab04c6f9645.png#clientId=ua99bfc06-cb5b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=683&id=u30e104d8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=854&originWidth=768&originalType=binary&ratio=1&rotation=0&showTitle=false&size=60879&status=done&style=none&taskId=u3d733ff6-86a2-48f5-9490-555197eca43&title=&width=614.4)<br />这样，这个修改权限后的私钥就只有你登入的账户的权限了。这时就可以通过 ssh 携带密钥的命令连接到远程服务器。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644400068469-391c1351-1a2d-4821-9c1f-bf3b6d2431a0.png#clientId=ua99bfc06-cb5b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=503&id=u28f36fbb&margin=%5Bobject%20Object%5D&name=image.png&originHeight=629&originWidth=1051&originalType=binary&ratio=1&rotation=0&showTitle=false&size=348758&status=done&style=none&taskId=u1aacf23e-5022-4069-b03d-1d242147be6&title=&width=840.8)
### Remote - SSH
到头来还得是夸夸 VSCode，这种问题，大佬们自然是早就想到将 SSH 这种功能集成到万能的 VSCode 当中。我个人也推荐使用这种方式来连接远程服务器，包括文件传输。那为什么还要介绍前面前面那几种呢？实话说，也是本篇文章书写到一半的时候，想起了 **Windows Terminal **，在解决「通过 ssh 携带密钥连接云服务器失败」的问题中，恍然想起了之前尝试 **Remote - SSH **，使用密钥连接时，也老是有哪个错，但是当时一直没什么头绪。直到解决上边的问题，发现问题是一样的。但前边的东西也写了不少了，也算是自己摸索的一个思路吧。🐶而且 Tebby 和 Windows Terminasl 也算得上高颜值的 Terminal，偶尔用用也是不错的。<br />话说回到 Remote - SSH，它也是 VSCode 的一个插件。具体使用步骤如下：<br />**一、安装后，点击 「+」 号，并按类似 **`**ssh root@175.xxx.xxx.15 -A**`**的格式输入后回车：**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644410475121-b124034f-9c5f-4c1c-9532-697e5a806fe3.png#clientId=ufc68df6e-6be2-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=439&id=u671aa8a3&margin=%5Bobject%20Object%5D&name=image.png&originHeight=549&originWidth=1404&originalType=binary&ratio=1&rotation=0&showTitle=false&size=48469&status=done&style=none&taskId=u599dff6d-4b9b-40ed-98a4-20ea7c25903&title=&width=1123.2)<br />** 二、之后会弹出这样一个框，下边有许多选项，这个框的意思是让我们选择一个文件对 Remote - SSH 的连接进行配置，这里原来默认的话会显示 **`**C:/Users/你的电脑用户名/.ssh/config**`**，也就是会在 .ssh 文件夹下生成一个没有后缀名，名为 config 的配置文件，不过这配置文件放在哪都是可以的，我放在了D盘下：**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644410605141-ec832d64-3a6d-4179-9ff2-caa56b10926d.png#clientId=ufc68df6e-6be2-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=162&id=u9a698bd4&margin=%5Bobject%20Object%5D&name=image.png&originHeight=202&originWidth=885&originalType=binary&ratio=1&rotation=0&showTitle=false&size=13863&status=done&style=none&taskId=uf5d1c4ce-c9f2-4561-a1b0-f0d522c6595&title=&width=708)<br />**三、以上图为例子，「D:\vscodessh\」这个路径下的名为「ssh」的配置文件，大概写成这个样子就行：**
```
Host blog
  HostName 175.xxx.xxx.15
  User root
  IdentityFile "D:\xxxx\私钥文件"
  ForwardAgent yes
```

- Host：就是给连接的远程服务器起个名字
- HostName：远程服务器的公网地址
- User：远程服务器的User
- **IdentityFile：私钥文件，这个私钥文件也就是我们按上边方法处理过 「Permissions for 'xxxxx' are too open」权限的私钥文件**
- ForwardAgent：这个也不需要管它，这是自带的

​

假如你想连接多个远程服务器，那就按上边的格式在当前的文件继续往下写就行。<br />​

配置完成后，我们点击那个打开弹窗的小图标，就会帮我们打开新窗口，自动连接上远程服务器（可能第一次需要输入密码），新弹窗左下角也会提示我们连接上了。而且终端处可以直接输入执行 linux 命令。<br />**然后我们点击打开文件夹，选择一个想要进行编辑的文件夹，即可在里边进行文件的增删改查，这些操作是同步到远程的。🐮🍺！**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644411879228-8f166bad-3ced-401f-b663-fff618777b7b.png#clientId=ufc68df6e-6be2-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=566&id=u58cde03b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=708&originWidth=1897&originalType=binary&ratio=1&rotation=0&showTitle=false&size=125477&status=done&style=none&taskId=u22b19d6b-3535-4a1d-a690-7d4322def4e&title=&width=1517.6)<br />​

至此，有关远程连接的东西基本讲完了，接下来就是准备整一个博客了。~<br />![p249201516.webp](https://cdn.nlark.com/yuque/0/2022/webp/283876/1644413280020-5442ab28-448f-4040-b75e-32277aceedf6.webp#clientId=ufc68df6e-6be2-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=246&id=u68dcc2fa&margin=%5Bobject%20Object%5D&name=p249201516.webp&originHeight=307&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&size=31012&status=done&style=none&taskId=u5a1cde5d-5c8a-4ce4-b1c8-9b5f98efe52&title=&width=864)
## 参考

- [windows terminal 连接远程 ssh](https://www.jianshu.com/p/b7a105a67253)​
- [ssh 携带密钥连接到远程服务器](https://docs.microsoft.com/zh-cn/azure/virtual-machines/linux/ssh-from-windows#connect-to-your-vm)
- [win10 通过 ssh 连接云服务器失败 are too open. bad permissions.](https://www.cnblogs.com/chkhk/p/13414823.html)

**​**<br />
