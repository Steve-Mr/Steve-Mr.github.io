---
layout: post
title:  "Hello, Blue Archive on Redroid 11"
date:   2024-10-05  22:00:00 +0800
toc: true
categories: android 
excerpt_separator: <!--more-->
tags: [Android, Redroid, Blue Archive, 转译层, Emulator]
image: /assets/2024-10-05-redroid-blue-archive/1728137667.webp
---

前几天还念叨 Redroid 上没法跑 Blue Archive，结果今天心血来潮又折腾了一次居然成了。  
我不过是代码的搬运工，感谢社区的力量🙏。  

|![alt text](/assets/2024-10-05-redroid-blue-archive/1728137667.webp)|
|:--:|
|嘿嘿，我的 Hina|


## TL, DR

太长不看版本：  

1. 克隆仓库 [https://github.com/Steve-Mr/redroid-script](https://github.com/Steve-Mr/redroid-script) 到本地。
2. 进入仓库目录
```shell
cd redroid-script/
```
3. 生成镜像
```shell
sudo python redroid.py -a 11.0.0 -gn
```
（可能需要 `lzip` 和 `vim` 的依赖，参考正在使用的发行版的安装方法。）
4. 运行容器
<details>
<summary markdown="span">docker run</summary>

```shell
    ## 运行容器
    sudo docker run -itd --rm --privileged --name redroid_patched \               
        -v ~/Android/data11:/data \
        -p 5555:5555 \
        redroid/redroid:11.0.0_gapps_ndk \
        ro.product.cpu.abilist=x86_64,arm64-v8a,x86,armeabi-v7a,armeabi \
        ro.product.cpu.abilist64=x86_64,arm64-v8a \
        ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi \
        ro.dalvik.vm.isa.arm=x86 \
        ro.dalvik.vm.isa.arm64=x86_64 \
        ro.enable.native.bridge.exec=1 \
        ro.dalvik.vm.native.bridge=libndk_translation.so \
        ro.ndk_translation.version=0.2.2 \
        androidboot.redroid_gpu_mode=auto

    ## 连接 scrcpy
    scrcpy --stay-awake --video-bit-rate=20M --audio-codec=aac --audio-encoder='OMX.google.aac.encoder'
 ```
</details>

<!--more-->


## 你是 Waydroid 用户？

那你来这干什么。  

来，首先 [waydroid_script](https://github.com/casualsnek/waydroid_script) 安装转译层，AMD 选 `libndk`，Intel 选 `libhoudini`。  

然后打补丁（注意这里是为了运行 Blue Archive，非必需操作）

**`libndk`** `->` [这里下载 scripton_ndk.txt](https://github.com/waydroid/waydroid/issues/788#issuecomment-2167334937) `->` `重命名为 scripton_ndk.sh 并设置为可执行文件` `->` `sudo ./scripton_ndk.sh`

**`libhoudini`** `->` [这里下载 scripton.txt](https://github.com/waydroid/waydroid/issues/788#issuecomment-2162386712) `->` `重命名为 scripton.sh 并设置为可执行文件` `->` `sudo ./scripton.sh`

## 现有的解法

Blue Archive 的第一个问题是需要 Play 服务运行，这个还算比较简单，Redroid 给了[手动编译带有 Gapps 版本 docker 镜像](https://github.com/remote-android/redroid-doc/tree/master/android-builder-docker)的办法，虽然可行但是复杂度较高。好在 [redroid-script](https://github.com/ayasa520/redroid-script) 给了不需要重新打包镜像的方式。  

另一个问题则是 Blue Archive 没有原生的 x86_64 版本，而使用转译层时会出现直接闪退的情况。Waydroid 社区针对这个问题给出了上面提到的解决方案。顺便一提 libndk 的补丁是 `.so` 文件里四个 0 改成 1，非常魔法。（似乎还是从模拟器里毛过来的）  

## 开抄

Play 服务的问题已经又 redroid-scipt 解决了，当然我遇到了 `The device isn't Play Protect certified` 的问题，你也可能会遇到。不过不必担心，在 [Troubleshooting](https://github.com/ayasa520/redroid-script?tab=readme-ov-file#troubleshooting) 部分已经给出了解决办法。  

然后则是补丁，redroid-script 似乎从 waydroid_script 获得了灵感，但是其使用的 libndk 版本似乎较老，需要更换为 waydroid_script 使用的版本。修改的主要是链接、MD5 和文件名。  

<details>
  <summary markdown="span">链接、MD5 和文件名的修改</summary>

{% highlight git %}
-  dl_link = "https://github.com/supremegamers/vendor_google_proprietary_ndk_translation-prebuilt/archive/181d9290a69309511185c4417ba3d890b3caaaa8.zip"
+  dl_link = "https://github.com/supremegamers/vendor_google_proprietary_ndk_translation-prebuilt/archive/9324a8914b649b885dad6f2bfd14a67e5d1520bf.zip"

-  shutil.copytree(os.path.join(self.extract_to, "vendor_google_proprietary_ndk_translation-prebuilt-181d9290a69309511185c4417ba3d890b3caaaa8", "prebuilts"), os.path.join(self.copy_dir, "system"), dirs_exist_ok=True)
+  shutil.copytree(os.path.join(self.extract_to, "vendor_google_proprietary_ndk_translation-prebuilt-9324a8914b649b885dad6f2bfd14a67e5d1520bf", "prebuilts"), os.path.join(self.copy_dir, "system"), dirs_exist_ok=True)

-  act_md5 = "0beff55f312492f24d539569d84f5bfb"
+  act_md5 = "c9572672d1045594448068079b34c350"
{% endhighlight %}

</details>

接下来则是重头戏：Docker 的机制让我们很难在容器运行后再对容器内的系统文件打补丁，因此需要直接生成打过补丁的镜像。  

<details>
  <summary markdown="span">patch.py</summary>

{% highlight python %}
import os
import subprocess

def check_hex(file_path, offset, hex_to_check):
    skip_bytes = offset - 0x101000
    read_bytes = len(hex_to_check) // 2
    command = [
        'od', file_path,
        '--skip-bytes={}'.format(skip_bytes),
        '--read-bytes={}'.format(read_bytes),
        '--endian=little',
        '-t', 'x1',
        '-An'
    ]

    command_output = subprocess.check_output(command).decode().replace(' ', '').strip()
    return command_output == hex_to_check

def patch_hex(file_path, offset, original_hex, new_hex):
    file_offset = offset - 0x101000
    if check_hex(file_path, offset, original_hex):
        hex_in_bin = bytes.fromhex(new_hex)
        with open(file_path, 'r+b') as f:
            f.seek(file_offset)
            f.write(hex_in_bin)
        print(f"Patched {file_path} at {file_offset} with new hex {new_hex}")
    elif check_hex(file_path, offset, new_hex):
        print("Already patched")
    else:
        print("Hex mismatch!")

def patch_ndk_library(ndk_path):
    if os.path.isfile(ndk_path):
        if os.access(ndk_path, os.W_OK) or os.geteuid() == 0:
            patch_hex(ndk_path, 0x307dd1, '83e2fa', '83e2ff')
            patch_hex(ndk_path, 0x307cd6, '83e2fa', '83e2ff')
        else:
            print("libndk_translation is not writable. Please run with sudo.")
    else:
        print("libndk_translation not found. Please install it first.")

# 调用示例
if __name__ == "__main__":
    ndk_path = "ndk/system/lib64/libndk_translation.so"
    patch_ndk_library(ndk_path)

{% endhighlight %}

</details>

`patch.py` 其实是我直接用 `scripton_ndk.txt` 问 ChatGPT 要的 Python 版本代码，作用自然相同。然后在下载解压 ndk 完成后增加打补丁的过程。  

```
+  ndk_path = os.path.join(self.copy_dir, "system", "lib64", "libndk_translation.so")
+  patch_ndk_library(ndk_path)
```

到这里修改算是完成，我也 fork 了 redroid-script 仓库，上传了[我修改后的版本](https://github.com/Steve-Mr/redroid-script)，你可以使用。  

## 示例

我需要的只有 GAPPS 和打过补丁的 libndk，因此我使用的命令如下，不过可能需要 lzip 和 vim 的依赖，需要参考你使用的发行版的安装方法。

```shell
# 进入 redroid-script 文件夹
cd Build/redroid-script/
# redroid 11 镜像的基础上增加 gapps 和打过补丁的 libndk
sudo python redroid.py -a 11.0.0 -gn
```

在完成镜像的生成后，运行容器的命令如下：

```shell
sudo docker run -itd --rm --privileged --name redroid_patched \               
    -v ~/Android/data11:/data \
    -p 5555:5555 \
    redroid/redroid:11.0.0_gapps_ndk \
    ro.product.cpu.abilist=x86_64,arm64-v8a,x86,armeabi-v7a,armeabi \
    ro.product.cpu.abilist64=x86_64,arm64-v8a \
    ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi \
    ro.dalvik.vm.isa.arm=x86 \
    ro.dalvik.vm.isa.arm64=x86_64 \
    ro.enable.native.bridge.exec=1 \
    ro.dalvik.vm.native.bridge=libndk_translation.so \
    ro.ndk_translation.version=0.2.2 \
    androidboot.redroid_gpu_mode=auto
```

如果像我一样已经有一个正在运行的 `5555` 端口的 redroid 实例，那么这里的 `-p 5555:5555 \` 需要修改为一个不一样的端口，如 `-p 5556:5555 \`，这时为了能够使用 `scrcpy` 需要用 `adb` 连接其端口如 `adb connect localhost:5556`，使用默认配置则不需要这一步。  

如果为了多确认一步，可以将容器里的 `libndk_translation.so` 拷贝出来再用上面提到的 `scripton_ndk.sh` 检查是否都是 `Already patched`。拷贝的命令是 `sudo docker cp redroid_patched:/system/lib64/libndk_translation.so ~/`，需要修改 `scripton_ndk.sh` 里的 `ndk_path` 变量为你复制出来的位置。  

最后则是 `scrcpy` 的命令：`scrcpy --stay-awake --video-bit-rate=20M --audio-codec=aac --audio-encoder='OMX.google.aac.encoder'`。同样如果有多个实例/设备需要连接的具体设备如 `-s localhost:5556`。  
