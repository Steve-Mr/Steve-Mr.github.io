---      
layout: post      
title:  "使用 Git 管理 Dotfiles"      
date:   2023-01-13 08:00:00 +0800      
categories: linux dotfiles       
excerpt_separator: <!--more-->
toc: true
---      
  
本文基于 [Storing dotfiles with Git - This is the way](https://engineeringwith.kalkayan.io/series/developer-experience/storing-dotfiles-with-git-this-is-the-way/)  
<!--more-->

```bash  
git init --bare $HOME/.dotfiles  
```  
Bare 仓库需要一个工作树（work-tree）和 git 目录（git-directory）。  

```bash  
git --git-dir=$HOME/.dotfiles --work-tree=$HOME status  
```  
```--work-tree```：声明仓库需要追踪的文件地址在 ```$HOME``` 中。  
```--git-dir```：声明仓库的位置。  
为了简便可以添加别名：  
```bash  
alias dotfiles="/usr/bin/git --git-dir=$HOME/.dotfiles --work-tree=$HOME"   
  
echo "alias dotfiles='/usr/bin/git --git-dir=$HOME/.dotfiles --work-tree=$HOME'" >> $HOME/.bashrc  
# 添加到 bash 配置  
```  
# 使用 git 管理 dotfiles  
```bash  
dotfiles add ~/.config/polybar  
dotfiles commit -m "added polybar config"  
dotfiles push origin main  
```  
查看仓库文件状态  
```bash  
dotfiles status  
```  
为了不显示没有追踪的文件，使用以下命令：  
```bash  
dotfiles config --local status.showUntrackedFiles no  
```  
## 迁移到新设备  
**未验证**  
```bash  
git clone --bare https://github.com/<username>/dotfiles.git $HOME/.dotfiles && source ~/.zshrc   
```  
## 历史记录  
```bash  
# To check the version history   
dotfiles log   
  
# To checkout to last working version  
dotfiles reset <commit-hash> --hard/soft  
```  
## 关于 push 到远程仓库  
默认仓库名称可能需要更改  
```bash  
dotfiles branch -m main  
```  
添加远程仓库  
```bash  
dotfiles remote add origin https://github.com/<username>/dotfiles.git  
dotfiles branch --set-upstream-to=origin/main main  
```