---
layout: post
title: "Sway 下记忆 Firefox PIP 窗口位置"
date: 2024-12-12 22:30:00 +0800
sitemap:
  lastmod: 
toc: true 
excerpt_separator: <!--more-->
tags:
  - Linux
  - Sway
---

## TL; DR

保存下面的代码到文件，比如 `remember-pip.py`，然后在 sway 配置下面加一个 `exec remember-pip.py`，当然路径需要正确。第一次使用会默认把 PIP 移动到右下角，移动过之后则会记录移动的位置。  

<details>
<summary markdown="span">
这是代码
</summary>

<script src="https://emgithub.maary.top/embed-v2.js?target=https%3A%2F%2Fgithub.com%2FSteve-Mr%2Fdotfiles%2Fblob%2Fmain%2F.config%2Fsway%2Fscripts%2Fremember-pip.py&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></script>

</details>

<!--more-->

## 问题

Firefox 最近在 Labs 增加了个功能：[切换标签页的时候自动将当前视频 PIP](https://blog.nightly.mozilla.org/2024/08/26/streamline-your-screen-time-with-auto-open-picture-in-picture-and-more-these-weeks-in-firefox-issue-166/)。虽然这个特性确实有时候会整点乌龙，但是整体还是非常好用的。不过在我[切换到 Sway](/2024-12-01-Hello-Wayland.md) 之后遇到了问题：Firefox 的 PIP 既不会记忆上次的窗口大小也不会记忆位置。  

于是搜到了类似的 [Bug 反馈](https://bugzilla.mozilla.org/show_bug.cgi?id=1767414)，说明至少不是我犯了低级的配置错误。但是坏消息如下：  

> This is not "intentional" as much as a known Wayland limitation, see [bug 1870955](https://bugzilla.mozilla.org/show_bug.cgi?id=1870955).

好嘛，没有想到在这种地方翻了车。  

## 解法

其实没有很简便的解决方法，最终还是落回到靠代码解决。

### 省事解法

首先搜索了之后没有现成的东西，但是有自动给窗口丢到角落的[脚本](https://git.xkonni.de/konni/config_sway/src/branch/main/bin/sway_move)。可以配合给 PIP 窗口的自动 floating 规则直接窗口出来就丢到角落，至于大小可以用 `resize` 百分比来。  

<details>
<summary markdown='span'>在这里记录一下脚本内容</summary> 

```shell
#!/usr/bin/env bash

# Copyright (C) 2020-2021 Bob Hepple <bob.hepple@gmail.com>

# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or (at
# your option) any later version.
#
# This program is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
# General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program. If not, see <http://www.gnu.org/licenses/>.

# http://bhepple.freeshell.org

initialise() {
    PROG=$(basename $0)
    VERSION="1.0"
    ARGUMENTS="top-right|bottom-right|bottom-left"
    USAGE="move a floating window to the edges because sway lacks a way to do it!"

    case $1 in
        -h|--help)
            echo "$USAGE"
            exit 0
            ;;
        top-left|top-center|top-right|center-left|center-center|center-right|bottom-left|bottom-center|bottom-right)
            command="$1"
            ;;
        *)
            echo "$PROG: bad argument" >&2
            exit 1
            ;;
    esac

    return 0
}

initialise "$@"
width=$( swaymsg -t get_outputs |
    jq -r '.. | select(.focused?) | .rect | .width' )
height=$( swaymsg -t get_outputs |
    jq -r '.. | select(.focused?) | .rect | .height' )
scale=$(swaymsg -t get_outputs | jq -r '.. | select(.focused?) | .scale' )
monitor_width=$(echo "${width}/$scale / 1" | bc)
monitor_height=$(echo "${height}/$scale / 1" | bc)
win_dim=( $( swaymsg -t get_tree |
    jq '.. | select(.type?) | select(.type=="floating_con") | select(.focused?)|.rect.width, .rect.height, .deco_rect.height' ) )

win_width=${win_dim[0]}
win_height=${win_dim[1]}
deco_height=${win_dim[2]}

spacing_x=5
spacing_y=35
new_x=$spacing_x
new_y=0
case $command in
  ## top
  top-center)
    new_x=$(( (monitor_width - win_width)/2 ))
    ;;
  top-right)
    new_x=$(( monitor_width - win_width - 2*spacing_x ))
    ;;
  ## center
  center-left)
    new_y=$(( (monitor_height - win_height - deco_height - spacing_y)/2 ))
    ;;
  center-center)
    new_x=$(( (monitor_width - win_width)/2 ))
    new_y=$(( (monitor_height - win_height - deco_height - spacing_y)/2 ))
    ;;
  center-right)
    new_x=$(( monitor_width - win_width -2*spacing_x ))
    new_y=$(( (monitor_height - win_height - deco_height - spacing_y)/2 ))
    ;;
  ## bottom
  bottom-left)
    new_y=$(( monitor_height - win_height - deco_height - spacing_y ))
    ;;
  bottom-center)
    new_x=$(( (monitor_width - win_width)/2 ))
    new_y=$(( monitor_height - win_height - deco_height - spacing_y ))
    ;;
  bottom-right)
    new_x=$(( monitor_width - win_width -2*spacing_x ))
    new_y=$(( monitor_height - win_height - deco_height - spacing_y ))
    ;;
esac

swaymsg "move position $new_x $new_y"

```

</details>

在 Sway 配置里使用可以用类似 `for_window [title="Picture-in-Picture"]  floating enable, exec /home/maary/.config/sway/scripts/sway_move bottom-right` 这样的结构，resize 就看自己思路吧。  

### 还是得记忆位置

但是折腾到这里了又感觉，干脆一步到位直接写个记录位置的脚本算了。思路很简单：  
1. 在后台跑个 python 脚本持续监听窗口状态。
2. 有 PIP 出来就自动调整大小和位置到上次的状态，如果没有上次的记录就自动移动到右下角。
3. 如果移动和调整了 PIP 窗口，那么窗口的焦点状态会发生改变，然后根据最后的状态写到文件中供第二步读取。

得益于 Sway 和 i3 的兼容性，i3ipc-python 库也支持 Sway，因此可以直接摸过来用。然后理所当然的找 GPT 要代码了，顺便还出发了 ChatGPT 的 Canvas 特性。  

![ChatGPT Canvas](/assets/2024-12-12-sway-remember-pip/image.png)

不过这次有些逻辑最后还是自己去调整，而且有些地方的逻辑其实感觉不太对劲，但是既然现在能用也就懒得调整了。设置好可执行权限之后在 Sway 配置里加上 `exec remember-pip.py`，代码直接丢下面不折叠了。  

```python
#!/usr/bin/python

import i3ipc
import os
import json

def save_location(window):
    """
    Record the window's location and save it to .data/saved_locations.
    If the directory or file does not exist, create them.
    """
    # Ensure the .data directory exists
    data_dir = os.path.join(os.getcwd(), ".data")
    os.makedirs(data_dir, exist_ok=True)

    # File to save the locations
    file_path = os.path.join(data_dir, "saved_locations")

    # Read existing locations if the file exists
    locations = {}
    if os.path.exists(file_path):
        with open(file_path, "r") as file:
            try:
                locations = json.load(file)
            except json.JSONDecodeError:
                pass

    # Update or add the location for the current window
    locations = {
        "x": window.rect.x,
        "y": window.rect.y,
        "width": window.rect.width,
        "height": window.rect.height,
    }

    # Save back to the file
    with open(file_path, "w") as file:
        json.dump(locations, file, indent=4)

def restore_location(window):
    """
    Restore the window's location based on saved data or move it to the bottom right.
    """

    # Ensure the .data directory and file path
    data_dir = os.path.join(os.getcwd(), ".data")
    file_path = os.path.join(data_dir, "saved_locations")

    if not os.path.exists(file_path):
        move_bottom_right(window)
        return

    # Load saved locations
    try:
        with open(file_path, "r") as file:
            location = json.load(file)
    except (json.JSONDecodeError, FileNotFoundError):
        move_bottom_right(window)
        return

    # Check if the window ID exists in the saved locations
    window.command(f"resize set width {location['width']}px height {location['height']}px")
    window.command(f"move absolute position {location['x']} {location['y']}")



def move_bottom_right(window):
    """Moves the given window to the bottom right corner of the screen."""

    i3 = i3ipc.Connection()
    outputs = i3.get_outputs()
    focused_output = next((o for o in outputs if o.focused), None)
    if not focused_output:
        raise RuntimeError("No focused output found.")

    # Calculate monitor dimensions considering scale
    monitor_width = int(focused_output.rect.width / focused_output.scale)
    monitor_height = int(focused_output.rect.height / focused_output.scale)

    # Get window dimensions
    win_width = window.rect.width
    win_height = window.rect.height
    deco_height = window.deco_rect.height  # Assuming this gives the decoration height

    # Calculate new position
    spacing_x = 5
    spacing_y = 35
    new_x = monitor_width - win_width - 2 * spacing_x
    new_y = monitor_height - win_height - deco_height - spacing_y

    # Move the window
    window.command(f"move position {new_x} {new_y}")

def main():
    # Create a connection to the i3/sway IPC
    ipc = i3ipc.Connection()

    # Dictionary to track the focus state of windows
    tracked_windows = {}

    def on_window_event(ipc, event):
        current_window = event.container

        # Check if the window meets the conditions
        if current_window.name == "Picture-in-Picture" and current_window.floating.startswith('user_on'):
            if event.change == "focus":
                restore_location(current_window)
                tracked_windows[current_window.id] = current_window


        focused_window = ipc.get_tree().find_focused()
        for window_id, window in list(tracked_windows.items()):
            if focused_window.id != window.id and window.focused:
                try:
                    tree = ipc.get_tree()
                    window = tree.find_by_id(window_id)
                    if window is not None and window.floating.startswith('user_on'):
                        save_location(window)
                    del tracked_windows[window_id]
                    break
                except Exception as e:
                    print(f"Error: {e}")
                    break

    # Subscribe to window focus and window new events
    ipc.on("window::focus", on_window_event)

    # Start the main event loop
    ipc.main()

if __name__ == "__main__":
    main()
```