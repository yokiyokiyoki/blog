---
title: Hexo的开始
date: 2017-03-07 15:29:00
tags: "Hexo"
comments: false
---

# 基于Hexo的博客
> 我的第一个页面哎~


## 开始
>Hexo的[Github](https://github.com/hexojs)和[中文官网](https://hexo.io/zh-cn/)

#### 下载
- Node和Git必须要有
- `npm install -g hexo-cli`下载hexo的脚手架,可以全局使用hexo命令

---

#### 建站

- `hexo init <folder>`初始化文件夹，如果不写folder，那么则在当前目录初始化，如果写了folder则在当前目录创建这个folder，并在folder里面初始化
- `npm install`下载依赖

---

#### 配置
> 网站分别有两个配置文件。一个是 _站点配置文件_ 即根目录下的_config.yml,一个是 _主题配置文件_ 即themes文件夹内的_config.yml


1. __网站__

| 参数 | 含义 |
| ---- | :--: |
| title | 网站名称 |
| subtitle | 网站副标题 |
| description | 网站描述（用于SEO，告诉搜索引擎一个关于您站点的简单描述，通常可以写关键词） |
| author | 本作者啦~ |
| language | 网站语言，这里是zh-CN |
| timezone | 网站时区，默认是电脑的时区 |
| url | 这里是https://yokiyokiyoki.github.io/blog/ |
| root | 根目录是/blog/，这里是css和js找到资源的地方 |

2. __拓展__

| 参数 | 含义 |
| ---- | :--: |
| theme | 启用主题名称，与theme里的名字要一样，这里是meterial，默认是landscape |
| deploy | 部署到GitHub的`type: git,repo: https://github.com/yokiyokiyoki/blog.git,branch: master` |

---

#### 命令
- `hexo new ..`写新的日志
- `hexo g`生成静态文件,不简写的话是`hexo generate`
- `hexo d`部署到`github`，不简写的话是`hexo deploy`（可以先删除deploy_git文件）
- `hexo s`本地启动服务预览，不简写的话是`hexo server`
- `hexo clean`清除缓存文件 (db.json) 和已生成的静态文件 (public)。在某些情况（尤其是更换主题后），如果发现对站点的更改无论如何也不生效，可能需要运行该命令。
- `hexo version`显示`Hexo`的版本
---

## 主题
>采用了next主题，[GitHub](https://github.com/iissnan/hexo-theme-next)和[官网](http://theme-next.iissnan.com/)

-  安装「Next」
    - 在`theme`文件夹下面采用`git clone`下载，缺点是很慢，优点是方便更新
    - 更新：直接拉取远程分支更新。
    - 在`theme`文件夹下，然后改名为 __next__

---

## 分支发布策略
- 可以先删除`.deploy_git`文件
- hexo分支用来保存源代码和编辑文章，每次编辑完文章首先应该推送hexo分支到远端，先`hexo clean`然后执行`hexo g`最后`hexo d`,就会制动部署。
- master分支用来部署静态页面。

## 私密文章
> 草稿其实就是相当于很多博客都有的`私密文章`功能

- `hexo new draft "draftName"`会在source/_drafts目录下生成一个文件，但是这个文件不被显示在页面上，链接也访问不到。也就是说如果你想把某一篇文章移除显示，又不舍得删除，可以把它移动到_drafts目录之中。
- 如果希望强行预览草稿，更改配置文件。`reder_drafts:true`。或者`hexo server --drafts`启动命令行
- 也可以发布草稿，`hexo publish [layout] <title>`,`layout` 来指定布局
