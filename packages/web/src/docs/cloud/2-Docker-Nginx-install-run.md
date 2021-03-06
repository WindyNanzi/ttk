# 2. Docker 与 Nginx 的安装与配置

为什么要选择 docker 呢？其实原因也比较简单，单纯为了了解和学习一下这项技术，到底干什么的，了解一些基本的东西。二来相对于 k8s 集群，我觉得上手难度或许会少一些，一口气也吃不成个胖子，慢慢来嘛。而 Nginx 用来作为目前最受欢迎的反向代理服务器，也是有必要了解一下的。<br />我们先定一个小目标：「利用 Docker 与 Nginx 搭建一个静态的网站」

![00843cM2ly1gr0td2i19hj30qc0qkkjl.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/283876/1644891756042-9ea5b756-f3d2-4bb6-9933-608ecbd6ceb2.jpeg#clientId=u4b93d9e9-dc6d-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=473&id=ue41efd4d&margin=%5Bobject%20Object%5D&name=00843cM2ly1gr0td2i19hj30qc0qkkjl.jpg&originHeight=956&originWidth=948&originalType=binary&ratio=1&rotation=0&showTitle=false&size=167612&status=done&style=none&taskId=ue92d502a-aa58-46ad-90a9-e82cef64b32&title=&width=469)

## Docker 的安装

建议去到 Docker 官网 [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/) ，参照它的步骤一步步来，也可以参照网上许多文章，配合着官网的教程来。我这里参照的是：[狂神说 Docker](https://www.bilibili.com/video/BV1og4y1q7M4?spm_id_from=333.999.0.0)<br />[点击查看【bilibili】](https://player.bilibili.com/player.html?bvid=BV1og4y1q7M4)<br />大概步骤其实也就以下几步：

1.  清除旧版 Docker 的安装目录，缓存目录
1.  设置下载 Docker 的镜像地址
1.  下载最新/指定版本的 Docker 以及配套的工具

操作完成之后，在命令行键入：`docker -v`，假如有 Docker 的版本信息显示出来，则证明安装成功了。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1644894921353-631290b9-bb96-4d11-8a0a-268e96e70f1c.png#clientId=u4b93d9e9-dc6d-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=104&id=u20619332&margin=%5Bobject%20Object%5D&name=image.png&originHeight=104&originWidth=351&originalType=binary&ratio=1&rotation=0&showTitle=false&size=7740&status=done&style=none&taskId=u477ddc34-9900-4a6c-8b30-9d4041b75b1&title=&width=351)

## 在 Docker 中添加 Nginx 镜像

在 Docker 安装成功后，使用如下命令：

```shell
systemctl start docker
```

这样，Docker 就被启动起来了，这样就能使用 Docker 的命令行进行操作了<br />添加 Nginx 镜像：

```shell
docker pull nginx
```

## 使用 Docker 启用一个 Nginx 容器

```shell
docker run --name nginx1 -p 8080:80 nginx
```

上边这行命令行的意思是，创建一个名为 「nginx1」 的容器，8080 对应的是主机的端口，即 `175.xxx.xxx.15:8080`，而 80 对应的是容器内部端口， 创建的 「nginx1」的镜像是 nginx

![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1647243866855-0970195d-f728-4db3-875b-eb12578790e9.png#clientId=ue55f15f4-5d2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=358&id=uf95995dc&margin=%5Bobject%20Object%5D&name=image.png&originHeight=448&originWidth=1315&originalType=binary&ratio=1&rotation=0&showTitle=false&size=638355&status=done&style=none&taskId=ufd1535b8-4021-4dac-b51f-b747760acf2&title=&width=1052)<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1647245052493-34ecda36-8481-463c-9fa6-b7758ff140af.png#clientId=ue55f15f4-5d2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=530&id=u3ec6f87c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=662&originWidth=1541&originalType=binary&ratio=1&rotation=0&showTitle=false&size=57864&status=done&style=none&taskId=u21a35d8a-c5d1-4aac-a225-149f3811662&title=&width=1232.8)<br />在云服务器的防火墙中，我们将服务器的 8080 端口暂时打开，允许访问，这样，我们就可以访问到启动好的 nginx 服务了：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1647244039954-ca01a3d2-ab2b-4b5f-ac8e-ba9ea74ff56c.png#clientId=ue55f15f4-5d2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=578&id=u1b92db22&margin=%5Bobject%20Object%5D&name=image.png&originHeight=722&originWidth=1291&originalType=binary&ratio=1&rotation=0&showTitle=false&size=50537&status=done&style=none&taskId=ue501f6a7-990e-46d0-ab99-7c2ba842774&title=&width=1032.8)

## 启动一个方便上传文件的容器

上边我们已经可以成功启动服务了，但是我们还想放上去我们自己的东西。通过 docker 学习，大概了解到在执行 `docker run` 命令时，可以将容器内部的目录也映射到主机的目录，这叫做数据卷。使用 `-v` 添加。我们只要找到映射在哪里就行了。

我们需要知道容器内部的目录结构。首先，我们在刚才创建容器的地方 `Ctrl + C` 停止并退出容器。然后，执行`docker container ls -a`，我们即可看到我们刚刚创建的 「nginx1」在容器列表里边：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1647246380066-b81b5f84-1b9c-40a0-af9c-de24cdf895d0.png#clientId=ue55f15f4-5d2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=234&id=ucc04255f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=292&originWidth=864&originalType=binary&ratio=1&rotation=0&showTitle=false&size=228936&status=done&style=none&taskId=ud7f5c818-cc4b-4a76-931f-81ae3f0443f&title=&width=691.2)<br />然后执行：`docker restart nginx1`，即可启动容器。<br />执行：`docker exec -it nginx1 bash`，即可进入到 「nginx1」容器的内部：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1647246537749-4caee4ca-ad34-4136-a6e4-7fa92fea06bb.png#clientId=ue55f15f4-5d2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=226&id=u39a787d3&margin=%5Bobject%20Object%5D&name=image.png&originHeight=282&originWidth=1283&originalType=binary&ratio=1&rotation=0&showTitle=false&size=315859&status=done&style=none&taskId=ubfc217f5-5fe1-4274-8512-e3b12259ae4&title=&width=1026.4)<br />我们访问得到的页面是 Welcome to nginx! 因此，我们需要找到的是 nginx 的配置在哪。我们可以执行这个命令行找到和 nginx 相关的目录：`whereis nginx`<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1647246737932-5bb9092a-1397-4f0c-9727-e7f10e2d49c0.png#clientId=ue55f15f4-5d2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=185&id=u4e0c4a2b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=231&originWidth=967&originalType=binary&ratio=1&rotation=0&showTitle=false&size=193610&status=done&style=none&taskId=u7d9be08c-1b7d-4cbb-b733-4d1304641e6&title=&width=773.6)

在上边显示出的各个文件夹寻找，包含有 default.conf 的文件夹就是存放 nginx 配置的，包含 index.html 的文件夹就是对应配置的，服务器展示页面的文件夹。<br />排查后，可以知道 /etc/nginx/conf.d/default.conf 对应的是 nginx 的默认配置，而 index.html 存放在 /usr/share/nginx/html 的目录下。<br />了解目录结构后，我们即可退出容器，其中：`exit` 是停止容器并退出，而 `Ctrl + P + Q` 则是退出容器，但不停止。

我们先构想出这样一段命令，但不执行它：

```shell
docker run \
    --name blog \  # 容器名称
    -p 80:80 \ # 映射的端口，冒号前是主机端口，冒号后是容器端口
    -v '/var/blog/nginx/nginx.conf':'/etc/nginx/conf.d/default.conf' \ # 文件映射
    -v '/var/blog/nginx/log':'/var/log/nginx' \ # 文件夹映射
    -v '/var/blog/dist':'/usr/share/nginx/html' \ # 文件夹映射
    nginx # 镜像
```

上边的 3 个 -v 对应 3 个数据映射，我们需要提前将这些文件目录创建好，冒号后的对应的是容器内部的目录，这就不需要我们创建了，而冒号前的也就是我们存放代码，和 nginx 配置，log 的目录，文件。需要提前创建好。<br />/var/blog/dist 目录下需要上传一个 index.html ，其余的代码文件随意。<br />/var/blog/nginx 目录下创建 nginx.conf，以一个最简单的为例：意思是监听 80 端口，而对应的网站目录是容器内部的`/usr/share/nginx/html` ，这是因为我们上传到 dist 目录下的文件会自动同步到我们配置的映射目录中。

```shell
server {
  listen  80;

  location    /   {
    root /usr/share/nginx/html;
    index index.html;
  }
}
```

一切准备就绪后，执行我们构想的命令行。我们的静态 blog 就能通过公网地址访问啦！博客代码我早已写过 （使用 dumi），所以呈现以下效果。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/283876/1647249021529-3be18fe6-abca-4159-bb94-deee8f34b136.png#clientId=ue55f15f4-5d2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=640&id=u28877599&margin=%5Bobject%20Object%5D&name=image.png&originHeight=800&originWidth=1860&originalType=binary&ratio=1&rotation=0&showTitle=false&size=53041&status=done&style=none&taskId=u01bd6273-6b37-4080-8584-3f90a8d5de1&title=&width=1488)
