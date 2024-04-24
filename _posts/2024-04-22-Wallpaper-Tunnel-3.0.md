---
layout: post
title:  "自嗨记录之一"
date:   2024-04-23  09:00:00 +0800
toc: true
categories: Android 
---

> 造轮子，痛！  
> 自嗨，爽！  

## **写在前面**

大概是 21 号开始写这个的，本意是觉得真不容易啊可得让我水一篇。不过 22 号早上突然想起来，我最先开始写这个东西好像是四年前的四月，结果一翻仓库记录，头一条 Commit 是当年 4 月 23 号发的，还真是巧了。

![init commit](/asset/WallpaperTunnel3.0/init_commit.png)

## **什么自嗨**

最近十多天主要是针对着之前的 Wallpaper Tunnel 进行了一番爆改。事实上核心功能并没有变化，依然是简化壁纸的设置流程。之所以称为「自嗨」，除了写一个日常用户可能只有自己的 App 这件事情非常自嗨之外，塞进去各种可能自己除了测试都不会用几次的功能更是非常自嗨。  
那么说自嗨在哪之前，先看看原来 Wallpaper Tunnel 有什么问题：

1. 如果源应用不支持系统标准分享，那么整个流程都断掉。  
![傻逼张小龙](/asset/WallpaperTunnel3.0/wechat.jpg){: width="200" } 
2. 由于开发过程断断续续，代码结构和质量都很差，导致维护困难，同时权限管理一团乱麻。
3. 在第 2 点的基础上，由于 Google 做出了奇怪的权限改动，适配 Android 13 难度提高。
4. 在这个 [issue](https://github.com/Steve-Mr/WallpaperTunnel/issues/10) 提到了想直接获取系统壁纸进行编辑。我虽然做了个初步修改（显示启动器图标，然后直接显示当前系统壁纸），但是对这个方式不是很喜欢，不但加重了第 2 点的问题，而且也没办法获取锁屏的壁纸。
  
在问题之外，在找壁纸过程中我还有一些需求，主要是图片处理上遇到两个问题：第一个是分辨率可能不够；第二个是图像的比例未必适合现在这么长的屏幕，同样需要在电脑上进行一些调整。这两个问题的解决方法都是用电脑处理，前者用超分模型跑，后者用 GIMP 或者我之前找 GPT 写的[生成模糊背景脚本](https://github.com/Steve-Mr/blur_background_generator)处理。但是在电脑上处理除了直接触发上面的问题 1 让流程断掉之外，需要电脑本身就破坏了流程。
  
那么这次造的轮子就呼之欲出了。

1. 适配 Android 14。
2. 首次启动引导，权限设置放到启动引导中完成。
3. 增加启动 Activity，同时显示锁屏和桌面壁纸，并且可以直接调用 Android 新的图片选择器直接选择图像。
4. 将编辑器从 MainActivity 分离出来。
5. 编辑器增加纯色/模糊填充和超分辨率功能。
6. 壁纸历史记录入口修改：引导流程增加添加磁贴引导、长按图标 Shortcut 增加入口。
7. 顺便学点现代 Android 的东西：Preference DataStore, ViewModel 什么的。

还有一个自嗨的事情，版本号提高到 3.0 了。当然造这么多轮子的代价自然是不稳定，我已经遇到了各种各样的问题，而且接下来也会遇到各种问题。尤其是编辑器部分是重灾区，而且这部分除了 Bug 之外，数据的处理过程整体就很别扭，昨天盯了一下午也没有想出来更合适的流程，只能说留给将来的自己了。  

留给将来的自己的不止这些，虽然这次搞完我感觉一时半会没有什么需求了，但是也不是没有需求，只不过是一时半会可能完不成的东西：

1. 用点新东西。（其实也不新）比如 Jetpack Compose，比如 Jetpack navigation。
2. 本地模型抠图。
3. 生 成 式 图 像 填 充 。（看看 Blog 名字）

好，那么到这里其实主要的内容已经结束了，后面可能就属于想到哪算哪的闲聊环节了。

### **功能**

#### **首次启动引导**

老实讲这部分的布局写的比较随意，而且说明信息也不太够，在申请权限之后的一些处理上偷了非常多的懒，但是至少现在把权限这部分抽出来就达到目标了。  
另外在 Android READ_MEDIA_IAMGES 这个权限在新版本上会让用户选择是全部图片还是部分图片还是不允许，虽然我也不喜欢应用申请各种庞大权限，但是这里不拿到全部图片权限就是无法获取系统壁纸。

![引导流程](/asset/WallpaperTunnel3.0/welcome.png)

流程整体就是图上这么个样子，这部分的意义除了更清晰的显示权限，也能让我更清晰地管理权限，适配新 SDK 理论上大概也能更加直观一些，毕竟谁知道今天 Google 又会整出来什么活呢。

#### **启动 Activity**

启动 Activity 承担的两个任务：1. 从本地读图片。2. 抓桌面和锁屏壁纸。  
但是需要注意第 2 个任务没办法抓动态壁纸，Google 的 AI 壁纸也不行。（那东西看样子应该是「静态的动态壁纸」）

这里其实涉及到了前面提到的问题 1，2 和 4。 4 比较直观不提；2 的话是在于直接 Main Activity 负责的获取系统壁纸的工作分摊给了启动 Activity；1 这个还真得谢谢 Google，因为我本来想的是使用 Files 的那套选择器来选，问题很直观：图片依然在文件夹里，事实上流程并没有简化多少。而这次新的图片选择器能直接根据时间先后顺序选择图片，就让非分享的流程变成了 保存图像 -> 打开应用 -> 选择图像 -> 编辑或直接设置 的流程。

![Start Activity](/asset/WallpaperTunnel3.0/start.png){: width="500" } 

虽然你可能看不出来，但是启动 Activity 的界面直接地抄了 Google「壁纸与个性化」。最主要的是选择「锁定屏幕」和「主屏幕」那里，壁纸与个性化里设定成了 Tab，我这里主要为了展示于是直接两个都摆上来。

#### **编辑器**

编辑器是让我下了大功夫的地方，因此也会是给我造成 Bug 最多的地方，说不定也是最没用的地方。  

![Editor Fragment](/asset/WallpaperTunnel3.0/editor.png){: width="200"}

先说整体的界面，这个东西其实基本上沿袭了 History Activity 的布局。左上取消，右上确认是为了给一个明确的出口，返回当然可以用，我这里把返回和取消定义成了相同的作用：放弃一切修改回到上个界面；确认就是为了保存进行的修改。  
中间的预览切换键其实是偷懒的结果。简单来说在 Main Activity 中切换锁屏和主屏壁纸的预览是通过点击壁纸实现的（写完这句之后我又觉得只有这样不行于是加了个指示按钮）。进入编辑器之后，这里其实是在 Main Activity 上覆盖了编辑器的按钮，导致这里的点击监听器失效了，于是设置了这个预览切换键。  

下面的按键这么放置其实是因为「我觉得我能给后面再放点东西」，谁知道呢。

1. 模糊和亮度  

    ![](/asset/WallpaperTunnel3.0/blur&brightness.png){: width="500"}

    这部分功能没有改变，主要就是把之前合在一起的界面又拆开了。

2. 内容填充

    这部分的场景前面也提到了，图片的比例不够，有的图片正好是纯色背景可以直接填颜色，有的塞个模糊背景也不是不行。另外填纯色这个，也可以直接放个横屏的图片进去然后其余部分全部填成纯色来。

    2.1. 纯色填充  
        ![](/asset/WallpaperTunnel3.0/paint.png)
        
    这部分的主要问题其实就一个：颜色谁给提一下。我起初问 GPT 他告诉我 Androidx [Palette 库](https://developer.android.com/reference/kotlin/androidx/palette/graphics/Palette)能提取颜色，要了代码直接拿来用，打眼一看效果不错于是画 UI 去了。但是 UI 画完才发现仔细一看不对劲：颜色有小的色差，看来 Palette 还是提取完先自己处理之后才返回吧。不知道这个库和现在的动态取色有没有什么想法上的联系，它从 18 年 1.0 稳定版发布后就没更新过了。  
        那么接下来的问题就有点麻烦了，调库不行，那就暴力提取吧！于是我直接把图像缩放到 128*128 的大小，然后直接统计颜色取前五，显而易见的效率爆炸，所以这里的问题出来了：点击这个按钮的反应时间是最长的。  
        另外可能是没有按比例缩放的原因，有时候取出来的颜色基本上全是类似一个颜色的状态，可用性其实要打问号。  
        
    2.2. 模糊填充  
        ![](/asset/WallpaperTunnel3.0/paint_blur.png)
        这部分其实就是我上面提到仓库的 Kotlin 版本，甚至代码就是我直接拿来找 GPT 「你来给我写个一样的」。问题也和仓库里 Python 版本一样，阴影的实现太野路子了效果不是很行。
3. 超分辨率
    > AI! AI! AI!  

    别的先放在一边，AI 的概念其实是需要一些清理了。你幻觉拉满的 LLM 是 AI，那指着猫说是狗的 CNN 算 AI 吗？现在各种厂商一股脑的冲向 AI 让这水更加浑了。  
    这部分让我头疼老长时间，做这个突出一个「自嗨」：如果说上面的内容填充我大概还会用用，这个超分辨率我大概率还是直接用电脑算了。
    
    ![](/asset/WallpaperTunnel3.0/waifu2x.png)

    首先模型用的是 [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN)，其实已经有点年头了，但是挺好用的我也懒得找更新的模型了。正好前一段做别的东西摸了下 Meta 和微软搞得 ONNX 运行时，而 Read-ESRGAN 是 Pytorch 模型也有不错的兼容性，于是选了 RealESRGAN_x4plus, RealESRGAN_x2plus 和 RealESRGAN_x4plus_anime 这三个模型来用，要把模型移到手机上搞还是挺麻烦的。
  
    就结果上来说，效果还行，但是时间上非常吃亏，当然也可能是我手上的设备确实没什么正经的 NPU 支持（不过这里因为图片压缩的问题可能不是很明显）。

4. 主题  

    基于内容的动态取色太慢了……当然要处理 Bitmap 确实不容易。不过这个结论去年下的不知道现在有没有改善，这次懒得去试了，但是看了一眼 Pixel 3 上加载一张壁纸模糊老长时间，感觉还是不行。  

    这次就是用的上面提到的 Palette 库了，直接去统计占比前三的颜色当作 Primary, Secondary 和 Tertiary。当然这么操作一定会有问题，取出来的颜色未必适合当背景来用，但是我也不懂图像这些细节，于是直接找了 GPT 要提高和白色/黑色之间差异的代码。就结果来说虽然颜色不怎么好看，但是基本上不会干扰其他内容。

5. 问题

    编辑器的问题就是我不懂设计啊，处于一种各个功能互相打架的状态。  
    简单来说，编辑器要编辑什么？编辑之后应用到哪里？我在这里做了分别应用到桌面和锁屏两个选项，带来的问题是如果壁纸和锁屏的状态不一样了，后面的怎么处理。比如桌面只调整了亮度，锁屏只调整了模糊，到填充这里处理哪个？是否应该处理那个用户当前看不到的壁纸。在纠结中我暂时是决定填充的逻辑是看见哪个处理哪个，超分的逻辑也是看见哪个就处理哪个但是处理结果强制同时应用到锁屏和桌面。有没有更好的办法？或许有，但是大概要加设置项，但是怎么加设置项能把这事情足够简洁地讲清楚又是个问题。


### **实现**

这部分主要是位于功能「后面」的东西。

#### **Google! 劈柴！拿个系统壁纸吃你家大米了？**

我没想到你们 Google 拿个系统壁纸权限收这么紧：既要管理所有文件，又要读取所有图片。开发团队和 Play 审核团队关于权限打架先不提，虽然能理解「保护隐私」的考量，但是还是有点离谱了吧。
比如：
>  Important note:
>
>    - Up to Android 12, this method requires the Manifest.permission.READ_EXTERNAL_STORAGE permission.
>    - Starting in Android 13, directly accessing the wallpaper is not possible anymore, instead the default system wallpaper is returned (some versions of Android 13 may throw a SecurityException).
>    - From Android 14, this method should not be used and will always throw a SecurityException.
>    - Apps with Manifest.permission.MANAGE_EXTERNAL_STORAGE can still access the real wallpaper on all versions.
>
> Equivalent to getDrawable(int) with which=FLAG_SYSTEM.
>
> Requires Manifest.permission.MANAGE_EXTERNAL_STORAGE or android.Manifest.permission.READ_WALLPAPER_INTERNAL

这是文档里有关获取当前壁纸的说明，虽然索要的权限非常高，但是也说明了只要有 Manifest.permission.MANAGE_EXTERNAL_STORAGE 仍然能读取壁纸。**但是用起来不是这么回事。**  
  
稍微补充一点背景知识，READ_EXTERNAL_STORAGE 这个权限在 SDK 33 之后某种意义上被三个权限替代了，分别是 READ_MEDIA_IMAGES, READ_MEDIA_VIDEO 和 READ_MEDIA_AUDIO。然后在 SDK 34 上 Google 又给出了照片和视频的部分访问权限，即用户可以选取 APP 可以读取的媒体范围。  

那么反映到这里是什么样的情况呢？首先 MANAGE_EXTERNAL_STORAGE 需要搭配 READ_MEDIA_IMAGES 才能获取到系统壁纸，而且在 SDK 34 上还需要用户授予所有图片的访问权限，缺一不可。  

另外你们这权限变动啊，我现在查个权限各种 if 看哪个版本查哪个权限。

#### **SharedPreferences -> Preference Datastore**

DataStore 的官方说明：

> Store data asynchronously, consistently, and transactionally, overcoming some of the drawbacks of SharedPreferences  

虽然其实我完全没有触及到 SharedPreferences 的缺点或者限制，但是既然 Google 说推荐用这个就迁移了。  

用法并不复杂，我看了一圈跟着示例写了个 helper 类。

1. 首先是获取 datastore: `private val dataStore = context.dataStore` 就能获取 DataStore 的实例。
2. 不同的 Key 是通过 companion object 定义以实现其他位置的访问，但我基本上是直接调用的函数。
    定义方式也比较简单：比如 `val SETTINGS_HISTORY = booleanPreferencesKey("enabled_history_key")` 
3. 由于 DataStore 强调了异步，键值对的读写都是异步实现的，当然你可以直接 runBlocking 强制等待就是了。
    - 读：我在 Helper 里写了 get 函数，其实用法可能不是那么的「最佳实践」，基本上是先拿到一个 Flow 再等待结果并返回。
        ```kotlin
        private fun getFP16Flow(): Flow<Boolean> {
            return dataStore.data
                .map { preferences ->
                    preferences[FP16] ?: false
                }
        }

        fun getFP16(): Boolean = runBlocking {
            getFP16Flow().first()
        }
        ```
    - 写：写的话是 suspend 函数，异步不会阻塞其他事情挺好的。在其他 Activity 或者 Fragment 调用的时候跑个 `lifecycleScope.launch` 就好了
        ```kotlin
        suspend fun setCPUDisabled(boolean: Boolean) {
            dataStore.edit { settings ->
                settings[CPU_DISABLED] = boolean
            }
        }
        ```
    
#### **ViewModel**

**帮大忙了！**  

虽然重构后 MainActivity 从八百行到不到三百行的副作用是 ViewModel 八百行了。  

Android 开发者网站上关于 ViewModel 的介绍：

> The ViewModel class is a business logic or screen level state holder. It exposes state to the UI and encapsulates related business logic. Its principal advantage is that it caches state and persists it through configuration changes. This means that your UI doesn’t have to fetch data again when navigating between activities, or following configuration changes, such as when rotating the screen.

现在 Android 团队也希望开发者能按着 MVVM 的结构来开发，那么什么是 MVVM 呢？让我们问问 llama3:8B。

> `>>>` 介绍软件开发中的 MVVM 架构，然后以 Android 开发作为示例简要介绍
>
> 😊当然可以！下面是关于MVVM架构的中文介绍：
> 
> **什么是 MVVM？**
> 
> MVVM（Model-View-ViewModel）是一种软件架构模式，分为三个相互连接的组件：
> 
> 1. **模型**：表示应用程序的业务逻辑和数据存储。
> 2. **视图**：负责显示用户界面和处理用户输入。
> 3. **视图模型**：作为模型和视图之间的中介者，facilitates communication 和数据绑定。
> 
> **MVVM 如何工作？**
> 
> 以下是 MVVM 的高级概述：
> 
> 1. 模型包含业务逻辑和数据存储。它可以是一个数据库、Web 服务或本地存储。
> 2. 视图负责显示用户界面和处理用户输入（例如，点击、触摸）。它与视图模型通信，以获取数据或执行操作。
> 3. 视图模型收到视图的请求，并与模型交互以获取或更新数据。同时，它还 exposes 属性和命令，让视图可以绑定。
> 
> **MVVM 的优点：**
> 
> 1. **解耦合**：将关注点分离，使得单个组件更容易维护和修改。
> 2. **reactive 程序设计**：允许您轻松实现 reactive 程序设计概念（例如，观察属性变化）在您的应用程序中。
> 3. **可测试性**：使得单个组件可以单独进行-unit 测试，减少了对集成测试的需求。
> 
> **Android MVVM 示例：**
> 
> 让我们创建一个简单的 Android 应用程序，使用 MVVM 架构。我们将建立一个“待办事项”应用程序，让用户添加、编辑和删除任务。
> 1. 在您选择的 IDE（例如，Android Studio）中创建一个空白 Android 项目。
> 2. 定义 **模型** 作为一个 Java 类（`TaskStore`），负责存储和检索任务数据：
> ```java
> public class TaskStore {
>     private List<Task> tasks = new ArrayList<>();
> 
>     public void addTask(Task task) {
>         // 添加任务到存储中
>     }
> 
>     public List<Task> getTasks() {
>         return tasks;
>     }
> }
> ```
> 3. 定义 **视图** 作为一个 Android Activity（`ToDoListActivity`），负责显示用户界面：
> ```java
> public class ToDoListActivity extends AppCompatActivity {
>     private ListView listView;
> 
>     @Override
>     protected void onCreate(Bundle savedInstanceState) {
>         super.onCreate(savedInstanceState);
>         setContentView(R.layout.activity_to_do_list);
> 
>         listView = findViewById(R.id.list_view);
>     }
> }
> ```
> 4. 定义 **视图模型** 作为一个 Java 类（`ToDoListViewModel`），负责与模型和视图通信：
> ```java
> public class ToDoListViewModel extends AndroidViewModel {
>     private TaskStore taskStore;
>     private ObservableArrayList<Task> tasks;
> 
>     public ToDoListViewModel(Application application) {
>         super(application);
>         taskStore = new TaskStore();
>         tasks = new ObservableArrayList<>();
>     }
> 
>     public void loadTasks() {
>         // 从存储中加载任务
>     }
> 
>     public LiveData<List<Task>> getTasks() {
>         return tasks;
>     }
> }
> ```
> 5. 将视图模型绑定到视图使用 Android ViewModelProvider：
> ```java
> public class ToDoListActivity extends AppCompatActivity {
>     private ToDoListViewModel viewModel;
> 
>     @Override
>     protected void onCreate(Bundle savedInstanceState) {
>         super.onCreate(savedInstanceState);
>         setContentView(R.layout.activity_to_do_list);
> 
>         viewModel = ViewModelProvider(this).get(ToDoListViewModel.class);
> 
>         // 将 UI 元素绑定到视图模型的属性和命令中
>     }
> }
> ```
> 这样，我们基本上介绍了 Android 中的 MVVM 架构！通过将关注点分离、 reactive 程序设计和可测试性，您可以创建一个 maintainable、testable 和 reactive 的应用程序。🚀

在我这里的应用简单来说就是 UI 的部分只需要考虑交互内容，至于发生交互该怎么办就丢给 ViewModel。同时 UI 监听 ViewModel 中存储的数值改变 UI。  

实际上之前写 HistoryActivity 就用过 ViewModel 了，不过那会是直接抄了 Android 的 ScopedStorage 图库实现，所以其实完全不知道怎么回事。这次想要重构 MainActivity 想起来这个东西，试了一下确实好用。  

虽然开头就踩坑了：ViewModel 只支持（或者说一般方法下只支持）一个 Activity，多个 Activity 中请求 ViewModel 会得到多个实例。也就是说我将不得不去用 Fragment 了，好久没用都忘了怎么搞了。

搞清楚大体上的概念后面就很好办了，MainActivity 和 EditorFragment 共用一个 ViewModel，极大简化了 UI 部分的代码量。不过我分离的可能也不是很符合最佳实践就是，下面拿 BlurFragment 当个例子。

```kotlin
class BlurFragment : Fragment() {

    private val viewModel: WallpaperViewModel by activityViewModels()
    private var _binding: FragmentBlurBinding? = null
    private val binding get() = _binding!!
    private lateinit var onBackPressedCallback: OnBackPressedCallback

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        onBackPressedCallback = object : OnBackPressedCallback(true) {
            override fun handleOnBackPressed() {
                // 省略 设置返回动作的代码
            }
        }
        requireActivity().onBackPressedDispatcher.addCallback(this, onBackPressedCallback)

        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.primaryColorState.collect {
                    // 省略 设置主题的代码
                }
            }
        }
    }

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                              savedInstanceState: Bundle?): View {
        // Inflate the layout for this fragment
        _binding = FragmentBlurBinding.inflate(inflater, container, false)
        val sliderBlur = binding.adjustmentSlider

        sliderBlur.valueFrom = 0f
        sliderBlur.valueTo = 25f
        sliderBlur.stepSize = 1f
        sliderBlur.addOnChangeListener(
            Slider.OnChangeListener { _: Slider?, value: Float, _: Boolean ->
            viewModel.editBlur(requireActivity(), value)
        })
        return binding.root
    }

    override fun onDetach() {
        super.onDetach()
        onBackPressedCallback.remove()
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```
真正起到作用（在用户操作滑动条的时候模糊图像）的其实只有这 `viewModel.editBlur(requireActivity(), value)` 一句。上面 `onCreate()` 里负责的其实是处理返回操作和设置主题。

**但是**，讨厌的地方就是凡事都有但是。先说在前面这可能是我自己理解和用法不对，但是 StateFlow 更新的触发方式必须将新的对象赋给 StateFlow 的 value。如果你只是处理 String, Int, Boolean 那其实无所谓，横竖占不了多少内存。问题出在哪呢，我这里要处理的是 Bitmap，每次修改图像都要复制两个（桌面和锁屏） Bitmap 出来，调模糊的一个滑动过去三十来张 Bitmap 被复制出来，空间效率很难不爆炸。

#### **ONNX**

在超分辨率这个问题上，在电脑上跑都有很现成的包。但是在 Android 上又是另外一个问题了，在仓库中提到了 Android 平台的[实现](https://github.com/tumuyan/RealSR-NCNN-Android)，但是看了一眼感觉设置过程有点复杂，需要投入的精力可能超出了我的预期。但是正好看到了 ONNX 也有 Android 的支持，于是决定试试 ONNX。  

那么 ONNX 是什么呢，让我们再问问 llama3:8B。

> `>>>` 使用中文介绍 onnx  
>   
> 🤖 ONNX (Open Neural Network Exchange) 是一种开放式的神经网络模型交换格式，旨在解决不同深度学习框架之间的模型互通性问题。
> 
> **什么是 ONNX？**
> 
> ONNX 是一个独立于特定深度学习框架（如 TensorFlow、PyTorch、Caffe）的模型交换格式。它允许不同的深度学习框架之间交换和部署神经网络模型，从而实现了跨框架的互通性。
> 
> **ONNX 的特点**
> 
> 1. **开放式**：ONNX 是一个开放式的标准，旨在促进不同深度学习框架之间的互通性。
> 2. **平台无关**：ONNX 模型可以在不同的平台上运行，无需进行额外的修改或转换。
> 3. **模型可交换**：ONNX 允许不同的深度学习框架之间交换和部署神经网络模型。
> 
> **使用 ONNX 的优势**
> 
> 1. **跨框架互通性**：使用 ONNX 可以在不同的深度学习框架之间自由地交换和部署神经网络模型。
> 2. **平台无关**：ONNX 模型可以在不同的平台上运行，无需进行额外的修改或转换。
> 3. **加速部署**：使用 ONNX 可以加速部署和测试神经网络模型。
> 
> **如何使用 ONNX**
> 
> 1. **将模型转换为 ONNX**：使用 ONNX 转换工具将不同的深度学习框架中的模型转换为 ONNX 模型。
> 2. **部署 ONNX 模型**：在不同的平台上部署 ONNX 模型，无需进行额外的修改或转换。
> 
> 总的来说，ONNX 是一个具有开放式和跨框架互通性的神经网络模型交换格式，旨在促进不同深度学习框架之间的互通性。它可以加速部署和测试神经网络模型，同时也可以简化不同平台上的模型部署过程。 🚀

事实上 Real-ESRGAN 的 Pytorch 模型转 NCNN 似乎也是用的 ONNX 作为中间层进行的转换，并且仓库中直接提供了转换的[脚本](https://github.com/xinntao/Real-ESRGAN/blob/master/scripts/pytorch2onnx.py)，虽然这个脚本还是需要一定修改就是了。  

1. 模型转换  
这部分前面提到了仓库提供了转换脚本，基本上没啥问题，需要注意的是 model 的结构上 anime 模型的结构和 x4plus 是不一样的，翻了翻 inference 部分的定义找到的。  
另外我在定义 input 部分添加了 `requires_grad=True`，去掉应该也没有啥问题，可能是之前为了解决啥问题加的？  
最主要的改动是 `export` 部分添加了动态的输入和输出：  
    ```python
    with torch.no_grad():
        torch_out = torch.onnx._export(model, x, args.output, opset_version=18, dynamic_axes={
                'input': {
                    0: 'batch',
                    2: 'height',
                    3: 'width'},  # shape(1,3,640,640)
                'output': {
                    0: 'batch',
                    1: 'anchors'}  # shape(1,25200,85)
            }, export_params=True,
            input_names=['input'],  # the model's input names
            output_names=['output'])
    ```
    因为一般模型训练的时候输入的图像大小是一定的，在实际应用场景中这样并不方便。虽然我预期中其实是直接把整张图片丢进模型这么处理的，但是最后这个动态输入也用上了。
1. 预处理？  
好，完成了模型的导出，似乎接下来直接按照[官方教程](https://onnxruntime.ai/docs/tutorials/mobile/superres.html)丢进去就好啦！但并不是，直接丢进去后遇到了一个问题：模型需要浮点输入，在 Python 上 import 个包处理一下就好了，但是 Android 上该怎么弄我还真不太清楚。  
大概搜了一下发现首先是我没仔细看官方教程，在准备模型部分提到了  

    > Run the script to export the core model and add pre and post processing to it   

    而我只看到了导出没看到添加预处理和后处理的部分。
    后面翻了一下有 [KotlinDL](https://github.com/Kotlin/kotlindl) 这个库能实现与桌面上类似的处理，不过感觉好像还是直接把预处理和后处理塞到模型里更方便。  
    在添加预处理和后处理上参考了官方的[教程](https://github.com/microsoft/onnxruntime-extensions/blob/main/onnxruntime_extensions/tools/Example%20usage%20of%20the%20PrePostProcessor.md)，不过我全部使用官方提供的预处理过程，比如把 Resize 和 CenterCrop 去掉了。

    ![](/asset/WallpaperTunnel3.0/model_prepost.png)
    右边是添加预处理和后处理后的模型，能看到在 Conv 之前加了好几层。
1. 量化！  
遇事不决先量化。
首先还是找 llama3 介绍一下模型量化：

    > **什么是模型量化？**
    > 模型量化是一种技术，它将深度学习模型转换为可在低精度计算平台上运行的模型形式。这个过程称为量化（quantization），因为它将浮点数值转换为整数值或其他低比特率数据类型，从而降低模型所需的计算资源和存储空间。
    > 
    > **为什么需要模型量化？**
    > 在实际应用中，深度学习模型通常需要高性能计算设备和大量存储空间，这限制了其部署范围。模型量化可以解决这个问题，因为它可以将模型转换为可在低精度计算平台上运行的形式，从而使得模型能够部署到移动设备、嵌入式系统或边缘计算设备中。  

    为什么要量化呢，因为模型太占地方了，量化前的三个模型让应用 APK 体积直冲 200M 量化后应用体积不到 60M，对比还是相当直观的。  

    在量化上依然是使用了 ONNX 提供的[教程](https://onnxruntime.ai/docs/performance/model-optimizations/quantization.html)，而且照旧用了更简单的 Dynamic Quantization，好处是简单的同时保证了精度，坏处就是在执行时间上可能反而会变长。  
  
    不过话说回来，量化之后给我带来一个问题就是 onnx runtime 里关于 NNAPI 使用 FP16 的设置，在 Pixel 3 上没事在 Realme Q3 Pro 上就会导致出来的结果是黑的，于是加了个设置菜单塞到测试按钮的长按里了。  

#### **边到边**

反正情况也不能变得更坏了，对吧。

先说好的地方，如果现在你在 Android Studio 中按着官方新建 Activity 流程来，新建立的 Activity 会自动启用边到边，并且非常贴心的给了一个处理边距的实现。除了一个问题：这个实现其实是需要修改的。虽然我不知道他们认为的最佳实践是什么，但是现在的处理应该不会达到最「边到边」的效果。

```kotlin
ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
    val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
    v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
    insets
}
```

我们说边到边其实就是应用告诉系统：你有什么导航栏、状态栏无所谓，我要把内容显示在这些东西下面。但是这里的处理是设置了布局中的根视图的 Padding。Padding 这个可以视作视图的内边距，它改变的不是视图的位置，而是视图内容的位置。在这个情况下根视图中的所有内容与根视图的边缘保持这样的距离。感觉似乎还好？但是如果这里放个列表似乎就不太对了。  
就显示效果来说，在这里设置子视图的 Margin 才是更加合适的选项。Margin 是视图的外边距，其会影响视图的位置。  

![对比](/asset/WallpaperTunnel3.0/edgeToEdge.png){: width="500" }  

这里左边就是默认的效果，能看到不管是列表的内容还是 FAB 的阴影都被截断了；而右边是单独设置了 Fab 的 Margin 但是没有设置根视图的 Padding，于是能看到列表的内容到状态栏下面了。  

其实能理解这样一个预设的原因：不管适配不适配边到边，优先保证显示范围的安全性，不会有内容因为没有设置合适的边距导致被遮挡。  
但是至少加一个 TODO 说明吧。


## **还想造的轮子**

想造的轮子里面呢，除了学点新东西和完全没谱的生成式之外，扣图我其实真的想做。其实代码里留了个按键，虽然大概率短期内搞不定。  
![foreground icon](/asset/WallpaperTunnel3.0/foreground.png)  
因为有很多图非常好看，比例合适，但是纯白背景。如果能直接给前景抠出来，直接塞个纯色背景进去也能直接当壁纸的。但可惜的地方是，这个东西比超分辨率麻烦多了。  

前一段正好摸过 [U2-Net](https://github.com/xuebinqin/U-2-Net)，是个扣图效果还不错的模型。然后他们新搞的[DIS](https://github.com/xuebinqin/DIS)似乎有更好的效果。但是遇到了一个问题：模型这种东西，没有学特定数据集的话就很麻烦。用其他人提供的在线 Demo 随手找了几张图片试了一下，其实已经很不错了，但是真要去用是不可用的。  
![Oneline demo](/asset/WallpaperTunnel3.0/dis_demo.png)  

比如，抠 成 线 稿。  

不过写着这东西发现了个仓库 [anime-segmentation](https://github.com/SkyTNT/anime-segmentation)，真有人用动画数据集训练了，虽然还没尝试，但是这玩意说不定真能行。