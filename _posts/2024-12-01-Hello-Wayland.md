---
layout: post
title: "Hello, Sway"
date: 2024-12-03 22:00:00 +0800
sitemap:
  lastmod: 2024-12-05 21:30:00 +0800
toc: true 
excerpt_separator: <!--more-->
tags:
  - Linux
  - ArchLinux
  - Sway
  - Wayland
---
我！终于！迁移到 Wayland 了！  
*虽然我现在已经不知道为啥要迁移了。

|![alt text](/assets/2024-12-01-Hello-Wayland/image.webp)|
| :--: |
|现在是 WM: Sway (Wayland) 了！|

<!--more-->

## Wayland 

Wayland 是一个旨在替代 X 窗口系统的显示服务器协议。虽然 Wayland 到目前为止仍然面临各种各样的问题，尤其是软件适配的兼容性问题，但是马上 2025 年了，Wayland 已经完全开箱即用了，比如一众发行版已经切换到了默认 Wayland。  

至于 Wayland 到底好在哪里，直接问 Gemini 了：

> Wayland 作为 X11 的替代品，优势主要体现在以下几个方面：
> 
>   * **性能**: 更低的延迟和更高的帧率，带来更流畅的体验，尤其是在游戏等场景下。
>   * **安全**:  更强的隔离性和更简洁的架构，降低了安全风险。
>   * **用户体验**: 更平滑的窗口管理和多点触控支持，带来更现代化的交互体验。
>   * **架构**:  更简洁的代码库和模块化设计，更易于维护和扩展。
> 
> 总而言之，Wayland 更快、更安全、更现代化，代表了 Linux 图形显示的未来方向。

### 迁移到 Wayland 的困难

困难不在于软件的兼容性，而是经典的 Nvidia 专有驱动作妖。这里也是太老生常谈了我们直接问 Gemini：

> 好的，以下是关于 NVIDIA 专有驱动程序与 Wayland 兼容性问题的简短说明：
> 
> **问题:**
> 
> * Wayland 和 NVIDIA 驱动程序的架构差异导致兼容性挑战。
> * NVIDIA 驱动程序需要深度内核交互，与 Wayland 的安全模型冲突。
> * 对 XWayland 的依赖可能导致性能下降。
> * 部分 NVIDIA 特性 (如 G-SYNC) 在 Wayland 下支持不完善。
> 
> **现状:**
> 
> * NVIDIA 正积极改进驱动程序，兼容性逐步提升。
> * 已支持 DMA-BUF 和 GBM 等 Wayland 核心技术。
> * Ubuntu 24.10 默认启用 Wayland for NVIDIA 用户，标志着重大进展。
> * 仍存在内存泄漏、程序启动问题和 XWayland 性能等问题。
> 
> **未来:**
> 
> * NVIDIA 持续改进驱动程序，社区合作推动兼容性完善。
> * Wayland 的显式同步模型有望解决更多兼容性问题。
> 
> 
> 总而言之，Wayland 与 NVIDIA 驱动程序的兼容性正在稳步改善，但仍有挑战需要克服。 相信随着双方共同努力，Wayland 最终将为所有 Linux 用户提供流畅的体验。

而我好死不死的又在用 3050，而且是比较复杂的笔记本上集显和独显共存的情况。不过事实上 Gnome 已经能够在 Wayland 上正确处理这样的情况，因此我多了一些信心。  

## Wayland Compositor

但我其实不是很懂 Wayland Compositor 和 X11 下的 Window Manager 的区别，这里也不辨析了。总之习惯了在 X11 下使用 i3 之后我就将目标转向了这些能够当 wm 用的 compositor。

1. Sway

    > Sway is a tiling Wayland compositor and a drop-in replacement for the i3 window manager for X11. It works with your existing i3 configuration and supports most of i3's features, plus a few extras. 

    可以认为 Sway 是 Wayland 上的 i3。（当然仍然是有差别的）于是毫无疑问这就是最适合我的 Compositor……吗？  
    问题就出在 Nvidia 上，这么说吧，使用 Nvidia 专有驱动跑 Sway 一度需要加上 `--my-next-gpu-wont-be-nvidia` 的参数，当然现在变成 `--unsupported-gpu` 了。  

    但是我依然遇到严重问题：外接屏幕各种闪烁/撕裂。尝试了各种环境参数最后的结果是笔记本屏幕和外接屏幕只能用一个，无奈尝试其他选项。

