---
layout: post
title:  "这次看齐 iOS 了。Fcitx5 for Android 简单体验"
date:   2023-05-14 09:41:44 +0800
categories: Android IME 
---
先套盾：我的体验时间并不长，有些东西可能研究不够透彻，项目也处在早期状态，我仍然是爱 Fcitx5 的。

# TL; DR
未 来 可 期。

什么情况下我推建议你使用：
1. 新就是好！有鲜为什么不尝
2. 不用开源软件我咳嗽/不应该需要联网的工具应用上传数据我就会死
3. 我非常想体验 iOS 双拼体验（开个玩笑
4. 我在用全面屏并且我想把按键放到离曲面那地方越远越好（半开玩笑

优点：
- 基于成熟可扩展的 Linux 输入法 Fcitx5 框架
- 开源且高自定义度，以至于「双曲面福音」
- 可免 Root 使用自定义词库
- 支持剪贴板
- 支持内嵌自动填充建议
  
缺点：
- 项目处在早期状态，功能不够完善
    - 剪贴板仅支持文本
    - 语音输入疑似不工作
    - 语言支持较少
    - 一些处理逻辑可能不完善
- （虽然这个对手选的不合理但对比 Gboard）功能缺失
    - **缺少双拼纠错** <- 为什么说 *「这次看齐 iOS 了」*
    - 缺少英文滑动输入
    - 快速输入没有直接搜索表情/贴图好用（当然我本来就不太懂快速输入就是）
    - 没有单手/悬浮模式
    - Material 了但不 ~~幼~~ ~~诱~~ You

# 简介

先说我怎么想起来要用 Fcitx5 For Android 的，前两天朋友发了个拼音输入法词库的仓库 [CustomPinyinDictionary](https://github.com/wuhgit/CustomPinyinDictionary)，里面提到了对于手机输入法的支持：Fcitx 5 For Android 能直接导入，Gboard 只能使用 Magisk 模块，而且用模块体验也不一定有多好，这里先放着后面再说。没 Root 的我于是对 Fcitx 5 For Android 产生兴趣了。

然后解释俩名词：
- [Fcitx 5](https://wiki.archlinuxcn.org/zh/Fcitx5)：一个输入法框架，支持中文、日文等语言，我现在电脑上就在用，包括写这篇的时候。
    - Github 仓库：https://github.com/fcitx/fcitx5
    - 项目主页：https://fcitx-im.org/wiki/Fcitx_5/zh-cn
- Fcitx 5 For Android: Fcitx5 的 Android 移植版
    - Github 仓库：https://github.com/fcitx5-android/fcitx5-android
    - 项目主页：https://fcitx5-android.github.io/
  
关于前面提的 Gboard 用模块也不好使的情况，参见这个 [issue](https://github.com/wuhgit/CustomPinyinDictionary/issues/21)。大致上是这么几点
- 当前词库的实现方式更像是「自定义短语」而非「词库」，从而可能会导致「词语和拼音被莫名分割」的问题，这个限制更多是 Gboard 实现上导致的限制。
- Gboard 的词库设置有一个 `500000` 的容量上限，只要词库达到这个上限，输入法会将其整个清空。并且如果用户无意触发 bug 也会导致词库被清空，虽然我暂时似乎没遇到但是我朋友遇到好几次了。
- 即使能绕开 Gboard 实现上的限制，导入「词库」的体验也相当不好。
# 外观
- Material 了但不 ~~幼~~ ~~诱~~ You
- 高自定义
# 功能
## 基础功能
### 英语
- 滑动输入
### 中文（双拼）
- 双拼方案支持
- 纠错
- 字符全角半角
### 日语
## 其他功能
- 剪贴板
- 语音输入
- 插件
- 内嵌自动填充