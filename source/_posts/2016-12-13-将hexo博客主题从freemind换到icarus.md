---
title: 将hexo博客主题从freemind换到icarus
tags:
  - blog
categories:
  - Others
keywords:
  - blog
  - 博客
  - hexo
  - 主题
description:
  - blog
  - 博客
  - hexo
  - 主题
toc: true
date: 2016-12-13 16:15:44
thumbnail:
banner:
---

#### 为什么换主题呢？
因为博主每次打开或者推荐给别人打开我的Hexo旧博客时，大家的第一印象就是“太工程师了”。为了改变这种印象，所以就开始着手寻找新的主题。

#### 试用新的主题
先在gihub搜索"hexo theme"，然后按Most stars排序，针对前几名的主题选了两个功能比较完善和文档写的比较详细的做了试用。
* [hexo-theme-tranquilpeak](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak)
这个是国外的人开发的，感觉很符合自己期望的风格，然后看到支持多说，就觉得非常良心了，于是就愉快开始改主题的config.xml了，修改完成看到效果之后发现跟之前的freemind主题相比好像少点什么：
"toc"目录好像没有了；不支持国内的分享插件jiathis等；搜索居然用了一个付费的服务...用到这里，我发现了，国外的还是不太合适我，所以果断放弃了。
* [hexo-theme-icarus](https://github.com/ppoffice/hexo-theme-icarus)
点进项目，看到demo的域名http://blog.zhangruipeng.me/hexo-theme ，尽管项目的README都是英文，但我知道肯定是国人开发的了。
修改配置，然后试用自己喜欢试用的toc和搜索，发现都很满意，然后就下定决心，就她了。
<!-- more -->
#### 主题配置分享
``` yml
# Menus
menu:
  主页: .
  归档: archives
  分类: categories
  标签: tags
  "|":
  网址收藏夹: favorites
  学习: study
  工具: tools
  书籍: books
  音乐: music
  影视: video
  生活: life
  关于: about

# Customize
customize:
    logo:
        enabled: true
        width: 40
        height: 40
        url: images/logo.png
    profile:
        enabled: true # Whether to show profile bar
        avatar: https://avatars1.githubusercontent.com/u/2291007?v=3&s=400
        gravatar: # Gravatar email address, if you enable Gravatar, your avatar config will be overriden
        author: Guoqing Wang
        author_title: Don’t chase success, chase excellence and success will follow
        location: Shanghai,China
        follow: https://github.com/wonderful60
    highlight: monokai
    sidebar: right # sidebar position, options: left, right
    thumbnail: true # enable posts thumbnail, options: true, false
    favicon: favicon.png # path to favicon
    social_links:
        github: http://github.com/wonderful60
        stack-overflow: http://stackoverflow.com/users/6273980/wonderful60
        weibo: http://weibo.com/wonderful60
        at: mailto:wanggq1989@gmail.com
        rss: atom.xml
    social_link_tooltip: ture # enable the social link tooltip, options: true, false

# Widgets
widgets:
    - tagcloud
    - recent_posts
    - category
    - archive
    # - tag
    - links

# Search
search:
    insight: true # you need to install `hexo-generator-json-content` before using Insight Search
    swiftype: # enter swiftype install key here
    baidu: false # you need to disable other search engines to use Baidu search, options: true, false

# Comment
comment:
    disqus: # enter disqus shortname here
    duoshuo: wonderful60 # enter duoshuo shortname here
    youyan: # enter youyan uid here

# Share
share: jiathis # options: jiathis, bdshare, addtoany, default

# Plugins
plugins:
    lightgallery: true # options: true, false
    justifiedgallery: true # options: true, false
    google_analytics: xxx # enter the tracking ID for your Google Analytics
    google_site_verification: xxx # enter Google site verification code
    baidu_analytics: xxx # enter Baidu Analytics hash key

# Miscellaneous
miscellaneous:
    open_graph: # see http://ogp.me
        fb_app_id:
        fb_admins:
        twitter_id: 
        google_plus:
    links:
        Hexo: http://hexo.io
```

#### 动手修复toc目录的显示bug
https://github.com/ppoffice/hexo-theme-icarus/pull/213

#### 为page layout关闭分享和评论
因为之前使用的freemind主题是默认对page layout关闭评论和分享的，后面想想about页面让别人有分享和评论的功能确实不太合理，所以就建议关闭了.
https://github.com/ppoffice/hexo-theme-icarus/pull/215

#### 制作晨间日记模板
https://github.com/ppoffice/hexo-theme-icarus/issues/217

#### 模仿简书增加"打赏云"
https://github.com/ppoffice/hexo-theme-icarus/issues/216
