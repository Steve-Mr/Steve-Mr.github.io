---
layout: post
title:  "该魔改这里主题了"
date:   2024-04-24  09:00:00 +0800
toc: true
categories: jekyll 
excerpt_separator: <!--more-->
---

但其实也没整多大改动，并且懒得配图了。

<!--more-->

## 为什么 Minima

如你所见，我现在在用 Github Pages 当作自己的 Blog 来用。原因不难理解：免费、省事、还有版本控制。Pages 使用 Jekyll 作为静态网站的生成器，能直接以 Markdown 生成 Html。  

使用 Jekyll 构建 Github Pages 的默认主题就是 [minima](https://github.com/jekyll/minima)，突出一个简洁到甚至有点简陋，但我还是挺喜欢的。不过因为决定真的把这里当个正经地方继续写下去，这个主题稍微有点不太够看了。

### 问题

1. 对中文的支持不好。
2. 缺少暗色模式。
3. 没有章节目录。

## 改动和坑

其实首先我还是想找找有没有合适的主题，但是大部分都有点太花里胡哨了，当然这是我的问题，我还是喜欢比较简单一点就行了。但不是没有收获，找到一个很不错的[主题](https://github.com/gledos/jekyll-minimal-theme-zh-cmn-hans)，但是没有暗色模式，而我懒得去整颜色了作罢。

### minima 3

那么现在的目标还是定到了魔改 minima 上，于是第一个坑来了。你说的 minima 是哪个 minima？  

现在官方仓库中的 minima 是 minima 3，和之前的结构有一定的差别，同时支持了自适应的主题。但是事实上 Github Pages 使用的默认主题是 minima 2.5，于是我折腾半天发现白搭，需要在 Gemfile 中指定 `gem "minima", :github => 'jekyll/minima'` 才行。不过摆弄了一会感觉自己在仓库里自定义有点难受，干脆 [fork 了一份 minima](https://github.com/Steve-Mr/minima) 自己来改。

#### 自适应主题

这部分起始在项目的 `_config.yml` 中设置 minima: skin: auto 就好了，也有 solarized 版本但是我一直对于那个配色不是很感冒。  

| ![](/asset/minima/auto.png){:width=500} |
| :-------------------------------------: |
|            *亮色和暗色效果*             |

#### 摘要

这部分其实也只需要在 `_config.yml` 中声明 `show_excerpts: true` 就好了。  

当然为了更精确的控制可以在 md 文件头中设置 `excerpt_separator: <!--more-->`，在文章中插入 `<!--more-->` 的位置之前的内容就是摘要内容了。


#### 字体

字体上其实现在也没有搞好，以及做了一些可能有点奇怪的决定。  

就是我现在突然感觉衬线也挺好并且设置成了正文字体，正文使用了 Noto Serif 标题使用 Noto Sans。  

主要的问题就是我不知道为什么 jekyll 生成的 html 中会把英文的撇号变成中文的撇号然后就爆炸了，不过我可能用不上？先这么放着了。

#### 章节目录

这部分是有点麻烦的，但是在后面才麻烦。  

这里使用了 [jekyll-toc](https://github.com/toshimaru/jekyll-toc) 插件，使用方法见 Readme 说的已经比较清楚了：在 Gemfile 中添加 `gem 'jellky-toc'` 然后 `bundle install`；接下来在 `_config.yml` 中的 `plugins:` 添加 `- jekyll-toc`；最后在 md 文件头添加 `toc:true`。  

但这是只是 1/3 的工作，还需要在页面中定义 toc 的布局，html 部分比较简单放在那里就行了。

{% raw %}
```html
<div class="toc">
  {{ content | toc_only }}
</div>
```
{% endraw %}

然后定义 toc 的位置，我习惯让它在左边。

```css
#toc {
  position: fixed;
  left: 20px; /* 调整左边距 */
  top: 50px; /* 调整上边距 */
  max-width: 300px; /* 设置最大宽度 */
  max-height: calc(100vh - 100px); /* 调整最大高度 */
  overflow-y: auto; /* 添加滚动条 */
  white-space: normal; /* 自动换行 */
}

/* 可以根据需要修改样式 */
#toc ul {
  list-style-type: none;
  padding: 0;
}

#toc ul ul {
  margin-left: 20px;
}

#toc a {
  text-decoration: none;
}

#toc a:hover {
  text-decoration: underline;
}
```

接下来还有一个问题：手机上怎么办？于是需要根据宽度隐藏 toc。

```css
@media screen and (max-width: $on-palm), (max-width: 800px) {
  .toc {
    display: none;
  }
}
```

好到这里 toc 的 2/3 的工作做完了。

### 本地完全 OK 了就 OK 了……吧？

然后惯例在本地跑一下，看着能解决的问题都解决了，这下只要推送就好了吧。  

但可惜不是，登上去一看：不对，怎么没正确生成 toc？然后一搜发现没有默认 toc 支持的原因了：  

> GitHub Pages cannot build sites using unsupported plugins. If you want to use unsupported plugins, generate your site locally and then push your site's static files to GitHub.  

好嘛，jekyll-toc 没在支持范围内。解决方法倒也简单，在仓库设置里把 Pages 的编译工具换成 Github Actions 就好了，jekyll 也提供了[教程](https://jekyllrb.com/docs/continuous-integration/github-actions/)。  

到这里算是差不多终于把 toc 搞完了，那么这次主题的折腾也就差不多这样了。