2. Hyprland

    救星来了！  
    如果我没记错，Hyprland 的开发者刚开始搞这个项目的时候还是个初中生来着，这么一比自愧不如。（当然也有一些 Hyprland 的相关争议似乎和开发者相关，这里不提了。）  

    好消息是 Hyprland 开箱即用，根本没有那么多有的没的。于是我一转学写 Hyprland 配置，但是大概配了一套框架出来之后发现了问题：Hyprland 的 scratchpad 功能的实现是有问题的。

### Scratchpad

我也说不太清楚 Scratchpad 到底是个啥，那么还是老方法，直接问 Gemini:  

> i3wm 的 scratchpad 就像一个**隐藏的口袋**，可以存放暂时不用的窗口。
> 
> **用途:**
> 
> 当你需要专注工作，又不想彻底关闭一些辅助窗口 (例如邮件、聊天、音乐等) 时，就可以把它们“塞进” scratchpad。需要时再快速调出来，用完放回去，非常方便。
> 
> **特点:**
> 
> * 隐藏窗口，节省屏幕空间
> * 快捷键快速访问
> * 存放临时使用的窗口
> 
> **使用方法:**
> 
> `$mod+Shift+minus`  将当前窗口移入/移出 scratchpad。
> 
> scratchpad 可以帮你更好地管理窗口，提高工作效率。 如果你正在使用 i3wm，强烈建议你试试这个功能！

不得不说 Gemini 这个解释确实是我的用法，当然具体来说还需要搭配 `rofi`:  

打开需要使用但是不需要一直在屏幕上的程序（比如即时通讯软件）  
`--暂时使用完毕-->` 丢到 scratchpad  
`--需要再次使用时-->` 使用 rofi 唤起  

在这里由于程序被丢到 scratchpad 之后会自动变成浮动状态，那么再使用 rofi 唤起会直接将程序从 scratchpad 移动到当前的工作空间，某种程度上算是 i3wm 下的一个「最小化」实现方式了。  

那么 Hyprland 当前的实现方式其实没啥大问题：将 scratchpad 当作一个特殊的通常不可见的 workspace。  
但问题出在整个流程中的窗口处理规则：  
被丢进 scratchpad 的窗口不会也不被允许变成浮动窗口，而且再次拉出来 scratchpad 更类似于一个浮动的 workspace。虽然想了一些方法，但是感觉效果都不好说，于是这个思路就暂时搁置了。


## 配置

那么最终的解决办法反而异常简单：我安装了 wlroots-git 和 sway-git 然后一切正常了，我前面都折腾了啥哦。要感谢我之前[放弃了 Ubuntu 转向 ArchLinux](2024-06-16-goodbye-ubuntu.md)。  
那么接下来就是写配置了，写死我了快。  

### Sway

从 i3 转向 Sway 的迁移其实很早就开始了，但是一直到现在才算正经完成。之前是直接上了 Python 脚本，理所当然的是找 ChatGPT 要的。

<details>
<summary markdown="span"><b>i3toSway.py</b></summary>

