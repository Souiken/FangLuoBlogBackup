---
title: 联邦宇宙漫游指北——使用 Docker 搭建 Mastodon 实例
tags:
  - mastodon
  - linux
category: 教程
---

*题外话：我也不知道这个标题的第一部分 `联邦宇宙漫游指北` 是怎么想出来的，说不定是看了别人的文章记下来的呢🤣如果真的重名了那就是我抄的别人23333，记得叫我改标题*

## 前言

要想跟随本文成功搭建自己的 Mastodon 实例，你需要以下这些

- 一个 Linux 系统的服务器
  - 服务器需要连接到互联网 *（如果不联网，你的实例将无法与其他实例互联）*
    - 如果你有防火墙，请**保证开放80和443两个端口**！
    - **不建议使用位于中国大陆的服务器搭建 Mastodon 实例！！！**
  - 服务器需要**至少2G**的 RAM 。如果你希望开启全文搜索功能，请保证有**至少4G**的 RAM 。
  - Mastodon 实例会将所有获取到的媒体资源保存在服务器本身，长期运行后会占用较大硬盘空间！
    - Mastodon 在4.0版本开始支持了自动清理一段时间之前的媒体文件，可以减少硬盘占用。
    - Mastodon 支持将资源文件存放在外部，使用**兼容 AWS S3 协议** 的对象存储即可。
- 一个支持 **SMTP** 协议，可以用来**发送邮件**的邮箱账号📤。
- 一个域名。注意域名一旦确定后**不可以**更改！
  - 域名怎么设置解析请参考别的文章，~~因为我真的懒～~~
- 一个会简单使用 Linux 系统的大脑。**本文将不会赘述 Linux 系统的基本操作，如命令行使用等**
- ~~至少一只能操作键盘的手和一只能看屏幕的眼睛😸~~
  - ~~其实就算没有手和眼睛，使用别的方式只要能和服务器交互也是可以的！~~

关于为什么选择 Mastodon 以及从 Twitter 迁移的注意点可以阅读[这篇文章](https://moe23333.vercel.app/posts/twitter-to-mastodon)。

## 设置 DNS 解析

给域名添加一个A记录，指向自己服务器IP就行了。
**特别注意： GFW 近期疑似大规模屏蔽 Mastodon 实例的域名及子域名，目前发现的均为 DNS 污染，不知道是否会解除。如果你希望用这个域名建其它网站并提供中国大陆访问，请把 Mastodon 放在子域名！**
**例如，本站的域名是 `fangluo.top` 而我的 Mastodon 域名为 `nekoland.fangluo.top` 。按照目前的情况， `abc.fangluo.top` 不会被 DNS 污染，而 `abc.nekoland.fangluo.top` 会被 DNS 污染！**

## 安装 Docker

我们需要安装 `Docker` 和 `Docker Compose` 。
本文以 Debian 为例，其它 Linux 发行版请参考官方[文档](https://docs.docker.com/engine/install/)。

```shell
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

复制粘贴上述命令即可。

如果你和我一样是一个在服务器上用 ArchLinux 的~~邪教分子~~，看这里🤪

```shell
sudo pacman -S docker docker-compose
```

~~什么，你连个 Docker 都装不上？如果不是你的系统环境太小众，就是你不满足`前言`中的条件😠！~~

安装后记得让 Docker 的服务开机自启！

```shell
sudo systemctl enable --now docker.service
```

~~（如果你的服务器不使用 systemd ,建议换个系统）~~

## 准备环境

为了方便操作，先切换到 root 用户。

```shell
sudo su
```

建立存放 Mastodon 数据的文件夹，本文以 `/home/mastodon/live/` 为例。

```shell
mkdir -p /home/mastodon/live/
cd /home/mastodon/live/
```

随后获取 Mastodon 的 Docker 镜像和 compose 文件。

```shell
docker pull tootsuite/mastodon:latest
wget https://raw.githubusercontent.com/tootsuite/mastodon/master/docker-compose.yml
```


## 配置 Mastodon

```shell
touch .env.production
docker-compose run --rm web bundle exec rake mastodon:setup
```

此时会出现一个设置向导让你填写一些信息，下方做简单翻译
*对问题留空按下回车则默认使用问题后括号内的内容*
**如果设置有误也没问题，稍后可以修改**

```text
Your instance is identified by its domain name. Changing it afterward will break things.
Domain name: 在这里输入你的实例域名

Single user mode disables registrations and redirects the landing page to your public profile.
Do you want to enable single user mode? 是否开启单用户模式

Are you using Docker to run Mastodon? 是否在用Docker运行，默认是，直接回车

以下为数据库设置，全部留空回车即可
PostgreSQL host: db
PostgreSQL port: 5432
Name of PostgreSQL database: postgres
Name of PostgreSQL user: postgres
Password of PostgreSQL user: 
Database configuration works! 🎆
Redis host: redis
Redis port: 6379
Redis password: 
Redis configuration works! 🎆

Do you want to store uploaded files on the cloud? 是否把用户上传的文件保存到云端，建议No，稍后设置

Do you want to send e-mails from localhost? 因为服务器没有设置邮件服务，no，接下来自行设置smtp
SMTP server: 前言中提到的邮件服务，填写smtp地址
SMTP port: 通常情况下默认587，具体看你使用的邮箱
SMTP username: 用来发邮件的邮箱
SMTP password: 密码
SMTP authentication: 此处默认plain,但是我使用的office365必须使用login
SMTP OpenSSL verify mode: 一般来说默认none即可
E-mail address to send e-mails "from": 推荐使用： 站点名字 <你的邮箱>
例如 Mastodon <notifications@mastodon.local>
Send a test e-mail with this configuration right now? 是否要发送测试邮件，建议是
Send test e-mail to: 接收测试邮件的邮箱地址

This configuration will be written to .env.production
Save configuration? 默认是，回车
```

接下来终端中会出现大段的配置文件内容，**复制它们！**
具体范围是从绿色文本 `Below is your configuration, save it to an .env.production file outside Docker:` 之后，到另一段绿色文本 `It is also saved within this container so you can proceed with this wizard.` 之前。

接下来继续

```text
Prepare the database now? Y

Do you want to create an admin user straight away? 是否创建管理员账号，建议现在创建
Username: 管理员用户名
E-mail: 管理员邮箱
```

完成后会看到一排绿色字 `You can login with the password:` 冒号后的内容为默认管理员密码，保存下来之后登录用。

现在打开 `.env.production` 文件，粘贴前文复制的配置文件并保存。

```shell
nano .env.production
```

## 配置 Web 服务器

先别急着启动你的服务器，因为现在启动了你也访问不了😋

为了省事