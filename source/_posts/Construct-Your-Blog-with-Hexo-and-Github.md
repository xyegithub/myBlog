---
title: Construct Your Blog with Hexo and Github
date: 2021-11-24 16:20:43
hidden: true
description:  Hexo, Next等的使用和介绍。
tags: 
- Hexo
categories:
- Little Things
- Hexo
---


# 主要参考博客

https://segmentfault.com/a/1190000017986794

https://godweiyang.com/2018/04/13/hexo-blog/

https://blog.guaoxiaohei.com/posts/Hexo-Level/

https://www.itfanr.cc/2021/04/16/hexo-blog-article-encryption/

https://www.zhangxinxu.com/wordpress/2021/05/css-html-hr/

typora标题和大纲编号

https://zhuanlan.zhihu.com/p/110257979

# 实现的时候也遇到了一些问题

## 本地显示没问题，hexo d之后 在GitHub无法显示主题样式

更改主目录下面的_config.yml文件



url: github远程仓库的地址, 如 https://xyegithub.github.io/myBlog/

root: url的最后一段，如/myBlog/

更改保存之后，hexo clean; hexo g; hexo d

##  git在推送的时候很容易出现网络错误

参考博客

https://juejin.cn/post/6844904193170341896

刷新dns管理员cmd下运行 ipconfig /flushdns

但是这个方法不是很管用

### 更好的方法

可以不用代理，将hexo _config.yml里的git地址由`https://github.com/xxx`修改为ssh `git@github.com:xxx/xxx`也可以

## 将github page 设置为谷歌可搜索

参考博客

https://mizeri.github.io/2021/04/18/hexo-sitemap-google/
