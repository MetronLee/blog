<!--
 * @Author: Metron Lee
 * @Date: 2025-09-17 13:47:12
 * @LastEditors: Metron Lee
 * @LastEditTime: 2025-09-18 00:22:34
 * @Description: 
-->
---
draft: false
date: 2025-09-11T14:33:36+08:00
title: 录音处理（人声、降噪、转录）
slug:
description:
categories:
  - Skills
tags:
  - skills
  - workflow
---
# Motivation

上哲学课听不懂，录音回去复习。

##### 需求

- 人声增强
- 转录
- 录音和转录稿同步播放

##### 困难

- 教授声音低沉
- MacBook Air 录音，不定向，环境噪音大

##### 环境

- Windows 11
- Nvidia Cuda

# Steps

### FFmpeg - 音量标准化

让整体音量一致，不高不低。

有两种方法：一遍处理和两遍处理（推荐）。嫌麻烦的话就一遍处理：

```Bash
ffmpeg -i input.wav -af loudnorm=I=-23:TP=-2:LRA=7 -ar 248k output.wav
```

两遍处理：先解析一次原音频，然后使用解析出来的参数：

```Bash
ffmpeg -i input.wav -filter:a loudnorm=print_format=json -f null -
ffmpeg -i input.wav -filter:a loudnorm=i=-23:tp=-2:lra=7:measured_I=-XX.X:measured_TP=-Y.Y:measured_LRA=Z.Z:measured_R128_thresh=-W.W -ar 248k output.wav
```

其中 `X.X`, `Y.Y`, `Z.Z`, `W.W` 换成第一遍的输出。

由于我每次录制的环境相同，音频质量相同，所以解析了一次之后得到下行代码直接复用就行：

```Bash
 ffmpeg -i imput.m4a -af loudnorm=I=-23:TP=-2:LRA=7:measured_I=-31.47:measured_TP=-10.58:measured_LRA=16.8:measured_thresh=-43.46:offset=-2.0:linear=true:print_format=summary -b:a 248k loudnormed.mp3
```

### UVR5 - 人声提取

[Ultimate Vocal Remover V5 官网](https://ultimatevocalremover.com/)

目前试过一些模型，发现 UVR-MDX-NET Inst HQ 5 效果很不错，142分钟的音频处理了14分钟，

### Audacity - 降噪

其实前一步效果已经很好了，可以略。

选中一部分无人声的噪音区间，然后在 Effect - Noise Removal and Repair - Noise Reduction 页面里 Get Noise Profile, 然后全选整段音频区间，打开同样的 Noise Reduction 页面，微调几个参数后 Preview，最后 OK 就行。

EQ曲线的话不太会调，感觉调了用处也不大。

### Whisper - 转录

[whisper 仓库地址](https://github.com/openai/whisper)

##### 安装

需要先装好 `ffmpeg`. 然后 `pip` 安装。

```bash
pip install -U openai-whisper
```

##### 运行

```bash
whisper input.flac input.mp3 input.wav --language en --initial_prompt="Heidegger, Phenomenology"
```

其中，
- `initial_prompt` 是初始提示词，用来准确识别一些术语。有意思的是根据[这篇文章](https://medium.com/axinc-ai/prompt-engineering-in-whisper-6bb18003562d), 在提示词中可以用短横线 `-` 来区分说话者，e.g.

```bash
--initial_prompt="- How are you? - I'm fine, thank you."
```

- `--carry_initial_prompt=True` 可以让初始提示词适用于全部。因为 whisper 是30s的滑动窗口算法，所以默认 `carry_initial_prompt=False`, 并将前30s的内容用来作为后面的提示词。
- `--model turbo` 设置模型，默认 `turbo`，是 `large-v3` 的优化加快版，速度是真实时间的~8X
- `--language en` 不解释

运行完成后，在当前工作目录下导出 .srt .vtt .txt 的逐字稿。也可以指定 `--output_dir /path/to/output/folder`.

### 播放

用 Gemini CLI 写了个网页工具同步播放音频和逐字稿，[项目地址](https://github.com/MetronLee/Minimalist-Transcript-Player)

# Afterword

途中花费了大量时间精力，感觉陷入工具的汪洋大海而本末倒置了，引以为戒。写下来算是画个句号，要好好学习了🤓
