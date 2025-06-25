---
layout: post
title:  "近期 Android 开发遇到的三个问题：图标可见性、SideSheet 和 BottomSheet"
date:   2025-06-25 20:55:00 +0800
sitemap:
  lastmod: 
toc: true
excerpt_separator: <!--more-->
tags:
    - Android
---
看不懂的文档、缺位的官方库与魔改的第三方、不完善的民间解法和被带偏的 LLM。
<!--more-->

最近费了点力气去更新了 LiveInPeace 和 FaceMoji，记录下遇到的几个问题。

## 图标可见性

我对自己写的应用一向是没有必要不放桌面图标，能隐藏就隐藏的态度。LiveInPeace 一开始就没有图标，FaceMoji 也放了隐藏图标的选项。只不过这次为了给 LiveInPeace 一个更完善的启动流程打算把桌面图标放出来，再加一个隐藏图标的选项。
但这会我才发现隐藏图标的功能在刷了 Android 15 的 Pixel 3 上不能用（而 Color OS 13 的 realme q3 pro 上却可以，Color OS 很神奇吧），隐藏掉图标只是让点击图标跳转到系统设置里应用设置页。  

先说这里隐藏图标的实现逻辑：  
每个应用都有一个应用清单，在用户点击图标之后要跳转到的页面需要在清单里声明「我是应用入口」，系统就会在用户点击图标时将用户导入到这个页面。那么在用户选择隐藏图标的选项之后，就将这个页面禁用掉，从而试图让系统找不到应用入口从而隐藏图标。  

当然隐藏图标这个功能本来就是具有一定敏感性的，恶意应用可以用这个来隐藏自己，因此 Google 也做了限制，Android 的 `LauncherApps` 类中有 `getActivityList` 的方法，其作用是获取一个应用启动器里应该显示的所有应用图标列表，这里摘录相关的说明如下：  

> As of Android Q, at least one of the app's activities or synthesized activities appears in the returned list unless the app satisfies at least one of the following conditions:  
> 
> - The app is a system app.  
> - The app doesn't request any permissions.  
> - The app doesn't have a launcher activity that is enabled by default. A launcher activity has an intent containing the ACTION_MAIN action and the CATEGORY_LAUNCHER category.  

按照这里的要求只要应用不申请任何权限，就可以隐藏掉图标。但是从实际上的测试来看，动态禁用组件来隐藏图标是**不可行**的。只有在初始状态下不声明应用入口才能隐藏图标，但是可以在后续启用应用入口的组件来显示图标。  

因此目前的方案是：新增一个默认不显示图标的版本，虽然默认不显示图标但是可以后续进入设置显示图标。  

这是目前 FaceMoji 的状态：

<details>
<summary markdown="span"> AndroidManifest.xml </summary>

```xml
<activity
    android:name=".MainActivity"
    android:exported="true"
    android:theme="@style/Theme.EmojiFace">
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:mimeType="image/*" />
    </intent-filter>
</activity>
<activity-alias
    android:name=".MainActivityAlias"
    android:exported="true"
    android:label="@string/app_name"
    android:enabled="${mainActivityEnabled}"
    android:theme="@style/Theme.EmojiFace"
    android:targetActivity=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity-alias>
```

</details>

<details>
<summary markdown="span"> build.gradle.kts </summary>

```kotlin
flavorDimensions("icon")
productFlavors {
    create("default") {
        dimension = "icon"
        manifestPlaceholders["mainActivityEnabled"] = "true"
        buildConfigField("boolean", "ICON_ENABLED", "true")
    }
    create("icon-disabled") {
        dimension = "icon"
        manifestPlaceholders["mainActivityEnabled"] = "false"
        buildConfigField("boolean", "ICON_ENABLED", "false")
    }
}
```

</details>

## SideSheet

