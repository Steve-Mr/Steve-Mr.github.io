---
layout: post
title: "给 Jekyll 的 Toc 设置 Scrollspy"
date: 2024-12-29 19:00:00 +0800
sitemap:
  lastmod: 2024-12-30 21:20:00 +0800
toc: true 
excerpt_separator: <!--more-->
tags:
  - Jekyll
  - Minima
---

向 JavaScript 低头了，投降！  
Jekyll 虽好，但是也很「毛坯」。  
比如常见的目录随页面滚动自动高亮就缺失了，这也是坚守 Jekyll 的代价了。

<!--more-->

## 问题
### 之前的状态

之前的 Toc 是利用 [Jekyll-toc](https://github.com/toshimaru/jekyll-toc) 插件实现的，搭配 CSS 实现了常驻在页面左侧的效果。  

但是这次写 [2024 年度回顾](2024-12-31-2024-Maary-Review-Part1.md)吃了瘪：想要更清晰的分级，于是目录搞得很长，没有自动的高亮和自动随内容滚动，显得很别扭。虽然靠着分成三部分解决了问题，但是分成三部分这个本身我就不是很喜欢。  

### 想要的效果

1. 高亮显示当前段落 Toc。
2. 随着页面滚动，必要时同步滚动 Toc。

## 实现方式

Toc 可以用静态方式实现，但是要动态更换高亮项目恐怕就绕不开 JavaScript 了。不过还是有一点最后的坚持：尽可能降低禁用 JavaScript 时对页面的影响。

### 一点弯路

做了一些搜索之后将目标放在了 [scrollnav](https://github.com/jimmynotjim/scrollnav) 上。但是简单的折腾了一下之后（顺便感谢疑似感冒神志不清，一开始折腾一半就顶不住睡觉去了）发现不太符合要求：

1. 不能随着内容滚动 toc 内容
2. 只支持 h2 单一层级
3. 关了 JavaScript 就啥都没了

### 还是 AI 吧

最后看了一圈决定还是投降，找 AI 吧。你们网页技术我是真的不懂。  

总之是在先问 GPT 给了一个还不错的答案之后，重新问 Gemini 1.5 pro 再加上自己调整之后得到了下面的东西：

<iframe frameborder="0" scrolling="no" style="width:100%; height:1759px;" allow="clipboard-write" src="https://emgithub.maary.top/iframe.html?target=https%3A%2F%2Fgithub.com%2FSteve-Mr%2Fminima%2Fblob%2F7899472bce919249117c9d255066e7e2f0b1dfd6%2F_layouts%2Fpost.html%23L69-L148&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

于是效果就是如你所见这样了。  
