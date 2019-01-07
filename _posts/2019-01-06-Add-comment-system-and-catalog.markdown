---
layout:     post
title:      "在 Jekyll 博客中添加评论系统及侧边栏目录"
subtitle:   "尝试一步步美化博客"
date:       2019-01-06
author:     "Thistledown"
header-img: "img/posts/post-bg-20190106.jpg"
catalog: true
tags:
    - Blog
    - Jekyll
---
## 前言

博客基本搭建好了，不过还有不少细节需要完善一下。浏览了一些他人精致的博客网站，发现还有很多值得学习的地方。接下来就从 `评论系统` 和 博文 `侧边栏目录` 两个方面入手，进行一些美化。

## 评论系统

> 搭建参考教程：[一步一步教你在Jekyll博客添加评论系统 - 一之笔的博客](https://yizibi.github.io/2018/09/26/Mac-%E4%B8%80%E6%AD%A5%E4%B8%80%E6%AD%A5%E6%95%99%E4%BD%A0%E5%9C%A8Jekyll%E5%8D%9A%E5%AE%A2%E6%B7%BB%E5%8A%A0%E8%AF%84%E8%AE%BA%E7%B3%BB%E7%BB%9F/)  


之前在其实使用过 [Disqus](https://disqus.com/)，但是几经比较还是觉得 [Gitalk](https://gitalk.github.io/) 更加美观些，而且使用 GitHub 账号登录也比较方便，支持 `markdown` 语法这一点也深得我意 ~~（其实根本没有人会评论）~~。所以最后还是关掉了 Disqus，着手开始搭建 Gitalk 啦 ~

#### 申请 GitHub OAuth Application

在 [GitHub](https://github.com/) 中点击头像下拉菜单，选择 Settings，然后点击左侧的 `Developer settings` ，接下来点击 OAuth Apps > Register a new application，开始填写你的信息：

![](https://ws1.sinaimg.cn/large/006y42ybgy1fyy7lfbwpgj30et0bb74x.jpg)

点击 `Register application` 后，就会得到你的 `Client ID` 和 `Client Secret`。

#### 在 Jekyll 中添加 Gitalk

在你需要添加评论系统的地方，一般是 `_layout/` 目录下的 `post.html`：

在 `Post Header` 中，引入 `gitalk.css` 和 `gitalk.min.js` ：

```html
 <link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
 <script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
```

在 `Post Container` 尾部，添加容器和 JavaScript 代码：

```html
<!-- Gitalk 评论框 start -->
<div class="comment">
    <div id="gitalk-container"></div>
    <script>
        var gitalk = new Gitalk({
            clientID: 'GitHub Application Client ID',
            clientSecret: 'GitHub Application Client Secret',
            repo: 'GitHub repo',
            owner: 'GitHub repo owner',
            admin: ['GitHub repo owner and collaborators, only these guys can initialize github issues'],
            id: location.pathname,      // Ensure uniqueness and length less than 50
            distractionFreeMode: false  // Facebook-like distraction free mode
        })
        gitalk.render('gitalk-container')
    </script>
</div>
<!-- Gitalk 评论框 end -->
```

具体设置参考 [Gitalk 的中文说明](https://github.com/gitalk/gitalk/blob/master/readme-cn.md)。

保存后，提交代码就行啦。

## 侧边栏目录

这个问题有点乌龙，其实我 Fork 的 [Hux Blog](https://github.com/Huxpro/huxpro.github.io) 主题中已经自带`catalog`，我在 `post.html` 中也看到了相关的代码：

```html
<!-- Side Catalog Container -->
{% if page.catalog %}
<div class="
            col-lg-2 col-lg-offset-0
            visible-lg-block
            sidebar-container
            catalog-container">
    <div class="side-catalog">
        <hr class="hidden-sm hidden-xs">
        <h5>
            <a class="catalog-toggle" href="#">CATALOG</a>
        </h5>
        <ul class="catalog-body"></ul>
    </div>
</div>
{% endif %}
```

因此，只要在 `.md` 文件中添加一行代码即可：

```markdown
---
layout:     post
title:      "……"
subtitle:   "……"
date:       ……
author:     "Thistledown"
header-img: "……"
# 添加下面这行代码
catalog: true  
tags: ……
---
```

## 其他

这几天折腾博客也花费了不少时间，期间踩了不少坑，善用搜索引擎和 GitHub Issues 可以解决很多问题，同时也感谢一些乐于分享的博主的帮助。最后，感谢开源社区。