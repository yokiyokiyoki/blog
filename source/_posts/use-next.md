---
title: use-next
date: 2017-03-08 14:37:41
tags: Hexo
---
# next主题初步使用

## 选择Scheme
> 目前支持三种Scheme

- Muse:默认 Scheme，这是 NexT 最初的版本，黑白主调，大量留白
- Mist:Muse 的紧凑版本，整洁有序的单栏外观
- Pisces:双栏 Scheme，小家碧玉似的清新

Scheme 的切换通过更改 主题配置文件，搜索 scheme 关键字。 你会看到有三行 scheme 的配置，将你需用启用的 scheme 前面注释 # 即可。

这里我使用了Pisces，哈哈小家碧玉我喜欢这个词

## 选择语言
- 在`站点配置文件`里面有language，目前next支持的简体中文是zh-Hans

## 开启标签页面
- `hexo new page tags`新建一个页面tags，然后它会在source下面生成一个tags，里面会有index.md
- 找到上面说的index.md添加`type: "tags"`

## 第三方服务

#### 评论
- 使用了多说评论，首先登录[多说](http://duoshuo.com/)
- 登录了多说之后选择“我要安装”
- 创建站点，填写站点相关信息，这里就是我的blog，多说域名一栏写的就是`duoshuo_shortname`，我的是blog419703726
- 在主题配置文件搜索`duoshuo_shortname`写上blog419703726

#### 分享
- 采用了多说分享
- 在主题配置文件里改`duoshuo_share: true`

#### 数据统计与分析
- 选择了[腾讯分析](http://v2.ta.qq.com/summary/index)，登录并填写域名
- 在主题配置文件里将ID放置在`tencent_analytics` 字段

#### 站内搜索
- 安装hexo-generator-searchdb --save
- 编辑站点配置文件，添加以下内容
```bash
 search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```
-编辑主题配置文件,enable改为true
```bash
local_search:
  enable: true
```


