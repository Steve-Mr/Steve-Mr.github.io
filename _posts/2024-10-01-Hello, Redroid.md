---
layout: post
title: "Hello, Redroid"
date: 2024-10-01 19:00:00 +0800
toc: true
categories: android 
excerpt_separator: <!--more-->
---

虽然 Blue Archive 的问题仍然没有解决，但至少摆脱 Genymotion 的水印了。

<!--more-->

## 问题

Genymotion 是 Linux 平台上为数不多 Android 模拟器之一，在易用性和性能的平衡上可能是最好的之一。自从主力 Linux 之后 Genymotion 一直是我的主力模拟器，前两天还折腾了[如何增加 Genymotion 虚拟机的存储空间](2024-08-30-Increace-Genmotion-Storage.md)，但是毛病也是有的：  
1. 水印。Genymotion Desktop 虽然是个人免费授权，但是个人版左下角会自带水印。如果说 GUI 下这个小水印没啥影响，那么如果用其自带的工具截图就会感到不妙了。  
![alt text](</assets/2024-10-01-Hello, Redroid/screenshot-2024-10-01_17.09.48.901.png>)
不过这个其实用 ADB 截图能破，比如 `adb exec-out screencap -p | xclip -selection clipboard -t image/png`
2. GPU。Genymotion 转移到 QEMU 后端之后的性能其实挺不错了，不过缺点就是 QEMU 的 GPU 支持并不是那么好做，比如 Geekbench 6 这里压根没有 GPU 的这项测试。
![alt text](</assets/2024-10-01-Hello, Redroid/image-1.png>)  
之前其实无所谓，没有就没有了，但是现在碧蓝航线上了 3D 宿舍，在 Genymotion 上感受到了压力。这也是让我最终决定换到 Redroid 的主要因素。
3. 转译。Genymotion 的转译层最高只到 Android 11，而且似乎上并没有 libndk 的支持。（libndk 似乎对于 AMD CPU 更加友好。）不过碧蓝航线有 x86 的支持所以倒也不是很大的问题，至于没有 x86 支持的 Blue Archive，只是普通的兼容层也不管用，后面再说。  

## Redroid

[Redroid](https://github.com/remote-android/redroid-doc) 是一直支持 GPU 加速的 AIC（Android In Cloud）的解决方案，简单来说就是 Docker 里跑 Android，而且提供了支持 `libndk_translation` 转译层的镜像。不过比较遗憾的是没有提供带有 Gapps 的版本，需要自己折腾。  

具体的流程参考官方文档，我这里就不费劲了。另外对于像我这样的主要冲着跑碧蓝航线来的，可以参考[在linux中使用alas+redroid挂机碧蓝航线](https://blog.akibot.cn/2024/06/09/%E5%9C%A8linux%E4%B8%AD%E4%BD%BF%E7%94%A8alas+redroid%E6%8C%82%E6%9C%BA%E7%A2%A7%E8%93%9D%E8%88%AA%E7%BA%BF/)一文。  

我使用的配置文件也可以作参考：  

<details>
    <summary>docker-compose.yml</summary>

{% highlight yaml %}
services:
    redroid:
      container_name: redroid
      image: docker.io/redroid/redroid:14.0.0-latest
      stdin_open: true
      tty: true
      privileged: true
      restart: always
      ports:
        - "5555:5555"
      volumes:
        - /home/maary/Android/data14:/data
      command:
        - androidboot.redroid_width=1280
        - androidboot.redroid_height=720
        - androidboot.redroid_gpu_mode=auto
        - androidboot.redroid_fps=60
    ALAS:
        ports:
            - "22267:22267"
        volumes:
            - '.:/app/AzurLaneAutoScript:rw'
            # - '../MAA:/app/MAA:rw'
            - '/etc/localtime:/etc/localtime:ro'
        container_name: 'alas'
        image: 'alas'
        build:
            context: ./deploy/docker/
            dockerfile: ./Dockerfile
{% endhighlight %}

</details>

这里没有使用 ws-scrcpy，一来本地已经够用，二来浏览器里的效果也一般。  

## 一些小坑

遗憾的是一切都不是完美的，前前后后我也遇到了几个坑，这里记录几个能想起来的。

### minitouch

我到现在其实也没弄明白这是怎么回事，总之是按照 ALAS 的要求安装完组件启动运行之后，会报找不到 minitouch 的错误。因为也没找到相关的资料，就切回了 Genymotion。这次看了一眼 ALAS 的设置发现改成 maatouch 之后能够正常运行，还挺奇怪。

### 指定 ABI

我也没想到你们碧蓝航线居然只提供了 x86 （32 位）版本啊。  

之前用的是纯 64 位镜像，装了 libchecker 才发现 Redroid 里的碧蓝航线是 arm64 下的。于是出现了选择 64 位但转译还是不转译但 32 位的问题，解决问题的办法则是：跑分！  

但是跑分也有问题，比如 Geekbench 6 现在只支持 x86_64 和 arm64_v8a，但是 Geekbench 5 还有 universal 的版本，于是用 Geekbench 5 跑了下面的结果。安装 Geekbench 5 的时候使用了 `--abi` 来制定架构，如 `adb install --abi x86`  

|![alt text](</assets/2024-10-01-Hello, Redroid/image_2024-10-01_15-27-43.png>)|arm64-v8a|
|:--:|:--:|
|![alt text](</assets/2024-10-01-Hello, Redroid/image_2024-10-01_15-18-46.png>)|x86|
|![alt text](</assets/2024-10-01-Hello, Redroid/image_2024-10-01_15-33-04.png>)|x86_64|

只能说转译带来的损耗比我想象中的大得多。  

另外非常意外的是，我抱着会直接闪退的觉悟直接给碧蓝航线设定 x86 abi 覆盖安装，结果居然能正常跑，还挺意外的。  

### 转译依然转不动

好死不死，我还玩隔壁「碧蓝」：Blue Archive。这位压根没有 x86 的支持，于是必须使用转译层。但是一般的转译层还不行，比如 Redroid 在我这里最好的成绩时能把头一个资源包下载完然后闪退。  

在社区支持更好的 Waydroid 那边有一些[讨论和解法](https://github.com/waydroid/waydroid/issues/788#issuecomment-2311021607)。其中直接改比特位堪称魔法，（似乎还是从市面上的商业模拟器里扒的）遗憾的是 Redroid 和其使用的转译层并不相同，我折腾了一圈之后并没有成功。看以后啥时候整整 `cage` 或者 `wayland` 吧。