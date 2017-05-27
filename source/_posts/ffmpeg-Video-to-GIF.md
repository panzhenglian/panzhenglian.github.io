---
title: ffmpeg Video to GIF
date: 2017-05-23 13:16:26
tags: Video
---
### 安装ffmpeg
```base
brew install ffmpeg
```

### 视频转gif

```base
ffmpeg -i in.mp4 out.gif
# 插入字幕
-vf
#输出RGB24位颜色gif图片
-pix_fmt rgb24 
#输出分辨率
-s 320x240
#帧率
-r 10
#截取，第五秒开始，截取10秒,下面两个命令是一样的
-ss 5 -t 10
-ss 00:00:05 -t 10
```

### 视频属性调整

```base
#缩放视频尺寸 ,-1代表保持长宽比
-vf scale=360:-1
#加倍速播放视频
-filter:v "setpts=0.5*PTS"
#慢倍速播放视频
-filter:v "setpts=2.0*PTS"
#移除视频中的音频
-an
```