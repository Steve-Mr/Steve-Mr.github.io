---
layout: post
title:  "自嗨记录之二：Oblivionis"
date:   2024-10-03  19:00:00 +0800
toc: true
categories: android 
excerpt_separator: <!--more-->
tags:
    - Android
    - App
    - Oblivionis
    - Development
image: /assets/2024-10-01-Oblivionis/image-1.webp
---

> 造轮子，爽！  
> 自嗨，爽！  

以及

> …ようこそ。Ave Mujicaの世界へ

简单来说 Slidebox 太好用了，但是在 Android 平台上确实在各个方面上都和平台不太搭。这造轮子的点不就来了。虽然现在东拼西凑搞出来了一个感觉功能上说得过去的版本，但是稳定性实在是看天。

<!--more-->

多说两句，Slidebox 在隔壁 iOS 上大概确实是优秀应用，但是在引导、控件上的 iOS 感也带到了 Android 平台上。（甚至引导界面放了 iPhone 的演示）  

| :--: | :--: | :--: | 
|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-213857.webp)|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-214555.webp)|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-214617.webp)|

而且在这里看着还正常的状态栏，其实用了「沉浸」模式，导航栏倒是没有做「边到边」的支持，于是在有的手机上就会增加一个黑条：

|:--:|:--:|
|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-21373417.webp)|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-21382826.webp)|

虽然在这里嫌弃归嫌弃，但是 Slidebox 在功能上确实没得说，虽然最近做了会员，但是限制也不算大，这方面算是良心了。

## 功能

目前实现的主要功能还是怎么删图片，Slidebox 提供的分类归档图片的功能虽然看起来很不错，但是我暂时没有这个需求，于是先放在一边了。毕竟看看标题：这（依然）是个自嗨的项目。  

|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-222647.webp)|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-222637.webp)|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-222454.webp)|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_20241002-222644.webp)|
|:--:|:--:|:--:|:--:|
|启动引导|相册列表|处理图片|应用设置|

在删除时除了使用按键/上滑删除图片之外，也提供了「反选」功能：下滑图片以「锁定」，然后长按删除键来将其他图片移动到回收站。最终的删除动作在回收站进行。至于通知功能目前做的也比较简陋：一是缺乏足够的测试，同时在第三方的 Android 系统上大概率会因为后台的额外限制出问题；二是目前只有基于时间的提醒，虽然可以选择固定日期提醒还是根据上一次整理图片的日期 + 固定间隔动态提醒，但是其实可以做一个根据图片数量/占据存储空间大小进行提醒的功能。不过目前我自己主力设备上的通知被系统干掉了，所以也没啥动力去折腾……  

那么功能介绍到这里似乎就结束了。  

## 杂谈

人呐，真的不能懒。之前写代码的时候有想过要不要边写边记录些遇到的感觉可以回头聊聊的点，但果不其然地「也没多长时间应该能回忆起来的吧」。然后现在似乎已经忘掉一大半了:-)  
 
### 名字

但是名字这个还记得。Slidebox 的名字起得很巧妙，直接突出应用的重点动作：Slide。而我一直以来的习惯其实是尽可能在名字中反映应用的功能，尽管这个「反映」并不一定能被除了我以外的其他人 Get 到。那么姑且回看一下？  

|WallpaperTunnel|某来源的图片 `-->` 应用 `-->` 系统壁纸，于是取了个管道的意思。|
|:--:|:--:|
|YetAnotherBatteryNotifier|非常直白了，就是个提醒你该拔充电器了的东西。|
|YetAnotherCalendarWidget|更直白了，就是个日历微件。|
|LiveInPeace|抽象起来了。应用的功能是显示当前的媒体音量，显示媒体音量是为了让设备在该安静的时候不会出声。因此有了下面的演变：`Quiet -> Wishing for peace -> Wishing for rest -> Rest -> RIP -> Live in peace`|  

这次依然延续了 LiveInPeace 的放飞自我的路子：  

一开始的想法大概是什么「清理工具」的思路，但是这种名字立马感觉应用体积暴增；  

