---

layout: post	
title: "自用ffmpeg脚本合集"	
date: 2023-12-22 15:32:50	
updated: 2024-05-08 19:48:12	
excerpt: "处理视频时使用到的一些ffmpeg脚本"	
categories: 
- 教程
- 计算机
tag: 
- 教程
- 计算机
typora-root-url: ..
---



# 指令

## 视频相关

### 拼接视频

```bat
@echo off

ffmpeg.exe -f concat -i .\filelist.txt -c copy .\output.mp4

echo done
pause
```

`filelist.txt`内容类似：

```
file 'Word2Vec.mp4'
file 'AttentionLayer.mp4'
file 'DecoderOnly.mp4'
```



### 缩放视频

缩放到1080P

```bash
ffmpeg -i .\input.mp4 -vf scale=1920:1080 -c:a copy output.mp4
```



### 裁取区间视频

```bash
ffmpeg -i input.mp4 -ss 00:01:00 -to 00:03:30 -c copy output.mp4
```



### 将视频转为264编码

```bash
ffmpeg -i 'input.mkv' -map 0 -c:a copy -c:s copy -c:v libx264 'input_264.mkv'
```



## 音频相关

### 合并多音轨

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



更多音轨，比如四个：

```bat
ffmpeg -i "%%F" -filter_complex "[0:1][0:2][0:3][0:4]amerge=inputs=4[a]" -map 0:v -map "[a]" -c:v copy -c:a aac -b:a 256k "output/!output_file!"
```

 只压制1、3音轨：

```bat
ffmpeg -i "%%F" -filter_complex "[0:1][0:3]amerge=inputs=2[a]" -map 0:v -map "[a]" -c:v copy -c:a aac -b:a 256k "output/!output_file!"
```



### 抽取视频音轨

```bash
ffmpeg -i 'input.mp4' -f mp3 -vn bgm.mp3
```



### 生成指定时长的空白音频

```bat
@echo off

set /p duration="Please input blank audio time:"
ffmpeg -f lavfi -t %duration% -i anullsrc blank_%duration%s.mp3 -y

echo generate a %duration%s blank audio
pause
```



### 拼接音频

```bat
@echo off

ffmpeg -f concat -i .\audiolist.txt -acodec copy .\output.mp3

echo done
pause
```



### 合并视频和音频

```bat
@echo off

ffmpeg -i .\output.mp4 -i .\output.mp3 -map 0:v -map 1:a output_bgm.mp4

echo done
pause
```