FaceMoji 上一个[实时预览功能](https://github.com/Steve-Mr/EmojiFace/issues/14)的 issue 让我打算把编辑 Emoji 的对话框换成 BottomSheet，与之对应的大屏布局下就是 SideSheet……吗？  

|![alt text](/assets/2025-06-24-three-problems-recent-android-dev/image.webp)|
|:--:|
|那我问你，我 Jetpack Compose 的 SideSheet 呢？|

这就是你们 Google 推荐使用的 Jetpack Compose 的大屏生态支持啊。  

但我反正狠狠 Vibe Coding 了，直接问 Gemini 方法了，答案是两条路：  

1. 用 RtL 布局的 Navigation Drawer 模拟。
2. 自己用 Surface 实现一个。

用 RtL 布局的 Navigation Drawer 来实现 SideSheet 这个路子确实不错，具体的思路直接引用 Gemini 了：  

> 1. CompositionLocalProvider(LocalLayoutDirection provides LayoutDirection.Rtl): 这是最关键的一行。它告诉它部的所有 Composable：“现在你们的布局方向是从右到左了”。ModalNavigationDrawer 检测到这个设置后，就会自动右侧滑出。
> 
> 2. 为什么要嵌套另一个 CompositionLocalProvider？
> - 当你把整个 ModalNavigationDrawer 的布局方向设为 Rtl 后，它内部的所有子组件（包括你的侧边栏内容和主屏幕内容）默认也都会变成 Rtl 布局。这可能导致你的文本和图标都靠右对齐，布局看起来很奇怪。
> - 为了解决这个问题，我们在 drawerContent 和主屏幕内容的外层，再次使用 CompositionLocalProvider(LocalLayoutDirection provides LayoutDirection.Ltr)，将布局方向“重置”回正常的从左到右。
> 
> 通过这种方式，你就可以精确地只改变 Drawer 的滑出方向，而不影响其内部内容的布局。

但是我的脑袋突然疼了起来，什么 Navigation Drawer 已经被废弃了、你是谁？请支持 Navigation Rail 之类的东西。  

虽说如此，管他呢，虽然我这次改 FaceMoji 非常的 vibe coding 了，但改现成的应该成功率总比从头来简单吧。  

实际上确实简单，直到 Color OS 给我整了活。  

|![alt text](/assets/2025-06-24-three-problems-recent-android-dev/Screenshot_2025-06-24-22-53-08-20_05bb2a206153740e9151ba3f4ad650d4.webp)|
|:--:|
|这里的空白是怎么回事呢？小编也不知道|

实际上我也确实不知道，后面折腾了一圈之后的**推测**是，Color OS 没有正确的处理 RtL 的布局，而是直接按着 Drawer 从屏幕左侧滑出的行为计算了边距，从而导致这样的情况。后面虽然进行了一堆调整但是都不太满意，索性换了第二个路子，从头来吧。  

实际上这个路子反而是意外的顺利，除了写死了宽度以及没有实现拖动关闭 SideSheet 的手势。代码就放在下面了。  

<details>
<summary markdown="span"> SurfaceSideSheet.kt </summary>

```kotlin
@Composable
fun SurfaceSideSheet(
    showSheet: Boolean,
    onDismissSheet: () -> Unit,
    isModal: Boolean = true,
    sheetContainerColor: Color = MaterialTheme.colorScheme.surface,
    sheetContent: @Composable ColumnScope.() -> Unit,
    content: @Composable () -> Unit
) {
    // 状态来驱动动画
    var animationState by remember { mutableStateOf(showSheet) }
    LaunchedEffect(showSheet) {
        animationState = showSheet
    }

    Box(modifier = Modifier.fillMaxSize()) {
        // 1. 主屏幕内容
        content()

        // 2. 遮罩层 (Scrim)
        val scrimColor by animateColorAsState(
            targetValue = if (animationState) Color.Black.copy(alpha = 0.32f) else Color.Transparent,
            animationSpec = tween(),
            label = "scrimColor"
        )

        if (scrimColor != Color.Transparent) {
            Box(
                modifier = Modifier
                    .fillMaxSize()
                    .background(scrimColor)
                    // 仅在 isModal 为 true 时，才允许通过点击关闭
                    .then(if (isModal) {
                        Modifier.pointerInput(Unit) {
                            detectTapGestures { onDismissSheet() }
                        }
                    } else {
                        Modifier
                    })
            )
        }


        // 3. 抽屉面板内容
        val density = LocalDensity.current
        val sheetWidth = 320.dp

        val offset by animateIntOffsetAsState(
            targetValue = if (animationState) {
                IntOffset.Zero
            } else {
                IntOffset(with(density) { sheetWidth.toPx() }.toInt(), 0)
            },
            animationSpec = spring(stiffness = Spring.StiffnessMedium),
            label = "sheetOffset"
        )

        Surface(
            modifier = Modifier
                .fillMaxHeight()
                .width(sheetWidth)
                .align(Alignment.CenterEnd)
                .offset { offset },
            color = sheetContainerColor
        ) {
            Column(
                modifier = Modifier.fillMaxSize()
            ) {
                sheetContent()
            }
        }
    }
}
```

</details>

## BottomSheet

Vibe Coding 的报应就来了，虽然其实我自己来也要踩到这个坑的。  

|![alt text](/assets/2025-06-24-three-problems-recent-android-dev/bottomerror.webp)|
|:--:|
|效果看图吧|

这里要实现的效果是：在用户点击 BottomSheet 以外的区域时 BottomSheet 不会自动收起，但导致的意外问题是滑动滑块会导致整个 BottomSheet 疯狂重绘以至于出现了「透明」的效果。  

Gemini 给出的方式非常符合逻辑，既然不想让他关闭，那么只要不允许其转移到关闭状态就好了：  

```kotlin
val bottomSheetState = rememberModalBottomSheetState( 
            skipPartiallyExpanded = true,
            confirmValueChange = { it != SheetValue.Hidden }
        )
```

但总之是出现了上面的问题，具体的细节原因我也没有完全理解。我后面也试着搜了一下相关的解法，发现 StackOverflow 上也是这个方法，这下 Gemini 这个回答的来源倒也知道了。  

最后的无奈解法是有些鬼畜的「没有按规定的方法关闭 BottomSheet 那么 BottomSheet 就会自己重新打开」：  

```kotlin
ModalBottomSheet(
    onDismissRequest = {
        scope.launch {
            // 重新展开，以阻止关闭
            bottomSheetState.show()
        }
    },
    sheetState = bottomSheetState,
    dragHandle = { },
    sheetGesturesEnabled = false,
    containerColor = containerColor
) { }
```