```python
    import re
    import shutil

    def process_i3wm_config(file_path):
        # 1. 复制一份 config 文件，保存为 .bak 文件
        backup_path = file_path + '.bak'
        shutil.copyfile(file_path, backup_path)
        
        # 2. 读取文件内容
        with open(file_path, 'r') as file:
            lines = file.readlines()
        
        # 3. 查找 set_from_resource 语句，记录 <name> 和 <fallback>
        set_from_resource_pattern = re.compile(r'set_from_resource\s+(\S+)\s+(\S+)\s+(\S+)')
        substitutions = {}
        
        new_lines = []
        for line in lines:
            match = set_from_resource_pattern.match(line.strip())
            if match:
                name, resource_name, fallback = match.groups()
                print('found: ' + name + ' ' + fallback)
                substitutions[name] = fallback
            else:
                new_lines.append(line)
        
        # 4. 替换所有 $<name> 为 <fallback>
        def replace_names(line):
            for name, fallback in substitutions.items():
                line = re.sub(rf'\{name}', fallback, line)
            return line
        
        new_lines = [replace_names(line) for line in new_lines]
        
        # 5. 保存修改后的文件
        with open(file_path, 'w') as file:
            file.writelines(new_lines)

    def process_i3wm_config_set(file_path):
        # 1. 复制一份 config 文件，保存为 .bak 文件
        backup_path = file_path + '.bak'
        shutil.copyfile(file_path, backup_path)
        
        # 2. 读取文件内容
        with open(file_path, 'r') as file:
            lines = file.readlines()
        
        # 3. 查找 set_from_resource 语句，记录 <name> 和 <fallback>
        set_from_resource_pattern = re.compile(r'set\s+(\S+)\s+(\S+)')
        substitutions = {}
        
        new_lines = []
        for line in lines:
            match = set_from_resource_pattern.match(line.strip())
            if match:
                name, fallback = match.groups()
                print('found: ' + name + ' ' + fallback)
                substitutions[name] = fallback
            else:
                new_lines.append(line)
        
        # 4. 替换所有 $<name> 为 <fallback>
        def replace_names(line):
            for name, fallback in substitutions.items():
                line = re.sub(rf'\{name}', fallback, line)
            return line
        
        new_lines = [replace_names(line) for line in new_lines]
        
        # 5. 保存修改后的文件
        with open(file_path, 'w') as file:
            file.writelines(new_lines)

    # 使用示例
    # process_i3wm_config('/home/maary/.config/sway/config')
    process_i3wm_config_set('/home/maary/.config/sway/config')

```

</details>

但是转换没有这么简单，仍然有些地方需要自己调整。  

1. Wayland 没有「默认屏幕」的概念，于是为了让 workspace 1 默认在笔记本屏幕上只能手动绑定。
    ```yaml
    workspace 1 output eDP-1
    workspace 2 output HDMI-A-1
    ```
2. Polybar 不支持 Wayland，需要换其他的比如 Waybar。
3. 手动设置屏幕位置。
  ```yaml
  output eDP-1 pos 0 0 
  output HDMI-A-1 pos 1920 0
  ```
