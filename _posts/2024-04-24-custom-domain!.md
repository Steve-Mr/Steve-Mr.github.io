---
layout: post
title:  "该自定义域名了"
date:   2024-04-24  19:00:00 +0800
toc: true
categories: jekyll 
excerpt_separator: <!--more-->
---

一时之间心血来潮，以及因为长久以来随便起的 Github 用户名导致 Github Pages 诡异地址，买了个域名然后设置过来了。  

虽然其实还好但是标准流程各种踩坑。简单来说就是，相信[官方文档](https://docs.github.com/zh/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)，但是有些细节还是得自己踩踩。  

<!--more-->

## 申请域名

最简单的一步。  

在阿里云买的，主要时间都在等认证和审核。

## 设置 Cloudflare

这里稍微有点 tricky。  

首先自然是注册验证登录一套下来，然后是一些需要注意细节的步骤。

1. Cloudflare 会让你选择套餐，对于我的需求个人的免费版就够用。然后会引导你走一系列的流程，跟着下一步基本上就问题不大。  
2. 在**域名提供商**处设置 DNS 服务器到 Cloudflare 所提供的 DNS 服务器地址。  
3. 我们在这里默认是进行 Github Pages 的配置，因此在 DNS 部分添加以下配置：  
   1. 设置根域名的 DNS。  
        这里其实按照官方的文档的配置 apex 域和配置子域来就好。  
        对于 apex 域需要指向以下的 IP 地址：  
        ```
        185.199.108.153
        185.199.109.153
        185.199.110.153
        185.199.111.153
        ```
        这里需要注意的是，在 CloudFlare 的控制台新增 DNS 记录的时候，「代理状态」默认是打开的，这里需要**关闭代理状态**，新建后的状态应该是「仅 DNS」。  
   2. 设置 www 子域。  
        这里主要是设置一个 CNAME 从 www 子域指向 github io 地址。要求同样是需要**关闭代理状态**。  
4. SSL 设置。  
    这里同样踩了个坑，需要在 SSL/TLS 中将加密模式修改为「完全」或「完全（严格）」，否则的话可能会遇到「看着全是正常但是进不去」的窘境。  

## 设置 Github Pages

这里就比较轻松愉快了，除了官方文档说明缺个词。（但是在其他部分补上了）  

同样按照官方文档，进入仓库的设置页 -> Pages -> custom domain 部分写入自己的地址，点击 save。如果前面的配置没有问题等待几分钟应该就能设置成功。  

这里官方文档提到  

> 在“自定义域”下，键入自定义域，然后单击“保存”。如果从分支发布站点，会创建一个提交，将 CNAME 直接文件添加到源分支的根目录。 如果使用自定义 GitHub Actions 工作流发布站点，则不会创建 CNAME 文件。有关发布源的详细信息，请参阅“配置 GitHub Pages 站点的发布源”。

看着很好，那么对于「使用自定义 GitHub Actions 工作流发布站点」的用户没有 CNAME 怎么办呢，我纠结了半天。然后在[排除问题的文档](https://docs.github.com/zh/pages/configuring-a-custom-domain-for-your-github-pages-site/troubleshooting-custom-domains-and-github-pages#cname-errors)中提到了：  

> 如果从自定义 GitHub Actions 工作流进行发布，则忽略且无需任何 CNAME 文件。  

你早说嘛。也就是说 CNAME 这部分其实是不用管的，不管是哪种方式都由 Github 自己解决了。


