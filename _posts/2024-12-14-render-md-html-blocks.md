---
layout: post
title: "让 Jekyll 在 HTML 块中渲染 Markdown"
date: 2024-12-14 18:00:00 +0800
sitemap:
  lastmod:
toc: true 
excerpt_separator: <!--more-->
tags:
  - Jekyll
---

## TL; DR

1. 在 `_config.yml` 中增加如下内容：
    ```yaml
    kramdown:
      parse_block_html: true
    ```
2. `<details>` 实例：
    <details>
    <summary markdown="span"> 这是一个测试 </summary>
    <!-- 注意这里的 markdown="span" 是必须的 -->

    以**观察** `Mardown` 是否能 _正常渲染_。

    </details>
    ```html
    <details>
    <summary markdown="span"> 这是一个测试 </summary>
    <!-- 注意这里的 markdown="span" 是必须的 -->

    以**观察** `Mardown` 是否能 _正常渲染_。

    </details>
    ```

<!--more-->

## 问题：Github Repo 和 Pages 的行为不一致

众所周知 Markdown 作为一个轻量级标记语言和 HTML 有着非常密切的联系，比如在 Github Repo 的 Readme 和 Github Pages 中就可以使用 Markdown 进行写作，同时在 Markdown 中也可以直接嵌入 HTML 元素。  

比如在写作中我们会遇到的场景：有一些内容比如细节的说明或者涉及内容剧透，虽然必要但是过于占据篇幅/不适合直接让读者看到，可以考虑将其「折叠」起来。在这方面 Github 文档中[使用折叠部分组织信息](https://docs.github.com/zh/get-started/writing-on-github/working-with-advanced-formatting/organizing-information-with-collapsed-sections)部分给出了如下实例：

> ```html
>     <details>
> 
>     <summary>Tips for collapsed sections</summary>
> 
>     ### You can add a header
> 
>     You can add text within a collapsed section. 
> 
>     You can add an image or a code block, too.
> 
>     ```ruby
>     puts "Hello World"
>     ```
> 
>     </details>
> 
> ```

但是在 Github Pages 中如果直接使用上面的内容则会出现下面的结果，你可以点开看一下：  

<details markdown="0">

<summary>Tips for collapsed sections</summary>

### You can add a header

You can add text within a collapsed section. 

You can add an image or a code block, too.

```ruby
   puts "Hello World"
```

</details>

造成这种区别的原因其实在于 Github Pages 和 Github Repo 中使用的渲染引擎并不一致，Github Pages 使用的是 Jekyll 来生成静态网站，使用 Kramdown 去渲染 Markdown；而 Github Repo 里则是用 Github 自己的 [libcmark-gfm](https://github.com/github/cmark) 去渲染。二者除了在这里还会有很多细节差异，后面也会提到另外的一点。

## 两种解决思路

知道了问题在哪，就有了两种思路：
1. 让 Kramdown 去模拟 Github 的风格
2. 更换 Github Pages 的渲染引擎

两种思路都是可行的，但是就我自己的使用情况来说都有一些小问题，下面进行简单介绍。

### 启用 Kramdown 的 HTML 块内 Markdown 渲染

其实很简单，在 `_config.yml` 中增加如下内容  

```yaml
kramdown:
  parse_block_html: true
```

但是问题来了，直接粘贴上面 Github 文档示例会变成下面的样子：

---
<details>

<summary>Tips for collapsed sections</summary>

<div class="no_toc_section">
### You can add a header
</div>

You can add text within a collapsed section. 

You can add an image or a code block, too.

```ruby
   puts "Hello World"
```

</details>

---

在 Kramdown 的 issue 中我翻到了这样的[解决方案](https://github.com/gettalong/kramdown/issues/155#issuecomment-339793629)：在 `<summary>` tag 中增加 `markdown="span"`。效果如下：

<details>

<summary markdown="span" class="no_toc_section">Tips for collapsed sections</summary>


<div class="no_toc_section">
### You can add a header
</div>

You can add text within a collapsed section. 

You can add an image or a code block, too.

```ruby
   puts "Hello World"
```

</details>

如你所见，变成我们想要的效果了，可惜我们需要把之前所有的 `<summary>` tag 替换为 `<summary markdown="span">`。

### 迁移到 jekyll-commonmark-ghpages

[jekyll-commonmark-ghpages](https://github.com/github/jekyll-commonmark-ghpages) 是 Github 给 Github Pages 准备的渲染引擎，完整兼容 Github 自家的 GFM 规范，自然也就与上面文档里的行为一致。  

使用方法参考仓库 Readme 的 Installation 部分，这个效果是最好的，也不需要修改任何东西。但是我却遇到了一个意外的情况：GFM 和 Kramdown 的语法并不完全一致，比如一个重点区别：Kramdown 允许表格的扩展语法中没有表头行，而 GFM 并不允许。我还挺喜欢这个特性，于是选择了修改 Kramdown 的思路。
