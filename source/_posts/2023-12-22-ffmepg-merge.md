---

layout: post	
title: "使用ffmpeg合并视频的多条音轨"	
date: 2023-12-22 15:32:50	
updated: 2023-12-22 16:54:12	
excerpt: "简单的使用ffmpeg合并多条音频流的方法"	
categories: 
- 教程
- 计算机
tag: 
- 教程
- 计算机
typora-root-url: ..
---



# 前言

最近在使用OBS录视频，设置了不同的输入分别录制音轨，就像下面这样：

![](/images/posts/ffmpeg/OBS多音轨.png)

那么对于这些多个音轨，需要进行合并之后再投稿等等



# 指令

```bat
@echo off
setlocal enabledelayedexpansion

set "output_folder=output"

if exist "%output_folder%" (
    rmdir /s /q "%output_folder%"
)

mkdir "%output_folder%"

for %%F in (*.mkv) do (
    echo "-------------------------"
    set "input_file=%%~nF"
    set "output_file=!input_file!_merge.mkv"

    ffmpeg -i "%%F" -filter_complex "[0:1][0:2][0:3]amerge=inputs=3[a]" -map 0:v -map "[a]" -c:v copy -c:a aac -b:a 256k "output/!output_file!"
)

echo All files processed successfully.
pause
```



如果你有更多音轨，比如四个，就改成：

```bat
ffmpeg -i "%%F" -filter_complex "[0:1][0:2][0:3][0:4]amerge=inputs=4[a]" -map 0:v -map "[a]" -c:v copy -c:a aac -b:a 256k "output/!output_file!"
```

 如果你只想压制1、3音轨，就改成

```bat
ffmpeg -i "%%F" -filter_complex "[0:1][0:3]amerge=inputs=2[a]" -map 0:v -map "[a]" -c:v copy -c:a aac -b:a 256k "output/!output_file!"
```



以此类推，按照你的需求进行修改即可
