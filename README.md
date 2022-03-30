# 一、FFmpeg概念
> FFmpeg 是一个自由软件，可以运行音频和视频多种格式的录影、转换、流功能[1]，包含了libavcodec——这是一个用于多个项目中音频和视频的解码器库，以及libavformat——一个音频与视频格式转换库。

## 1.1容器

> 视频文件本身其实是一个容器（container），里面包括了视频和音频，也可能有字幕等其他内容。

常见的容器格式有以下几种。一般来说，视频文件的后缀名反映了它的容器格式。

> * MP4
> * MKV
> * WebM
> * AVI

下面的命令查看 FFmpeg 支持的容器。

```powershell
$ ffmpeg -formats
```

## 1.2编码格式

> 视频和音频都需要经过编码，才能保存成文件。不同的编码格式（CODEC），有不同的压缩率，会导致文件大小和清晰度的差异。

常用的视频编码格式如下：

> * H.262
> * H.264
> * H.265
> * VP8
> * VP9
> * AV1

常用的音频编码格式如下：

> * MP3
> * AAV

下面的命令可以查看 FFmpeg 支持的编码格式，视频编码和音频编码都在内。

```powershell
$ ffmpeg -codecs
```

## 1.3 编码器

> 编码器（encoders）是实现某种编码格式的库文件。只有安装了某种格式的编码器，才能实现该格式视频/音频的编码和解码。

以下是一些 FFmpeg 内置的视频编码器：

> * libx264：最流行的开源 H.264 编码器
> * NVENC：基于 NVIDIA GPU 的 H.264 编码器
> * libx265：开源的 HEVC 编码器
> * libvpx：谷歌的 VP8 和 VP9 编码器
> * libaom：AV1 编码器

常用音频编码器如下：

> * libfdk-aac
> * aac

下面的命令可以查看 FFmpeg 已安装的编码器：

```powershell
$ ffmpeg -encoders
```

## 1.4 复用和解复用（mux）

> 把不同的流按照某种容器的规则放入容器，这种行为叫做复用（mux）
>
> 把不同的流从某种容器中解析出来，这种行为叫做解复用(demux)

## 1.5帧率和码率

> 帧率也叫帧频率，帧率是视频文件中每一秒的帧数，肉眼想看到连续移动图像至少需要15帧。
>
> 比特率(也叫码率，数据率)是一个确定整体视频/音频质量的参数，秒为单位处理的位数，码率和视频质量成正比，在视频文件中中比特率用bps来表达。

# 二、FFmpeg常用命令格式

FFmpeg 的命令行参数非常多，可以分成五个部分。

```powershell
$ ffmpeg {1} {2} -i {3} {4} {5}
```

参数说明依次如下：

> 1. 全局参数
> 2. 输入文件参数
> 3. 输入文件
> 4. 输出文件参数
> 5. 输出文件

参数较多可写成多行：

```powershell
$ ffmpeg \
[全局参数] \
[输入文件参数] \
-i [输入文件] \
[输出文件参数] \
[输出文件]
```

# 三、FFmpeg常用命令行参数

FFmpeg 常用的命令行参数如下

> * `-c`：指定编码器
> * `-c copy`：直接复制，不经过重新编码（这样处理比较快）
> * `-c:v`：指定视频编码器
> * `-c:a`：指定音频编码器
> * `-i`：指定输入文件
> * `-an`：去除音频流
> * `-vn`： 去除视频流
> * `-preset`：指定输出的视频质量，会影响文件的生成速度，有以下几个可用的值 ultrafast, superfast, veryfast, faster, fast, medium, slow, slower, veryslow
> * `-y`：不经过确认，输出时直接覆盖同名文件

## 3.1主要参数

> * -f fmt 强制参数fmt格式
> * -i 输入文件
> * -y 覆盖输出文件
> * -t 时长
> * -ss 开始时间
> * -g 关键帧间隔控制（视频跳转需要关键帧）

## 3.2视频参数

> * -b 设置比特率，缺省200kb/秒
>
> * -r 设置帧频，缺省25（用于视频截图）
>
> * -s 设置帧大小，格式为WXH，缺省160x128
>
> * -vn 不做视频记录
>
> * -vcodec codec 强制使用codec编解码方式
>
> * -aspect 设置横纵比 4:3  16:9

## 3.3音频参数

> * -ab 设置音频码率
> * -ar 设置音频采样率
> * -ac 设置通道 缺省为1
> * -an 去年音频
> * -acodec codec 使用codec编解码

# 四、常用命令

## 4.1查看文件信息

> 查看视频文件的元信息，比如编码格式和比特率，可以只使用`-i`参数。

```powershell
$ ffmpeg -i input.mp4
```

> 上面命令会输出很多冗余信息，加上`-hide_banner`参数，可以只显示元信息。

```powershell
$ ffmpeg -i input.mp4 -hide_banner
```

## 4.2转换编码格式

> 转换编码格式（transcoding）指的是，  将视频文件从一种编码转成另一种编码。比如转成 H.264 编码，一般使用编码器`libx264`，所以只需指定输出文件的视频编码器即可。

```powershell
$ ffmpeg -i [input.file] -c:v libx264 output.mp4
```

> 下面是转成 H.265 编码的写法。

```powershell
$ ffmpeg -i [input.file] -c:v libx265 output.mp4
```

## 4.3转换容器格式

