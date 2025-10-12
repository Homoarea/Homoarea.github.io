---
title: ffmpeg for python
published: 2025-10-12
description: "本文介绍ffmpeg的简单使用和使用python调用ffmpeg"
image: ""
tags: [python, ffmpeg, 教程]
category: "教程"
draft: false
lang: ""
---

## ffmpeg

[ffmpeg](https://ffmpeg.org/)是一个音视频工具,可以用其进行剪辑,封装,调节响度,浮动图片等各种操作.  
不同平台使用不同安装包安装,[下载地址](https://ffmpeg.org/download.htmlhttps://ffmpeg.org/download.html)
本文使用 Linux

使用 ffmpeg,比如你可以使用:

```bash
ffmpeg -i <input video>
```

获取`<input video>`的视频信息,告知了分辨率,时长,封装格式等信息

我们来使用 ffmpeg 剪切视频试试

测试视频(出自《kill la kill》动画的片段)
<video src="./klk.mp4" controls></video>

使用

```bash
ffmpeg -i <in_filename>
-ss <start_time>
-t <duration>
<out_filename>
```

- `<in_filename>`:输入文件
- `<start_time>`:开始时间
- `<duration>`:持续时间
- `<out_filename>`:输出文件

## 使用 python 第三方包

### 起步

我们使用`ffmpeg-python`来操作
::github{repo="kkroening/ffmpeg-python"}

```bash
# 安装
pip install ffmpeg-python
```

将刚才的操作封装

```python
import ffmpeg

"""
ffmpeg -i in_filename
获取视频信息
"""
def get_video_info(in_filename: str):
    probe = ffmpeg.probe(in_filename)
    return next(
        (stream for stream in probe["streams"] if stream["codec_type"] == "video"), None
    )

"""
ffmpeg -i in_filename
-ss start_time,-t duration
out_filename
从start_time开始,截取duration时长的视频
"""
def division_simple(in_filename: str, out_filename: str,start_time: int=0,duration: int=10):
    (
        ffmpeg.input(in_filename,ss=start_time,t=duration)
                .output(out_filename)
                .run()
    )
```

在本目录创建`assets`文件夹,放入测试视频,测试一下

```python
from pprint import pprint

video_file = "./assets/klk.mp4"
#打印信息
pprint(get_video_info(video_file))
```

```python
#剪切,默认从0开始剪切10s
division_simple(video_file,"./assets/test1.mp4")
```

### 写一写

我们可以将剪切不断重复,将一个视频分成若干分 duration 长的小段

```python
import ffmpeg
import threading
import math
import re
import os

"""
将视频截取为时长为duration的若干小片段
"""
def division_simple(in_filename: str, out_filename: str,start_time: int=0,duration: int=10):
    (
        ffmpeg.input(in_filename,ss=start_time,t=duration)
                .output(out_filename)
                .run()
    )
"""
将视频截取为时长为duration的若干小片段
"""
def div_mp4_segments(in_filename: str,duration: int,out_dir: str=None,out_prefix: str=None,out_suffix: str=".mp4"):
    out_dir=os.path.dirname(in_filename) if not out_dir else out_dir
    out_prefix=in_filename if not out_prefix else out_prefix
    in_duration=math.ceil(float(get_video_info(in_filename)['duration']))
    try:
        for i in range(math.ceil(in_duration/duration)):
            # 创建线程进行截取,target(函数名),args(参数)
            threading.Thread(target=division_simple,args=(in_filename,f"{out_dir}/{out_prefix}{i}{out_suffix}",i*duration,duration)).start()
    except ffmpeg.Error as e:
        print(e.stderr,"创建ffmpeg失败!")
```

将测试视频分割

```python
div_mp4_segments(video_file,10,out_prefix="part")
```

ffmpeg 不但能剪切,也能拼接,试一试

```python
"""
在dir目录下将匹配pattern(正则表达式)的视频拼接
"""
def concat_mp4(dir: str,pattern: str,out_filename: str):
    segs=list(map(lambda x:f"{dir}/{x}",filter(lambda x:re.match(pattern,x),os.listdir(dir))))
    print(segs)
    concat_video=ffmpeg.input(segs[0])
    for i in range(1,len(segs)):
        concat_video=ffmpeg.concat(concat_video,ffmpeg.input(segs[i]))
    concat_video=ffmpeg.output(concat_video,out_filename)
    ffmpeg.run(concat_video)
```

将刚才分割的视频拼接吧

```python
concat_mp4("./assets","part","./assets/concat.mp4")
```

## 自定义包 TODO

使用第三方包固然方便,但也有局限,我们自己写个来自用
//to be continue;