接下来想到了一首挺出名的歌：[昨日重现-Yesterday Once More](https://www.youtube.com/watch?v=wawbhXQX2TQ)，这里需要插入一个想法：`即使是「没用」的图片，它们也是组成我们记忆的一部分，也代表了我们过往的一部分`，与昨日重现不同，删除这些图片代表着「昨日不再」，于是想出了 `YesterdayNoMore`；  

但是有一个问题：太长了，于是简单粗暴的办法，缩写：`YDayNoMore`；长度没问题了，又不好看了，而且 YDay 当 Yesterday 的缩写也感觉不太对劲，但一时之间也没有好想法，于是先用这个名字拿去新建项目了，名字这个事情算是暂且搁置了。  

然后新建项目的时候迷迷糊糊的给包名写成了 `com.example.ydaynomore`，甚至这还是后面再改名字的时候才发现的，不愧是我。  

再然后其实是想起来了 The Last Jedi 当年的[预告](https://www.youtube.com/watch?v=Q0CbN8sfihY)里 Kylo Ren 的台词：

> Let the past die. Kill it, if you have to.

（顺便当年明明有些有意思的人设，最后全毁于一旦真是可惜）于是想到了 `PastKiller` 这个东西。虽然简短了也能对应上功能，但是感觉没有出处的说明的情况下就显得中二了。  

那么怎么到 `Oblivionis` 的呢？还是前面提到的想法的延续：`「删除」我们的过往其实也是我们一部分记忆的消失`，但那会我其实首先想起来的是哈利波特里的「一忘皆空」咒。虽然感觉对上了，但是似乎程度又有点过了，而且我也绝对算不上哈利波特的死忠粉。就这么寻思了两天，突然想起来了：好像有哪个[蕾丝面具团体](https://zh.moegirl.org.cn/zh-hans/Ave_Mujica)的出场表演有提过「我无惧遗忘」（憋笑）。不过也是去翻了百科才发现原来就是大祥老师。那就愉快地决定了！ 

|:--:|:--:|
|![alt text](/assets/2024-10-01-Oblivionis/image.webp)|![alt text](/assets/2024-10-01-Oblivionis/image-1.webp)|

这时才发现包名写的什么玩意，连着包名和应用名字一起换了。顺便一提之前的包名开头都还是 `com.maary`，这次想起来我还有这个域名于是变成了 `top.maary.oblivionis`。

### 多翻翻文档

> 比如不要试图用 FlowColumn 造 LazyVerticalStaggeredGrid 的轮子。

一开始为了节省开发时间我其实都没打算做相册的选择，计划是编译两个 APK，一个自动获取所有图片一个只获取截图文件夹下的。于是重点就变成了怎么处理图片列表，其实也没什么可考虑的，照着 Slidebox 来好了，但是我不会写动画，于是在底下加一个按钮负责删除。到了回收站页面就更简单了，只要展示一个图片列表就好了。不过有个小问题：既要能多列显示，又要照顾到图片可能的各种分辨率。  

这么一想不就是瀑布流布局嘛，于是立马想到了 Jetpack Compose 中的 [Flow 布局](https://developer.android.com/develop/ui/compose/layouts/flow)。  

![alt text](/assets/2024-10-01-Oblivionis/flow_row_arrangement_start_default.webp){: width="100" }

这是官方的 FlowRow 的示意图，旋转了 90 度作为 FlowColumn 的示意图。粗看似乎完美符合需求，只是人家其实更多是用来给 Chip 之类的控件的，用来展示图片会有点奇怪。（具体遇到的问题我也忘了，总之其实不符合需求。）然后连着搜索挂翻文档才发现原来已经有 LazyVerticalStaggeredGrid 组件能干这个事情了。  
 
### Coil 和 Glide

好，到这里一切 OK，布局和结构基本上都有雏形了。虽然具体的维护展示图片的列表、删除的动作这些还没做，但是因为有 `WallpaperTunnel` 里的 `Wallpaper History` 功能，基本的东西都能直接复用。东西都是现成的，还能出什么问题了？  

是啊，还能出什么问题呢：Jetpack Compose 的 Image 控件不支持 uri 的显示。你这东西明明在原来的 View 体系下面是支持的啊。  

于是转向第三方库：Coil 和 Glide。Glide 是老牌图片处理库了，Coil 则是相对更加现代和 Compose 友好的库。然后各自遇到了问题：使用 Coil 在滑动图片的时候图片会上下抖动，因为之前用静态资源没出现这个问题于是转向了 Glide。结果 Glide 更加整蛊：测试删除功能的时候，将图片移动到回收站之后按说应该触发 UI 的重绘，让被删除的图片消失。但是 Glide 的重绘时间显著落后于触发时间，于是排查了半天逻辑发现是库的问题。最后回到 Coil 之后给布局加了更多限制算是正常用了。  

但是 Jetpack Compose 的坑还不止于此，它还没有视频控件，而且这个问题我到现在仍然没有妥善解决。目前的解决方法[之一](https://proandroiddev.com/learn-with-code-jetpack-compose-playing-media-part-3-3792bdfbe1ea)是以引用 AndroidView 的形式来使用官方的 `ExoPlayer`，也有进一步打包成的 [compose-video 库](https://github.com/dsa28s/compose-video)。问题在于哪里呢，至少以我目前的实现，视频播放控件的性能很差，比如从视频页返回上一个界面，视频的预览图会比其他 UI 界面滞后两三秒再消失，而且加载也缓慢，加载过程中还会有闪烁的情况。  

目前的解决方法依然是用了 Coil：仅加载缩略图，需要播放的话使用外部播放器来播放。虽然说有一定的提升，但是性能仍然很差。

### 幽默边到边适配

我不知道这个东西之前吐槽过没有，总之它值得被多次吐槽。  

首先 Android 15 已经默认给 Target SDK 35 的应用启用边到边了，那么想必现在边到边的适配已经简单多了吧。好消息：简单多了；坏消息：简单适配的结果是没有适配。为什么这么说呢，因为代码上「适配」边到边不代表设计上边到边。简便的方式能保证应用的显示内容不会和导航栏/通知栏的显示冲突，但「简便」的代价是虽然应用内容不会冲突，但也不会在导航栏/通知栏下方显示，于是视觉上仍然是没有适配的样子。  

摘录[官方文档](https://developer.android.com/develop/ui/compose/layouts/insets#compose-apis)的两句话：  

> While this ensures that interactable elements don't overlap with the system UI, it also means that none of the app will draw behind the system UI to achieve an edge-to-edge effect. To make full use of the entire window, you need to **fine-tune** where the insets are applied on a screen-by-screen or component-by-component basis.

是的，你需要 "fine-tune"。  

- **状态栏**。现在的 Android 虽然做了手势条的自动变色，但是状态栏的图标却没有。于是就会有这样几个适配方式：
    1. 最稳妥的：不在状态栏下绘制应用内容，保证状态栏内容正常显示。
    2. 状态栏是你 Google 的事情，我的应用就要边到边：在状态栏下绘制应用内容，状态栏图标看不清拉倒。

        |![alt text](/assets/2024-10-01-Oblivionis/Screenshot_2024-10-03-12-03-20-57_1df053e4b34404e2d1b64d3b51b3d08a~2.webp)|
        |:--:|
        |PixEZ 才不惯着你们 Google|

    3. 精细控制：默认状态下状态栏下使用纯色绘制保证显示效果，需要绘制如图片这样的复杂内容时切换到沉浸模式。目前见到的似乎只有下面示意图中的 MyerSplash（Android 端疑似已停更）。

        |![alt text](/assets/2024-10-01-Oblivionis/Screenshot_2024-10-03-12-03-43-88_d4248e9cb0c84ed87d7376ae8b526aac~2.webp)|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_2024-10-03-12-03-48-64_d4248e9cb0c84ed87d7376ae8b526aac~2.webp)|
        |:--:|:--:|
        |默认状态|显示图片|

    4. 取个中间值吧：在状态栏部分加遮罩，能看到应用内容，状态栏图标的显示也能照顾到，而且省事。`<--` 目前我使用的方式。

        |![alt text](/assets/2024-10-01-Oblivionis/Screenshot_2024-10-03-12-04-26-75_66dd0d8ad22b5c53ba031f445a49e062~2.webp)|
        |:--:|
        |WallpaperTunnel 里壁纸历史的布局，我还挺喜欢的|

- **列表底端**。列表是一个特殊的组件，其包含的内容往往不止于屏幕上显示的内容，我们需要的效果是列表的中间内容可以在手势条下正常显示，而列表的末端应该在内容的安全绘制区内显示，避免和手势条冲突。这时你可能会想到 padding，但遗憾的是 padding 导致的是列表完全不会在手势条下方显示。不过这里[官方文档](https://developer.android.com/develop/ui/compose/layouts/insets#inset-size)给了解决方法，非常直观好用，但是也有些搞笑：

    ```kotlin
    LazyColumn(
        Modifier.imePadding()
    ) {
        // Other content
        item {
            Spacer(
                Modifier.windowInsetsBottomHeight(
                    WindowInsets.systemBars
                )
            )
        }
    }
    ```
    什么意思呢，其在列表的元素中，增加了一个无色透明的「空白组件」，这个组件的高度正好是系统导航栏的高度。这样当你滑动列表到底部的时候，事实上列表的元素仍然在和导航栏冲突，只不过这个冲突的元素视觉上不可见而已。某种意义上正好是「我们适配了边到边，但是视觉上仍然不是边到边」的反面（笑。  

    另外补一句，在 `LazyVerticalStaggeredGrid` 中要添加这样的 `Spacer` 需要给 item 增加 `span = StaggeredGridItemSpan.FullLine` 的参数，来让 `Spacer` 占满一行从而起到效果。  

### 但还是想做点动画试试啊

1. 应用内手势

    应用内的手势主要还是处理照片这页的问题，比如 SlideBox 的上划移动到回收站。在做这个之前是滑动图片的动画，官方的 [Pager in Compose](https://developer.android.com/develop/ui/compose/layouts/pager#scroll-effects) 还给出了示例，遗憾的是示例的效果和代码中实现的效果并不相同。不过好在东拼西凑之下还有算是不错的效果。  

    至于上划手势依然有不小的问题，没有做到 SlideBox 那样的图片飞向回收站 Icon 这个先不提。目前的动画只做了向上飞出这部分，下一张图片移动到现在这个位置依然是依靠触发重绘实现的，虽然功能上 OK，但是视觉上仍然是割裂的，大概需要先在动画里把下一张挪过来然后再重绘（但是不会。  

    |<video width="300" controls> <source src="https://github.com/Steve-Mr/Steve-Mr.github.io/raw/refs/heads/main/assets/2024-10-01-Oblivionis/Record_2024-10-03-18-12-46_698ad8eb4a208d88e25d64e99370491c.mp4" type="video/mp4" title="Title"></video>|<video width="300" controls src="https://github.com/Steve-Mr/Steve-Mr.github.io/raw/refs/heads/main/assets/2024-10-01-Oblivionis/Record_2024-10-03-18-23-00_698ad8eb4a208d88e25d64e99370491c.mp4" type="video/mp4" title="Title"></video>|
    |:--:|:--:|
    |页面切换动画|页面飞出动画|

2. 预测返回

    我依然不知道是哪里的问题。印象中的预测返回只要用了标准库，一个声明就能解决问题。但目前的问题是退出应用的动画是正常的，但是应用内的预测返回动画虽然有但是只是一个渐变过渡而没有缩放，很奇怪但顾不上研究这个了:-(  

### Jetpack Compose Button 的回马枪

怎么会有人要长按按键的？  

这也是原来 View 体系下面随便写的功能，但是在 Jetpack Compose 里的 Button 只支持点击而不支持长按。恰巧我是喜欢给按键加长按的，当然这很不直观，但是点一下删一个，长按删全部还是挺符合逻辑的吧。  

在 StackOverflow 上看了不少方法，还是直接看怎么解决的吧：  

<details>
<summary markdown="span">Jetpack Compose Button 识别长按</summary>

```kotlin
    val interactionSource = remember { MutableInteractionSource() }

    val viewConfiguration = LocalViewConfiguration.current


    LaunchedEffect(interactionSource) {
        var isLongClick = false

        interactionSource.interactions.collectLatest { interaction ->
            when (interaction) {
                is PressInteraction.Press -> {
                    isLongClick = false
                    delay(viewConfiguration.longPressTimeoutMillis)
                    isLongClick = true
                    onDelButtonLongClicked()
                }

                is PressInteraction.Release -> {
                    if (isLongClick.not()) {
                        onDelButtonClicked()
                    }
                }
            }
        }
    }

    Button(
        onClick = { },
        enabled = delButtonClickable,
        shape = CircleShape,
        contentPadding = PaddingValues(0.dp),
        modifier = Modifier.size(48.dp),
        colors = ButtonDefaults.buttonColors(containerColor = BadgeDefaults.containerColor),
        elevation = ButtonDefaults.buttonElevation(10.dp),
        interactionSource = interactionSource
    ) {
        Icon(
            imageVector = Icons.Default.Close,
            contentDescription = stringResource(id = R.string.mark_this_to_delete),
        )
    }
```

</details>

### 被拦截的通知

不要靠近 Color OS，会变的不幸。  

|![alt text](/assets/2024-10-01-Oblivionis/Screenshot_2024-10-03-19-18-53-74_0ba066473b79d6e213a1f6f52505e2ee~2.webp)|
|:--:|
|看看这贴心的拦截启动，那么我通知呢？|

通知上其实有两个思路：基于时间的和基于空间的。时间的自然是以一定间隔重复提醒，空间的则是根据存储空间或者图片数量达到一定阈值再提醒。空间的应该更智能，但是现在时间的还不能在我主力设备上跑通也就没什么动力去折腾了。  

当然时间上也可以设置为按照固定日期 + 时间间隔和上次清理日期 + 时间间隔。  

具体的实现上用了 Jetpack 的 WorkManager，当年的 Android Developer Blog 是怎么介绍它的呢：  

> WorkManager is an Android library that runs **deferrable** background work when the work’s constraints are satisfied.
> 
> WorkManager is intended for tasks that require a **guarantee** that the system will run them **even if the app exits**.

但是这样的 "grarantee" 现在不适用于「某些」系统。如果说碎片化是开源的无奈，但是你的官方机制无法在启动动画标着 "Based on Android" 的手机上正常运行，还能说什么呢 Google。  

### 我不知道主界面怎么办

前面虽然说了一开始没有做文件夹的选取，但是后面写了写发现挺好加的，只是有一个问题：`TopAppBar` 上应该写什么呢？  

因为东拼西凑所以翻一些技术博客比较多，自然看到了 `Happy Coding!`。于是灵感来了，那就填进去 `Happy Deleting!`。那么问题是，中文界面怎么办。群友关于 Happy Coding 的翻译提出了「码上快乐」，精妙无比但无法照搬，但是大脑唐突拉下摇杆弹出了三个大字：消！消！乐！。好就你了 XD。

### Box 的 MatchParentSize 

帮大忙了。  

突发奇想加了个页面指示器，正好能塞个进度条进去当长按的指示器。然后就遇到问题：这个进度条 `LinearProgressIndicator` 没法限制长度。因为如果在 `Box` 里 `fillMaxSize()` 的结果会是让 `Box` 去占据所有能占据的空间。然后就是折腾经过了：  

1. 写死宽度。效果拔群！然后页面指示器里的页数太长换行布局乱掉。
2. ConstraintLayout。效果拔群！然后只看了页面指示器够长的时候的效果，页数少了效果也不对。
3. MatchParentSize。效果拔群！这是丢给 GPT 问怎么办他给我加了一个这个，虽然他给的代码不对，但是我一看这个的说明：

    > Size the element to match the size of the Box after all other content elements have been measured.
    > 
    > The element using this modifier does not take part in defining the size of the Box. Instead, it matches the size of the Box after all other children (not using matchParentSize() modifier) have been measured to obtain the Box's size. In contrast, a general-purpose Modifier. fillMaxSize modifier, which makes an element occupy all available space, will take part in defining the size of the Box. Consequently, using it for an element inside a Box will make the Box itself always fill the available space.

    太完美了。

## 其实一直自嗨也不是办法

自嗨也不是问题，问题是太小作坊了。  

现在的我早就不会什么对整出来个 App 大成功的幻想抱希望了，一方面是市场上的阈值已经非常高了；另一方面则是我的技术事实上只能说龟速提高。及时抛开架构设计、前后端这些不谈，缺乏正经的测试是个关键问题，测试全靠手动跑应用的效率太低了。可惜现在时间上不是那么自由，下次一定。  

---
UPDATE：增加了 `Box` 的 `MatchParentSize` 修饰符说明，帮大忙了！