> 转换容器格式（transmuxing）指的是，将视频文件从一种容器转到另一种容器。下面是 mp4  转 webm 的写法。如下：只是转一下容器，内部的编码格式不变，所以使用`-c copy`指定直接拷贝，不经过转码，这样比较快

```powershell
$ ffmpeg -i input.mp4 -c copy output.webm
```

## 4.4调整码率

> 调整码率（transrating）指的是，改变编码的比特率，一般用来将视频文件的体积变小。下面的例子指定码率最小为964K，最大为3856K，缓冲区大小为 2000K。

```powershell
$ ffmpeg \
-i input.mp4 \
-minrate 964K -maxrate 3856K -bufsize 2000K \
output.mp4
```

## 4.5改变分辨率（transsizing）

> 下面是改变视频分辨率（transsizing）的例子，从 1080p 转为 480p 。

```powershell
$ ffmpeg \
-i input.mp4 \
-vf scale=480:-1 \
output.mp4
```

## 4.6提取音频

> 有时，需要从视频里面提取音频（demuxing），写法如下：`-vn`表示去掉视频，`-c:a copy`表示不改变音频编码，直接拷贝。

```powershell
$ ffmpeg \
-i input.mp4 \
-vn -c:a copy \
output.aac
```

## 4.7添加音轨

> 添加音轨（muxing）指的是，将外部音频加入视频，比如添加背景音乐或旁白。如下：示例中，有音频和视频两个输入文件，FFmpeg 会将它们合成为一个文件。

```powershell
$ ffmpeg \
-i input.aac -i input.mp4 \
output.mp4
```

## 4.8截图

> 下面的例子是从指定时间开始，连续对1秒钟的视频进行截图。

```powershell
$ ffmpeg \
-y \
-i input.mp4 \
-ss 00:01:30 -t 00:00:01 \
output_%3d.jpg
```

> 如果只需要截一张图，可以指定只截取一帧。如下：`-vframes 1`指定只截取一帧，`-q:v 2`表示输出的图片质量，一般是1到5之间（1 为质量最高）。

```powershell
$ ffmpeg \
-ss 01:23:45 \
-i input \
-vframes 1 -q:v 2 \
output.jpg
```

## 4.9裁剪

> 裁剪（cutting）指的是，截取原始视频里面的一个片段，输出为一个新视频。可以指定开始时间（start）和持续时间（duration），也可以指定结束时间（end）。如下：`-c copy`表示不改变音频和视频的编码格式，直接拷贝，这样会快很多。

```powershell
命令如下：
$ ffmpeg -ss [start] -i [input] -t [duration] -c copy [output]
$ ffmpeg -ss [start] -i [input] -to [end] -c copy [output]

示例：
$ ffmpeg -ss 00:01:50 -i [input] -t 10.5 -c copy [output]
$ ffmpeg -ss 2.5 -i [input] -to 10 -c copy [output]
```

## 4.10为音频添加封面

> 有些视频网站只允许上传视频文件。如果要上传音频文件，必须为音频添加封面，将其转为视频，然后上传。下面命令可以将音频文件，转为带封面的视频文件。如：下面命令中，有两个输入文件，一个是封面图片`cover.jpg`，另一个是音频文件`input.mp3`。`-loop 1`参数表示图片无限循环，`-shortest`参数表示音频文件结束，输出视频就结束。

```powershell
$ ffmpeg \
-loop 1 \
-i cover.jpg -i input.mp3 \
-c:v libx264 -c:a aac -b:a 192k -shortest \
output.mp4
```

## 4.11压缩视频大小

> * -threads 4 开启多线程
> * -i input.mp4 表示输入要转换的文件
> * -vcodec libx264 设定视频的编码器
> * -preset fast 指定编码的配置。
> * -crf 28：重要选项，指定输出视频的质量，取值范围0-51，默认值23，数字越小输出视频的质量越高。
> * scale=1280:720 设置视频显示的尺寸
> * -acodec libmp3lame 设置音频编码为MP3
> * -ab 128k 设置音频流量，值越小，压缩率越高，质量越差。

```powershell
ffmpeg  -threads 4 \
-i input.mp4 \
-vcodec libx264 \
-preset fast \
-crf 28 \
-y -vf scale=1280:720 \
-acodec libmp3lame \
-ab 128k output.mp4
```

## 4.12录屏

> 在Windows下，可以用FastStone Capture等来实现录屏，Linux下也可以用vokoscreenNG，但实际上，FFmpeg在命令行下就可以做到，不需要GUI。
>
> 常用参数如下：
>
> * -framerate 25：表示我录制的帧率为25
> * -video_size：需要录制的宽度和高度
> * x11grab:表明我们是通过x11抓屏的方式 ，这是Linux下的命令，如果是Win，则使用gdigrab

```powershell
ffmpeg -video_size 1024x768 \
-framerate 25 -f x11grab -i :0.0+100,200 output.mp4
```

## 4.13从视频截选指定长度的内容生成GIF图片

> 常用参数如下：
>
> -ss：开始时间
>
> -t：持续时长

```pow
ffmpeg -ss 3 -t 5 -i input.mp4 -s 480*270 -f gif out.gif
```

## 4.14从视频中抽取音频

```powershell
ffmpeg -i input.mp4 -f mp3 -vn qfl.mp3

```

## 4.15音频格式转换

> 指定比特率即可
>
> 常用参数说明：
>
> -ab 设置音频码率

```powershell
ffmpeg -i input.flac  -ab 256k output.mp3
```



