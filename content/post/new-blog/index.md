---
title: "New Blog"
description: Hugo, Github Actions 和 腾讯云
date: 2021-04-20T17:38:18+08:00
image: head.jpg
tags: [ Hugo, 'Tencent Cloud' ]
categories: [ Programming, Chinese ]
---

> 鉴于和腾讯云的纠缠应该只会发生在墙内，就不整洋文了。但这注定会是篇废话连篇的东西。

---

# 引子

原先的Wordpress已经很久都没管过了，但最近恐怕不得不管一下了。
<!-- more -->
它被托管在腾讯云双核1M小水管上若干年，打开网站需要六七秒不止的时间。你很难说bottleneck到底在哪里——带宽，性能，还是腾讯云，又或者他根本就是根管子——处处都是瓶颈。该做的优化都已经做过了，最后只能晾在那里井水不犯河水——你别再问我要续费，我也不再折腾你。每每时隔许久上来发现还真有人读完文章还评论，就不得不佩服他们的耐心。我是很难坚持对着空白的页面八秒而不想做点什么。还有人读完代码实现上来评论“这里这样改应该更快”还给了伪代码，感动得不知所措，那当年幼稚的代码前段时间我自己重构都难以卒读。

这两个月开始邮箱被报警邮件塞满：先警告我你的博客下线了，再提醒我它又回来了；一天能死去活来快十次。找不到什么快速让他闭嘴的方法，直接拉入黑名单总觉得是个dirty fix，不得已上来找解法。比较来去选择了Hugo，一个binary就可以解决的问题实在是不想整个JS全家桶。虽然说写Typescript的时候我真的很开心，但那堆复杂的依赖管理和配置每次重新捡起来都要花掉我一个下午去熟悉。以及随便一个`npm install`就塞给我成百上千个不知道是什么的包使我感到不适。

> 凡是能用JavaScript写的都将被用JavaScript重写

是的。下次一定。

# Hugo

