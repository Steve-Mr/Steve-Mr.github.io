---
layout: post
title:  "我来鼓捣鼠标了"
date:   2024-03-23 21:22:44 +0800
categories: Curosr 
excerpt_separator: <!--more-->
toc: true
tags:
    - Linux
    - Cursor
    - Development
image: /asset/WcurPorter/cursor-alice.webp
---

其实是不该鼓捣的，但是我还是想奖励一下自己。  
因为最近的事情其实还没有搞完。

## 起因

很简单，搞二次元别人有鼠标主题我没有我眼红了。  

比如有人做了[优香](https://www.bilibili.com/video/BV1MH4y1a7i4/)的鼠标指针分享，有人做了 [BA 爱丽丝](https://www.bilibili.com/video/BV1994y1L7YY/)的指针分享。
本 sensei 非常喜欢，但是呢一看 Windows 的，我的呢我的呢？  
然后本社恐也不想再去评论区聊这个，再说也不能每次都找别人搞，那还能咋办呢，自己想办法丰衣足食吧。
<!--more-->
## 背景

之前我曾经魔改过[别人的一个 fuchsia 的鼠标主题](https://github.com/beeaniebee/fuchsia-cursor-outlined)（说起来也不知道 fuchsia 现在怎么样了）。如果我没记错，这位应该是在 [fuchsia 移植版](https://github.com/ful1e5/fuchsia-cursor)的基础上做了一个描边的版本，但是用了白色描边，我感觉和白色背景不太容易区分，于是做了个粉色描边，中间填充白色的[版本](https://github.com/Steve-Mr/fuchsia-cursor-outlined)(虽然是比较粗糙)。  
  
当年鼓捣这东西让我对 Linux 下的鼠标主题有了点基本的认识，而且这里也有一些可用的代码，这是我们后面要用得到的妙妙工具.jpg。

## 问题

首先声明我对不同系统鼠标主题的发展完全没有头绪，所以下面内容可能非常的无知。  

但我还是要说相比起来 Windows 的鼠标主题看着可太优雅了，Linux 这边一眼就头大。

![鼠标主题对比](/asset/WcurPorter/2024-03-23_21-53.webp)

左边是 Linux 的右边是 Windows，看见那满屏幕的软链接了吗，事实上源文件只有十几个的样子，但是最终要用各种软链接补充到图里的样子，而且还有各种纯纯数字+字母的名称，完全不知道用来干什么的。  

## 大概过程

前两天头一次萌生了这个念头，抱着一种「我肯定不是头一个想这么干」的想法搜了半天发现最好的差不多也就是 [win2xcur](https://github.com/quantum5/win2xcur)：能够转换文件格式，但是不能直接拿来用，需要根据对应的名字进行修改打包才能用在系统里，于是就是上面的问题：这么多文件我怎么对的上啊？

后面忙其他事情到今天，算是小小的有个段落（虽然还有一屁股事情只是我自己奖励自己）。  
刚开始还是打算进行一次苦战：手动把这些东西的映射关系全对上，不过还没开始动手就想起来，之前的 fuchsia-cursor-outlined 不是有从 png/svg 生成 xcursor 的代码吗？那么理论上只要我能建立 win cur 文件名———svg 文件名的对应关系，就能利用 fuchsia-cursor-outlined 的代码实现自动增加软链接的功能，这就是前面提的神奇妙妙工具了。

接下来虽然有各种各样的小毛病，但是其实还是挺一帆风顺的。有 GPT 写代码，我差不多就是一个人肉执行器把他的代码拷过来跑一下看看是不是符合我的要求。（顺带一提虽然我天天念叨被 GPT 惯的不会写代码了但是该用还用，罪过罪过。）

那么到这里基本上的形态已经有了，main.py 接收 `输入路径`，`输出路径`，`主题名称`三个参数，先从 python 调 win2xcur 将 .cur/.ani 文件转换成 xcursor，然后再通过事先定义的对应关系把这些 xcursor 的文件名称改成可以被 fuchsia-cursor-outlined 建立软链接的代码读取的名称，然后就是打包使用。

按说是这样的，直到我有换了个鼠标主题下载下来打算试一下，发现他文件名全是中文。我才如梦初醒，对啊可以用里面的 inf 文件直接统一文件明明格式啊，然后把这个补进去做预处理的步骤，整个流程算是通顺了。最后丢了个[仓库](https://github.com/Steve-Mr/WcurPorter)，希望有缘人能看到吧（

但是不是没有问题的，简单来说就是你也不知道人家做了什么哪些指针，如果没有还得自己去修改映射关系。虽说理论上可以加上各种的 fallback 办法实现完全的无人参与，但是我懒了，有不是不能用.jpg

![看看我的爱丽丝](/asset/WcurPorter/cursor-alice.webp){: width="100" }  

看看我的爱丽丝，可爱捏