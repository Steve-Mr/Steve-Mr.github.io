---
layout: post
title:  "Preference Datastore 与 Flow 的坑"
date:   2025-07-14 22:20:00 +0800
sitemap:
  lastmod: 
toc: true
excerpt_separator: <!--more-->
tags:
    - Android
---

观察 `DataStore<Preferences>` 对象的 `flow` 却没有 `distinctUntilChanged()` 会导致一个 preference 更新所有的 `flow` 都发通知。

<!--more-->

## 由来

最近给 Facemoji 加上了模糊的功能，但是却发现了一个问题：在 Emoji 模式下切换 Emoji 字体会导致切换前对 Emoji 做的新增和修改操作丢失。  

而排查了一圈的直接原因是：选择不同的字体时，观察 `mosaicMode`（Emoji /模糊）和 `mosaicType`（高斯模糊/像素化/半色调）的观察者被**意外**触发了。  
这个观察者预期的功能之一是用户在 Emoji 和模糊模式之间主动切换时，根据人脸识别的结果计算 Emoji 或着模糊区域应该出现的位置和大小以及方向。因此这个观察者被触发就会导致 Emoji 应该出现的位置、大小和方向重新被计算，导致修改的状态丢失。  

## 罪魁祸首

实际上的问题出在 `Datastore` 的工作机制：对任何一个键值对的修改，都会使其 `data` Flow 发射一个包含所有最新数据的全新对象。  

而我在 `PreferenceRepository` 中所有设置项的 `Flow` 都是通过同一个 `dataStore.data` 进行 `map` 生成的。这也就导致对于字体设置项的修改触发了其他项目的更新（即使这个项目没有被更新），从而出现了修改丢失的情况。  

## 解法

解决这个问题就要让设置项 A 更新时不触发其他设置项 B, C, D... 的更新，解法就是 `distinctUntilChanged()`：如果两次状态更新值相同，那么其就会丢弃掉这次更新。  

最后就是给每个 `flow` 对象加上了这个操作符：  

```kotlin
val mosaicMode: Flow<Int> = dataStore.data.map { preferences ->
    preferences[KEY_MOSAIC_MODE] ?: MOSAIC_MODE_EMOJI
}.distinctUntilChanged() // <--- 添加在这里
```