[Hugo的文档](https://gohugo.io/about/)算是详细的，设计考虑得也很齐全，*比如至今版本号还在0.x.x徘徊*。但详细得一个Quick Start都长得让人犯困。

习惯上了解一个新东西顺着作者的思路走。先设计一个大致目录结构：
- archetypes 文章初始模板
- assets 资源
- content 内容(文章)
- data 数据
- theme 主题
- layouts 一些页面组成元素(模板)
- static 静态资源

需要描述的也就是文章内容放在content内，全站所需的一些素材放在assets，会被打包；static用于储存一些……静态资源 *(废话)*，这些静态资源一般会被外部采用，被放置在网站根目录，如favicon。

主题一般用git submodules组织会比较合理。写完东西后`hugo server`预览一下，然后可以`hugo --minify --gc --enableGitInfo`生成静态网站到public目录下。

这里忽略了很多细节，包括主题的定制，submodule的使用，文章的draft属性，git info自动补充lastmod属性等。但Google下来总是能完成的。我们更想谈谈Google不到的东西。

# 部署

之前在尝试云平台的时候就有了解到对象存储托管静态网站。个人主页也就这么托管上去了，而非由云服务器host，摆脱了1M小水管的限制。这次发现腾讯云新组织了个Cloudbase服务，把若干个服务糅合了进去。不知道出于什么样的考虑，把静态网站托管也糅合了进去，看上去还是一样托管在对象存储上。同时COS对象存储的静态网站托管功能还是留在了那。不知道这是一个服务，两个入口，还是两个独立的服务。但看到Cloudbase提供的GitHub Action所需的参数更精简，所以选择了这个新服务。COS还需要提供地域，读写模式等等参数，实在是不想再读那些文档揣摩作者的用意了，抛弃了赠送的资源包不用跑来了这个新服务。*结果告诉我哪儿哪儿的文档都一样。*

## Github Actions
这一部分还算比较清楚，但印象里以前的引导更加友善，升级成v2过后只能面对一个扑面而来的yaml配置文件尝试平复下情绪，寻找说明文档在哪儿。问题不大，别的都很好理解。关于secrets，存在这么个递进关系：
1. environment secrets
2. respository secrets
3. organization secrets

上层优先级高于下层。那么热爱least scope的我们当然会选择把secrets保存在environment里。那么就遇到了一个初始文档里没有提及的问题：environment需要和job绑定。

配置文件中jobs字段新增property:
```yaml
jobs:
  build:
    environment: your-environment-name

    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    # other configurations...
```

剩余的流程配置很清楚：
1. checkout当前分支  
需要注意的是，由于主题使用了git submodule，在checkout时需额外给予一个初始模板未提及的新参数`submodule: recursive`
2. 初始化Hugo  
使用最高star的Hugo Action即可
3. 生成静态站点  
`hugo --minify --gc --enableGitInfo`
4. 部署


## 腾讯云 Cloudbase Action
点进项目主页才发现:

> 请确保项目根目录存在 cloudbaserc.json 文件并配置好 CloudBase Framework，*参考教程*  
> 如需配置部署地域，请参考*配置字段*

*糟糕，怎么这么多参考文献又要来教我做事。* 它还要在项目根目录下有一个配置文件`cloudbaserc.json`。这一股浓浓的JS风当时就很劝退。躲了这么久没想到最后在这里还是得遇上它。

它还很骄傲地注明：
> > 如果你的项目正在使用 CloudBase Framework，那么此 Action 就是 0 配置的

我眼里就是在说：***如果你的项目没有使用 CloudBase Framework，那么此 Action 就全是配置***

但就算是这样 我也不想装个npm跑一遍这个**不支持Hugo**的框架。

去读配置的文档，看到两处完整配置的范例，其中对ENV_ID的引用都不一样，一处写成`env.envId`，另一处是`env.ENV_ID`。Action的Readme内有一处支持了`ENV_ID`的这种写法，猜测这个可能更接近正确答案。

把完整范例抄下来按需改了改，尝试运行。成功，但没有效果。观察到文档有提及配置文件中`version`字段的定义，目前应为`2.0`，但配置文件范例中没有。那么可能这是1.0的示例。再读一遍文档，发现`name`字段文档有提及但没有给出示例，范例中也没有，尝试加上。部署成功。

# 垃圾话
各个云服务商的文档实在是折磨客户。想之前CC多少个夜夜夜夜蹲在学校那个1080P屏前面睁大黑眼圈搜索、阅读那几篇使用文档，搜来搜去就是那几篇，最后变成了阅读理解，揣摩作者的意图+抠字眼希望有所发现。一圈折磨下来：
- AWS：文档多、年代久、用词含混指代不清，不同版本API杂乱
- GCP：文档较为齐全，from time to time细节缺失
- Azure：文档没啥印象，那应该不错。印象最深是event队列会丢event，这bug找得我怀疑人生
- 腾讯云：API经常有两代，文档一般覆盖第一代，第二代的是孤儿
- 字节云：文档什么的就图个乐，能找到人当然是直接飞书

Github Action有个令我难受的地方是配置文件需要在项目根目录`.github`目录下。作为一个只和Github有关的配置，我希望它能和我的项目无关。如同Github仓库的各种配置一样，既然仓库的collaborator之类信息只需要面板调整并保存在GitHub上，为什么workflow就需要保存到项目内呢。这也无法gitignore，一个GitHub专属的配置就从此成为了项目的一部分，makes me unhappy。

# 附录
完整的项目是一个公开仓库，[在这里](https://github.com/Whotakesmyname/darkray-blog)可以找到。附上workflow和腾讯云Cloudbase的配置文件。

## cloudbaserc.json
你会发现我把build的步骤放在了deploy时由cloudbase action帮忙做了。如果未来有多host部署，这一步可以拆出由单独的job/step完成。

```json
{
    "envId": "{{env.ENV_ID}}",
    "$schema": "https://framework-1258016615.tcloudbaseapp.com/schema/latest.json",
    "version": "2.0",
    "framework": {
        "name": "darkray-blog",
        "plugins": {
        "client": {
            "use": "@cloudbase/framework-plugin-website",
            "inputs": {
            "installCommand": "",
            "buildCommand": "hugo --minify --gc --enableGitInfo",
            "outputPath": "public",
            "cloudPath": "/",
            "ignore": [".git", ".github", "node_modules", "cloudbaserc.js"]
            }
        }
        }
    }
}
```

## main.yml
workflow定义文件
```yaml
name: Build & Deploy

# Controls when the action will run. 
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  deploy2tencent:
    environment: production-tencent
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - name: Checkout full code with theme
        uses: actions/checkout@v2
        with:
          submodules: recursive
          
      - name: Hugo setup
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: latest
          extended: true
          
      - name: Tencent CloudBase Github Action
        uses: TencentCloudBase/cloudbase-action@v2
        with:
          # 云开发 secretId
          secretId: ${{secrets.ID}}
          # 云开发 secretKey
          secretKey: ${{secrets.KEY}}
          # 云开发环境 Id
          envId: ${{secrets.ENV}}
```
