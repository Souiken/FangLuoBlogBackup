---
title: 修改版diaspora主题使用教学
tags:
 - Hexo
 - 主题美化
---

# 安装主题

在Hexo博客根目录下执行此命令

``` bash
git clone https://github.com/rainbowbedrock/hexo-theme-diaspora.git themes/hexo-theme-diaspora
```

或者你也可以手动下载并解压到themes目录下

然后在Hexo配置文件`_config.yml`中修改theme为该主题

``` yml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: hexo-theme-diaspora
```

# 配置主题

## 添加功能页

>### 创建分类页
>
>1.新建一个页面，命名为 categories 。命令如下：
>
>``` bash
>hexo new page categories
>```
>
>2.编辑刚新建的页面，将页面的类型设置为 categories
>
>``` markdown
>---
>title: categories
>type: "categories"
>---
>```
>
>主题将自动为这个页面显示所有分类。
>
>### 创建标签页
>1.新建一个页面，命名为 tags 。命令如下：
>
>``` bash
>hexo new page tags
>```
>
>2.编辑刚新建的页面，将页面的类型设置为 tags
>
>``` markdown
>---
>title: tags
>type: "tags"
>---
>```
>
>主题将自动为这个页面显示所有标签。
>
>### 创建搜索页
>
>1.需要安装hexo的搜索插件
>
>``` bash
>npm install hexo-generator-searchdb --save
>```
>
>2.配置hexo全局配置文件（请将生成的索引文件放在网站根目录或修改主题js文件的path值）
>
>```yml
>search:
>  path: search.xml
>  field: post
>  format: html
>  limit: 10000
>```
>
>3.新建一个页面，命名为 search 。命令如下：
>
>``` bash
>hexo new page search
>```
>
>4.编辑刚新建的页面，将页面的类型设置为 search
>
>``` markdown
>---
>title: search
>type: "search"
>---
>```
>
>5.在主题配置文件启用本地搜索
>
>``` yml
>#本地搜索,请将索引文件放在网站根目录
>local_search:
>    #是否启用
>    enable: true
>
>```
>
>主题将自动为这个页面显示搜索功能。

## 修改主题配置文件

```yml
# 头部菜单，title: link
menu:
  搜索: /search
  归档: /archives
  分类: /categories
  标签: /tags
```
