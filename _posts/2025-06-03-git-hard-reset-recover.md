---
layout: post
title:  "Git 救了这个 Blog"
date:   2025-06-03 21:30:00 +0800
toc: true
excerpt_separator: <!--more-->
tags:
    - Git
---

Git 命令千万条，安全第一条，操作不规范，Commit 全丢完。  

首先，不要犯懒；其次，不要随便 `git reset --hard` 和 `push --force`；再次，操作之前想清楚；最后 `git reflog` 救我狗命。  

**TL; DR**：`git reflog` 找到丢失的 commit 的 SHA-1，然后 `git reset <commit> --hard` 恢复原始状态。

<!--more-->

之前写过我在 `jekyll/minima` 的基础上进行[一些魔改](2024-04-24-maary-flavored-minima.md)，与之而来的问题就是与官方仓库进行同步时可能会有冲突：同一个文件的同一段内容，既被上游更改了又被我魔改了。而这次的问题就出在试图与上游同步中我的一系列弱智操作。  

## 弱智操作回顾

前面说了背景了，这里 `jekyll/minima` 就是上游(upstream)，我自己的修改作为 main。  

1. 初始状态  
    ```
    a -> b -> c -> d (upstream)
         \
          r (main)
    ```

    初始的状态很好理解，图中每一个字母是一次提交，我从 `b` 之后开始魔改，即 `r`；与此同时 `upstream` 也在进行自己的开发 `c` 和 `d`。  

2. 同步上游  
    ```
    a -> b -> c -> d (upstream)
         \        
          r (main)
          |
          r (sync)
    ```

    这是第一次同步上游的时候，从 `main` 分支分出来一个 `sync` 分支用来和 `upstream` 进行同步。  

    ```
    a -> b -> c -> d (upstream)
         \        /
          r -> s_m (sync)
                |
               s_m (main)
    ```
    在 `sync` 解决冲突问题之后（形成 `s_m` 提交），再和 `main` 合并。  

    到这里还是一切正常，那么我继续魔改就会变成下面这样，但问题的种子在这里埋下了：`sync` 分支其实应该用完就删，但我懒了一下。  

    ```
    a -> b -> c -> d -> e -> f (upstream)
         \        /
          r -> s_m (sync)
                \
                 t -> u (main)
    ```

3. 懒一下再懒一下，开始混乱  
    接下来就是这次的状况了：我又想同步上游了，从 `upstream` 向 `main` 提交了 pr，按着 Github 的流程开始解决冲突。  
    
    这时按着标准流程应该重新从 `main` 分一个 `sync` 出来，保证 `sync` 与 `main` 有相同的历史，但遗憾的是我忘记了上一次同步之后又进行了魔改，想当然的认为不需要重新把 `sync` 删了重建。  
    
    于是按着上面的流程一套下来就导致虽然 `sync` 与 `upstream` 同步上了，但是 `sync` 的历史变成了 `a -> b -> r -> s_m -> s_m2`，与 `main` 的 `a -> b -> r -> s_m -> t -> u` 也冲突了。   

    ```
    a -> b -> c -> d -> e -> f (upstream)
         \        /         /
          r -> s_m -----> s_m2 (sync)
                \          
                 t -> u  (main)
    ```

    这个时候虽然乱了一些但还没出现什么问题，我想着合都合完了再合一次也无伤大雅，于是又懒了一下在 `main` 分支处理了一次和 `sync` 的合并。  

    ```
    a -> b -> c -> d -> e -> f (upstream)
         \        /         /
          r -> s_m -----> s_m2 (sync)
                \           |
                 t -> u -> v_m (main)
    ```

    这个时候的 commit 历史就真的混乱了。  

4. 意外：合 炸 了。  
    在合并之后我测试了一下发现不能正常的编译，原因是上游在皮肤里加了新的颜色，然后我一看加了还不止一个，仔细调试有点麻烦于是打算先滚回去。  

    其实 Github 的 PR 有一个 `revert` 的功能，但是因为我在 `main` 分支处理了 `sync` 分支的合并，导致没办法直接 `revert` 了。  

5. 灵 感 突 现   
    那该怎么能 revert 呢？诶，我有一个点子☝️🤓：我们直接把 `v_m` 给 `hard reset` 掉不就行了。  

    结果就是在 `s_m` 和 `s_m2` 这两个节点之间在 `main` 分支之间的提交**全丢了**。好死不死的是我刚刚还正好 `push --force` 了，意味着远程的仓库里**也丢了**。  


## 解法

到这个情况我已经懵了，我完全不知道自己做了什么修改，甚至我已经在考虑从生成好的静态站里扒布局了。  

不过抱着死马当作活马衣的想法，还是搜了一下，结果还真的看到了[【狀況題】不小心使用 hard 模式 Reset 了某個 Commit，救得回來嗎？](https://gitbook.tw/chapters/using-git/restore-hard-reset-commit)！  

**帮大忙了。**  

先摘录一句话：   

> 首先要先建立一個觀念，不管是用什麼模式進行 Reset，Commit 就是 Commit，並不會因為你 Reset 它然後就消失了。  

具体的介绍可以查看地址内容，这里写一下我的过程：  

1. `Reflog` 找到原本的 commit 的 SHA-1。  
    Reflog 会记录 `HEAD` 的移动记录，因此在这里直接使用 `git reflog` 找到了之前 hard reset 时的移动记录。  
2. `reset` 回合并后的 commit。  
    这里就很简单：`git reset <commit> --hard`，这里的 `<commit>` 就是第一步找到的 SHA-1 记录。
3. `reset` 回合并前的 commit。
    `git log` 查看 commit 记录 SHA-1 但是这次学聪明了用了 `--soft`，虽然我还是 `git push --force` 了。