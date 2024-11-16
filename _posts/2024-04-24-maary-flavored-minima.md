---
layout: post
title:  "该魔改这里主题了"
date:   2024-04-24  09:00:00 +0800
modified_date:   2024-11-16 10:00:00 +0800
toc: true
categories: jekyll 
excerpt_separator: <!--more-->
tags:
  - Jekyll
  - Minima
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

---
## 2024-04-25 更新  

- 使用 [giscus](https://github.com/giscus/giscus) 增加了评论系统，这个东西好处在于它把 Github 仓库的 Discussion 功能映射到了 Pages 的评论区。虽说其实没啥用就是了。  
- 为了上面这点，增加了 `_include/custom-post.html` 用来增加自定义的评论区。
- 把 RSS 订阅的样式和 Social links 的样式统一了。  
- 找 Gemini 要了 favicon，效果还可以并且用上了。  

  | ![](/asset/minima/fav_icons.png) |
  | :-------------------------------------: |
  |            *Gemini 的结果中我比较喜欢的三个*             |

- 增加了主页的分页，Archive 的懒得搞了。

## 2024-04-29 更新
- 试图转移到使用仓库本地字体？但是不确定会不会成功或者效果变好。

---
## 2024-11-10 更新
最近又折腾了点东西，随便补充一下：

- Sitemap, robots.txt, seo 和 Copyright

> 所以 Google 你为什么不索引！

简单来说以上东西都是为了让 Google 能想起来索引一下我这个站。但是遗憾的是至今只有 https://maary.top 被索引了。  

  - Sitemap: 本来是用 `jekyll-sitemap` 来实现的，但是后面为了更丰富的内容，换了一个[实现](https://github.com/Steve-Mr/Steve-Mr.github.io/blob/main/sitemap.xml)。
  - robots.txt: 这个更简单了，我已经完全是 Google 你他妈快点来抓取的状态了所以啥都没限制。
  ```liquid
  ---
  layout: none
  ---
  User-agent: *
  Allow: /
  Sitemap: {{ "{{" }} site.url }}/sitemap.xml
  ```

  - seo: 这个同样用了最省事的 jekyll-seo-tag 插件。
  - Copyright: 在 `footer.html` 中增加了一行 `<p>{{ "{{" }} site.author.copyright-year | escape }}-{{ "{{" }} site.time | date: '%Y' }} &#169; {{ "{{" }} site.author.name | escape }} </p>`

- 地址

  这个主要是原来的地址过于繁杂，基本上是 /分类/年/月/日 的状况，于是在 `_config.yml` 中设置了 `permalink:/blog/:year/:title:ouput_ext`，现在的效果直接看地址栏就好了。

- Tags
  Jekyll 和 liquid 语法本身就支持 Tags，这里主要的任务是展示 Tags 以及按照 Tags 索引。  
  - 展示：在文章中要显示本篇文章的 Tags，在 Archive 中要显示当前所有的 Tags。  
    对于在文章中的 Tags 使用一个遍历即可：  
    ```liquid
    <br>
    {{ "{%- if page.tags" }} -%}
        {{ "{% for tag in page.tags" }} %}
            <a href="{{ "{{" }} site.baseurl}}/archive.html#{{ "{{" }}tag | slugize}}">
                #{{  "{{" }} tag }}
            </a>
        {{ "{% endfor " }}%}
    {{ "{%- endif" }}-%}
    ```  
    
    在 Archive 中的 Tags 也是类似的操作：  

    ```html
    <div id="tags">
    {{ "{% assign sorted_tags = site.tags | sort"}} %}
          <p>
          {{ "{% for tag in sorted_tags" }} %}
              <a class="post-tag" href="{{ "{{ site.baseurl "}}"}}/tag/{{ "{{ tag[0] | slugify"}} }}">{{ "{{ tag[0]"}} }}</a>
          {{ "{% endfor" }} %}
          </p>
    </div>
    ```  
    
    接下来还需要定义 Tags 的样式：
    ```sass
    .post-tag {
      display: inline-block;
      background: $code-background-color;
      padding: 0 .5rem;
      margin-right: .5rem;
      margin-bottom: .5rem;
      border-radius: 4px;
      color: $text-color;
      font-size: 90%;
      &:before {
        content: "#";
      }
      &:hover {
        text-decoration: none;
        background: $brand-color;
        color: $background-color;
      }
    }
    ```
          
  - 按 Tags 索引：这里直接使用了 `jekyll-tagging` 插件来实现基于 tag 的索引页面的生成，具体的使用方法参加项目 Readme。  
  Tag 索引页的布局其实就是按 Tag 筛选过的 Archive 页。

- 折腾 post 布局
  折腾这东西费了不少劲，而且有些 dirty。主要目的是实现在页面不宽不窄的时候页面的目录不会和内容冲突，顺便一个顺带的效果是在手机排版下也有目录了。
  1. 首先是给 post.html 的模板去掉了 wrapper，避免页面内容被强制居中，对于像标题、评论的东西，可以在对应元素外面套上 wrapper 格式。
  2. 定义了 `post-wrapper` 的样式用来显示 toc 和 文章内容，大致思路是使用 flexbox 布局来展示这两个部分。
  ```sass
  .post-wrapper {
    display: flex; /* 使用 Flexbox 布局 */
    gap: 20px; /* 设置子元素之间的间距 */
    $first-calculation: calc((100vw - #{$content-width} + #{$spacing-unit}) / 2);
    $second-calculation: calc($first-calculation - 300px);
    margin-right: $first-calculation;
    margin-left: $second-calculation;
    @media screen and (min-width: $on-large) {
      $first-calculation: calc((100vw - #{$content-width} + #{$spacing-unit * 2}) / 2);
      $second-calculation: calc($first-calculation - 300px);
      margin-right: $first-calculation;
      margin-left: $second-calculation;
    }
    @media screen and (max-width: $on-large) {
      flex-direction: column; /* 当屏幕宽度较小时，变为垂直布局 */
    }
  }
  ```
  这里也就是 dirty 的部分了，一些宽度的计算用了硬编码的值，目前似乎没啥问题但是毕竟测试的平台不够多，谁知道会出来什么问题呢🤷‍♂️