1. 对于浮动窗口的规则，因为 Wayland 和 X11 在窗口属性之间的区别，基本上来说要从原来的 `class` 关键词换成 `app_id` 关键词，不过对于运行在 `Xwayland` 下的程序不需要更改。
2. Sway 不再需要 picom 之类的工具，而是内置了类似的功能。之前使用的 picom 设置非焦点窗口自动半透明就得在 Sway 里设置了，在 [sway-contrib](https://github.com/OctopusET/sway-contrib) 仓库里有可用的工具：`exec /usr/share/sway-contrib/inactive-windows-transparency.py --opacity 0.85`  

    ---
    2024/12/05 更新：sway-contrib 已经不太够了，原因在于所有的非焦点窗口都会被设置半透明——于是 PiP 和在播视频的网页遭了殃。  
    解法也简单：设置规则，遇到符合规则的窗口就设置其透明度 100%。`inactive-windows-transparency.py` 的代码足够简洁改起来也很方便，这里是加入了下面的函数来实现的：  

    <iframe frameborder="0" scrolling="no" style="width:100%; height:415px;" allow="clipboard-write" src="https://emgithub.maary.top/iframe.html?target=https%3A%2F%2Fgithub.com%2FSteve-Mr%2Fdotfiles%2Fblob%2F24ae343cb2ede2f4ee06a7a2255596326b514d40%2F.config%2Fsway%2Fscripts%2Finactive-windows-opacity.py%23L15-L30&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

    剩下的就是调整逻辑了，这部分就更简单了，在之前所有直接调整透明度的位置都塞一个规则判断：  

    <iframe frameborder="0" scrolling="no" style="width:100%; height:163px;" allow="clipboard-write" src="https://emgithub.maary.top/iframe.html?target=https%3A%2F%2Fgithub.com%2FSteve-Mr%2Fdotfiles%2Fblob%2F24ae343cb2ede2f4ee06a7a2255596326b514d40%2F.config%2Fsway%2Fscripts%2Finactive-windows-opacity.py%23L64-L67&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

    ---

3. 我之前用来设置壁纸的 `nitrogen` 在 wayland 下面也没法用了，于是抄朋友的切换到了 [swww](https://github.com/LGFae/swww)。
4. 鼠标设置同样抄朋友的用了 [nwg-look](https://github.com/nwg-piotr/nwg-look)。
5. 在 i3 中使用的 `for_window [floating] sticky enable` 规则不起作用了，这条的作用是让所有的浮动窗口都能跨 workspace 显示，类似于 pin 到了屏幕上。解决办法是比较极端的 `for_window [app_id=".*"] sticky enable` 。

### Waybar

Waybar 好是好，但是没有那种 Theme Collection 还是有点麻烦的。于是打算直接摸 [catppuccin](https://github.com/catppuccin/waybar) 的主题，直到发现这个主题其实只是一个配色集合……  

然后又发现原来 Waybar 的主题是 CSS 定义的，我测这可太 easy 了。  

然后看到了 `r/unixporn` 的 [[hyprland] with waybar ](https://www.reddit.com/r/unixporn/comments/1d2ftc9/hyprland_with_waybar/) 帖，直接从他那里毛了现成的 dotfile 来改，顺带也大概理解了修改思路。  

以这个主题来说其实思路很简单：  
1. 设置整个 bar 的背景透明
2. 设置左、中、右三个模块的背景，设置圆角弧度看起来像是气泡
3. 详细设置每个模块的字体颜色

### Hyprlock

我在 i3 上用的是 [betterlockscreen](https://github.com/betterlockscreen/betterlockscreen)，算是基于 `i3lock-color` 做的一套脚本，遗憾的是他们同样暂时没有支持 Wayland 的打算。于是听朋友推荐转向了 [hyprlock](https://github.com/hyprwm/hyprlock)，边翻 wiki 边写配置，最后效果其实不是很满意，但是字体确实不好调整。  

|:--:|
|![alt text](/assets/2024-12-01-Hello-Wayland/image-1.webp)|

### Rofi

这个虽然朋友找到了新东西但是我实在是懒得去重新搞主题写配置了，于是决定坚守 Rofi……的 [Wayland port](https://github.com/lbonn/rofi)。  

Rofi 基本上没遇到什么问题，主要两个问题：

1. Wayland 和 X11 窗口属性定义和规则不同造成窗口名称太长。  
    `window-foramt` 从原来的 `{w} · {c} · {t}` 变成了 `{t}`
2. 主题里设置 `height` 属性到 `100%` 无法占据完成的垂直空间，而是空出了 waybar 的空间。  
    直接修改了 margin 变成和普通窗口一样的高度。

### Flameshot

这个可太麻烦了……  

首先按照 [flameshot 的文档](https://github.com/flameshot-org/flameshot/blob/master/docs/Sway%20and%20wlroots%20support.md)完整的配置一遍，就在我以为大功告成的时候，发现 flameshot 随机无法保存到剪贴板。  

然后看到 [Copy to clipboard doesn't work on Wayland](https://github.com/flameshot-org/flameshot/issues/2848) 的 issue 下面有[评论](https://github.com/flameshot-org/flameshot/issues/2848#issuecomment-1368644640)提到可以使用 `flameshot gui --raw | wl-copy` 命令来实现写入到剪贴板，测试了一下确实可以，但是这个状态下又不能直接贴图，于是给 sway 的 config 改成了这样的状态：

```yaml
bindsym F1 exec "flameshot gui --raw | wl-copy"
bindsym F2 exec "flameshot gui"
``` 

也许我应该找一个 Wayland 下的 Flameshot 替代品了。

## 但是问题依然有

比如现在正在遇到的 VSCode 随机丢字。由于 Chrome（也就是所有 Chromium 系和 Electron）对于 Wayland 下输入协议的支持不够好，VSCode 只能运行在 XWayland 状态下才能正常使用 Fcitx5 输入，虽然看到一些在 Wayland 模式下进行输入的方法但是我这里全没跑成。_sad :(_  
不过希望还是有的，Chrome 在 129 版本之后支持了 text-input-v3，能原生在 Wayland 下运行同时用 fcitx5 了，理论上 Electron 33 版本之后也会同步支持这个功能，但是可惜 VSC 还在 Electron 32。