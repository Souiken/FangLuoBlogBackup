---
title: code-server入门系列（一）
tags:
  - code-server
  - ubuntu
  - linux
  - 教程
date: 2021-08-08 15:45:26
category: 奇奇怪怪的教程
---


~~时隔一年多，泥萌的鸽子王坊洛酱终于更新博客了~~

今天我们来安装一个code-server用来云端写代码 ~~（摸鱼）~~


---

## 安装前的准备

首先，你需要一点点的linux命令行基础，例如

- linux中输入密码时不会显示任何字符，但是密码已经输入进去了
- 使用`Tab`键可以快速补全文件名
- ~~在命令行中输入命令后按下回车才能执行这条命令~~
- ~~很多时候你只需要一路回车即可采用默认值完成设置~~

然后，你需要一个域名并申请SSL证书！
*这很重要，因为code-server的部分功能必须要https访问才可用*s

### 准备一个服务器

这次我选择了`Ubuntu 20.04.2 LTS`作为服务器的操作系统
~~为什么要用Ubuntu呢？因为习惯了~~

服务器嘛自己买去，或者用家里的老电脑`(*/ω＼*)`

### 新建一个用户

在code-server中是可以访问控制台的，为了保护服务器的安全最好新建一个用户：

```bash
sudo adduser code
#其中code是你的用户名，如果你用别的用户名，后文也请改成你的用户名 
```

然后按提示设置密码等信息

---

## 安装code-server

推荐使用官方的一件安装脚本：

```bash
curl -fsSL https://code-server.dev/install.sh | sh
```

直接运行即可

### 国内服务器安装加速

使用这个安装脚本：

```bash
curl -fsSL https://raw.fastgit.org/cdr/code-server/main/install.sh | sed 's https://github.com/ https://hub.fastgit.org/ g' | sh
```

*说明：这个命令使用fastgit获取安装脚本，并将脚本中GitHub的链接替换为fastgit链接实现加速安装。*

### 手动上传deb文件

如果你觉得加速脚本还是太慢了，你可以在 [这里](https://github.com/cdr/code-server/releases/) 手动下载最新版deb并上传到服务器`~/.cache/code-server/`目录下，然后再次运行安装脚本就可以跳过下载啦！
*注：`~`为你运行安装脚本的用户的`home`

---

## 让code-server跑起来

### 配置code-server

首先让我们切换到`code`账号
*如果你前文使用了别的账号名，请替换这里的`code`*

```bash
su - code 
```

*如果你之前不是root账号你现在还需要输入密码！*

然后直接运行`code-server`，不出意外你会看到类似这样的输出：

```shell
code@VM-4-3-ubuntu:~$ code-server
[2021-08-08T05:13:12.759Z] info  Wrote default config file to ~/.config/code-server/config.yaml
[2021-08-08T05:13:13.338Z] info  code-server 3.11.1 c680aae973d83583e4a73dc0c422f44021f0140e
[2021-08-08T05:13:13.341Z] info  Using user-data-dir ~/.local/share/code-server
[2021-08-08T05:13:13.368Z] info  Using config file ~/.config/code-server/config.yaml
[2021-08-08T05:13:13.368Z] info  HTTP server listening on http://127.0.0.1:8080
[2021-08-08T05:13:13.368Z] info    - Authentication is enabled
[2021-08-08T05:13:13.369Z] info      - Using password from ~/.config/code-server/config.yaml
[2021-08-08T05:13:13.369Z] info    - Not serving HTTPS
```

这就说明code-server启动成功并生成了配置文件，现在我们可以按`Ctrl+C`关掉它。

然后我们打开它的配置文件：

```bash
vim ~/.config/code-server/config.yaml
```

*本文使用了`vim`，如果你不习惯`vim`，请改用你习惯的编辑器，如`nano`等*

修改`bind-addr`和`password`

```yaml
bind-addr: 0.0.0.0:8080
auth: password
password: #你的密码
cert: false
```

*因为配置文件是yaml格式，冒号后需要有**一个空格**！*

然后我们再次启动`code-server`
现在应该就可以访问 http://你的服务器ip:8080/ 啦
*你可能需要去服务器面板打开防火墙的8080端口*

![code-server](/static/code-server/1.png)

确认能正常打开并使用后就可以开始下一步啦！

### 配置Nginx

这里我使用了Nginx来实现SSL以及多网站并存

*插嘴：为什么要开启SSL呢？首先SSL可以让我们的流量更安全，不过感知最明显的是只有开启SSL后，部分功能才能正常使用。*

没有安装Nginx的请先安装Nginx

```bash
sudo apt install nginx
```

*安装前请先`exit`退出code账号，因为新建的账号没有sudu权限！*

#### 获取SSL证书

我这里采用了腾讯云申请的免费TrustAsia证书，具体操作不再赘述。
现在你需要两个文件，一个文件为`.key`格式，为证书密钥，另一个文件可能为`.pem`或`.crt`格式，为证书文件。
将这两个文件上传到服务器，保存到一个你能找到的位置

#### 配置站点

首先

```bash
cd /etc/nginx/sites-available/
```

进入全部站点文件夹，然后新建一个文件，本文中就叫`code-server`：

```bash
sudo touch code-server
```

然后编辑这个文件：

```bash
sudo vim code-server
```

*本文依然采用`vim`，你可以自行替换为你喜欢的编辑器*

```conf
server {
    listen 443 ssl;
    server_name example.com; #将example.com替换为你的域名，如果不需要绑定域名可以删去本行

    ssl_certificate /path/to/your/certificate.crt; #将/path/to/your/certificate.crt替换为你的crt或pem文件完整路径
    ssl_certificate_key /path/to/your/key.key; #将/path/to/your/key.key替换为你的key文件完整路径

    location / {
        proxy_pass http://localhost:8080;

        #下方提供code-server需要WebSocket支持
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Accept-Encoding gzip;
        proxy_set_header Host $host;
        }
}

# 下面为HTTP自动跳转HTTPS
server {
    listen 80;
    server_name example.com; #将example.com替换为你的域名，如果不需要绑定域名可以删去本行
    return 301 https://$server_name$request_uri;
}
```

将上述内容适当修改后粘贴到前文打开的文件中并保存

然后启用这个site：

```bash
sudo ln code-server ../sites-enabled/
```

现在测试一下Nginx配置是否正确：

```bash
sudo nginx -t
```

看到如下输出说明配置文件正确：

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

#### 启动code-server和Nginx

执行以下命令即可：

```bash
sudo systemctl enable --now code-server@code
sudo systemctl enable --now nginx
```

如果你之前已经启用过Nginx，那么你需要：

```bash
sudo systemctl restart nginx
```

#### 配置DNS

现在，你只需要去你的DNS提供方网站加入记录即可

~~啥？配置DNS还要我教你？哪里买的域名问哪里，基本都有文档教你操作~~

## 简单配置code-server

输入密码进入编辑器后，点击左侧插件按钮，搜索chinese，找到中文（简体）安装即可开启简体中文

![简体中文](/static/code-server/chinese.png)

## 最后

~~这只鸽子好像真的想教会我们欸~~

~~收藏从未停止，实践从未开始~~

下期预告：[code-server入门系列 （二）](/404)
