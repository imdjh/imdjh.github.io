---
published: true
comments: true
layout: post
title: How to capture high quality screen cast and convert to GIF(700k, ~10s duration)
category: howto
tags: [linux, ffmpeg, convert]
date: 2015-10-09 22:12:56 +0800
---
## Prepare:
Install ffmpeg, to shorten encoding duration. Please considering compiling ffmpeg yourself(instead of using binary file from package).  
Install ImageMagick (or GraphicsMagick).

## Go capture your screen:
{% highlight bash %}
$ sleep 10; ffmpeg -y -video_size 1366x680 -framerate 25 -f x11grab -i :0.0+0,80 output.mp4
## sleep {X}:                      prepare for X seconds before start recording
## -y:                             overwrite output.mp4 if exists
## -video_size {width}x{height}:   make frame size to width x height
## -framerate:                     frame per second
## -f x11grab:                     the encoder used, you shouldn't modify this =/
## -i {a}.{b}+{left},{top}:        record screen {a}.{b}(FYI: X server $DISPLAY) with offset from left top corner of screen to {left} {top}
{% endhighlight %}

## Review and trim your clip(output.mp4 -> myclip.mp4)
{% highlight bash %}
$ ffmpeg -y -i output.mp4 -ss 3 -t 11 myclip.mp4; ffplay myclip.mp4
## -i:         specify which input file
## --ss {n}:   trim {n} seconds from begining
## -t {d}:     secify the duration of myclip.mp4
## ffplay:     play myclip.mp4 once the encoding ends
{% endhighlight %}

## Output as series of pictures(myclip.mp4 -> PNGs)
{% highlight bash %}
$ rm -rf frames
$ mkdir frames
$ ffmpeg -i myclip.mp4 -vf scale=320:-1:flags=lanczos,fps=10 frames/ffout%03d.png
## frames/:            make a new directory to store output pictures
## scale={width}:-1:   make pictures at scale of {width} with calculated height
## flags={whatever}:   some MAGIC
## fps={x}:            make pictures with playback at {x}fps
## ffout%03d.png:      file name with 001, 002, ..., 999
{% endhighlight %}

## Combine series of pictures(PNGs -> GIF)
{% highlight bash %}
$ convert -delay 3 -loop 0 -dither None -colors 80 "frames/ffout*.png" -fuzz "10%" -layers OptimizeFrame "output.gif"
## -delay {f}: skip {f} frames between each frame in GIF, making GIF playing fast
## -loop 0: make GIF loop forever
## -color {c}: Make about {c} colors in available in GIF
## -fuzz "10%": fuzz each frame, would make GIF not recognizable
## -layers: some MAGIC
{% endhighlight %}

## Here's the demo GIF[54s(mp4) -> ~20s(GIF), 900k]:
![demo of converting][convert result pic]

#### with:
{% highlight bash %}
$ sleep 2; ffmpeg -y -video_size 683x384 -framerate 15 -f x11grab -i :0.0+0,0 output.mp4
$ ffmpeg -i output.mp4 -ss 10 -t 54 myclip.mp4; ffplay myclip.mp4
$ ffmpeg -i myclip.mp4 -vf fps=6 frames/ffout%03d.png
$ convert -delay 7 -loop 0 -dither None -colors 3 "frames/ffout*.png" -fuzz "10%" -layers OptimizeFrame "output.gif"
{% endhighlight %}

## Reference:
[More demo GIFs!!1] [origin post at v2ex]    
[Compiling ffmpeg][compiling ffmpeg]    
[Capture screen in Linux][ffmpeg wiki on capturing]    
[How to trim first seconds with ffmpeg][SO trim]    
[How to convert with reasonable quality][SO convert]

[SO convert]:                 http://superuser.com/questions/556029/how-do-i-convert-a-video-to-gif-using-ffmpeg-with-reasonable-quality
[SO trim]:                    http://superuser.com/questions/556029/how-do-i-convert-a-video-to-gif-using-ffmpeg-with-reasonable-quality
[ffmpeg wiki on capturing]:   https://trac.ffmpeg.org/wiki/Capture/Desktop
[compiling ffmpeg]:           https://trac.ffmpeg.org/wiki/CompilationGuide
[convert result pic]:         /img/2015/10/result.gif
[origin post at v2ex]:             http://v2ex.com/t/226531

