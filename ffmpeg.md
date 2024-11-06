# FFmpeg

## ffmpeg

Show available codecs, only encoders, only decoders, container formats, pixel formats:
```shell
ffmpeg -codecs
ffmpeg -encoders
ffmpeg -decoders

ffmpeg -formats
ffmpeg -pix_fmts
ffmpeg -sample_fmts

ffmpeg -muxers
ffmpeg -demuxers
```

Convert video to multiple images:
```shell
# Save frames as image files
ffmpeg -i input.mp4 -y -f image2 frame%04d.png

# Save keyframes as image files
ffmpeg -i input.mp4 -skip_frame nokey -vsync 0 -y -f image2 keyframe%04d.png

# Save a signle frame at 1.0s as an image file
ffmpeg -ss 1.0 -i input.mp4 -frames:v 1 -f singlejpeg - > frame.jpg
```

Convert multiple images to video:
```shell
# Put image files in `avi` container at 10 fps starting from IMG_0495.JPG:
ffmpeg -f image2 -start_number 495 -r 10 -i IMG_%4d.JPG -c:v copy -f avi output.avi
```

Manipulate h264/h265 raw bitstream:
```shell
# Put raw h264/h265 stream in mp4 container at 10 fps
ffmpeg -r 10 -i video.h264_or_h265 -c:v copy -f mp4 output.mp4

# Put raw h264/h265 stream in mp4 container at 10 fps using stdin/stdout
cat video.h264 | ffmpeg -r 10 -f h264 -i - -c:v copy -movflags frag_keyframe+empty_moov -f mp4 - > output.mp4
cat video.h265 | ffmpeg -r 10 -f hevc -i - -c:v copy -movflags frag_keyframe+empty_moov -f mp4 - > output.mp4

# Extract raw h264 stream from mp4 container:
ffmpeg -i input.mp4 -vcodec copy -bsf h264_mp4toannexb -f h264 output.h264
```

Convert video:
```shell
# Convert and scale input.avi to output.y4m
ffmpeg -i input.avi -vf scale=-1:720 -pix_fmt yuv420p -f yuv4mpegpipe output.y4m

# Change video container (copy video and audio):
ffmpeg -i video.webm  -vcodec copy -acodec copy video.avi

# Convert yuyv422 to yuv420p:
ffmpeg -f rawvideo -pix_fmt yuyv422 -s 1280x720 -i video422.yuv \
       -f rawvideo -pix_fmt yuv420p video420.yuv

# Convert nv12 to mjpeg (mp4 or avi):
ffmpeg -f rawvideo -pixel_format nv12 -video_size 1920x1080 -i video.yuv -vcodec mjpeg output.mp4
ffmpeg -f rawvideo -pixel_format nv12 -video_size 1920x1080 -i video.yuv -vcodec mjpeg output.avi
ffmpeg -f rawvideo -pixel_format nv12 -video_size 1920x1080 -i video.yuv -vf extractplanes=y -vcodec mjpeg output.mp4

# Convert nv12 to mpeg4 (mp4):
ffmpeg -f rawvideo -pixel_format nv12 -video_size 1920x1080 -i video.yuv -vcodec mpeg4 output.mp4

# Lossless png compression from video.raw to video.avi:
ffmpeg -f rawvideo -pix_fmt gray -video_size 2048x2048 -i video.raw -vf "vflip" -c:v png video.avi  

# Lossless single threaded png compression from video.raw to video.avi
ffmpeg -f rawvideo -pix_fmt gray -video_size 2048x2048 -i video.raw -vf "vflip" -threads 1 -c:v png video.avi  

# Decompression from video.avi back to video.raw:
ffmpeg -i video.avi -f rawvideo -pix_fmt gray -video_size 2048x2048 -vf "vflip" video.raw
```
 
Convert raw YUV images:
```shell
# Convert YUV image to PNG:
ffmpeg -pixel_format nv12 -video_size 1024x768 -i image.yuv -y -f image2 image.png

# Save Y/U/V plane from YUV image to PNG
ffmpeg -pixel_format nv12 -video_size 1920x1080 -i image.yuv -filter_complex extractplanes=y -y -f image2 y.png
ffmpeg -pixel_format nv12 -video_size 1920x1080 -i image.yuv -filter_complex extractplanes=u -y -f image2 y.png
ffmpeg -pixel_format nv12 -video_size 1920x1080 -i image.yuv -filter_complex extractplanes=v -y -f image2 y.png

# Convert YUV image to JPEG with specified quality (2-32):
ffmpeg -pixel_format nv12 -video_size 1024x768 -i image.yuv -y -f image2 -qscale:v 2 image.jpg
```

Calculate PSNR:
```shell
ffmpeg -i stefan.y4m -vf "movie=stefan_cif.y4m [ref], [ref]psnr=stats_file=stats.log" -f rawvideo -y /dev/null
ffmpeg -i stefan.y4m -vf "movie=stefan_cif.y4m, psnr=stats_file=stats.log" -f rawvideo -y /dev/null
```

Record video from V4L2 video source (e.g. web camera):
```shell
ffmpeg -f v4l2 -i /dev/video0 out.mp4
```

Audio processing:
```shell
# Convert audio file to wav (1 channel, 16000 Hz sample rate, 2 bytes per sample):
ffmpeg -i input.mp3 -ac 1 -ar 16000 -c pcm_s16le output.wav
```

## ffprobe

Packet information:
```shell
ffprobe -pretty -unit -show_packets input.mp4
```

Count video/audio packets:
```shell
ffprobe -show_packets input.mp4 | grep codec_type=video | wc -l
```

Stream information:
```shell
ffprobe -show_streams input.mp4
```

Print information in JSON format:
```shell
ffprobe -i input.mp4 -v quiet -of json -show_format -show_streams
```

## ffplay

Play V4L2 device:
```shell
ffplay -f v4l2 /dev/video0
```

Play V4L2 device using separate ffmpeg process:
```shell
ffmpeg -f v4l2 -i /dev/video0 -f yuv4mpegpipe -pix_fmt yuv420p - | ffplay -
```

Play raw YUV formats:
```shell
ffplay -pix_fmt yuyv422 -video_size 1280x720 capture422.yuv
ffplay -pix_fmt yuv420p -video_size 1280x720 capture420.yuv
```

Display two videos side by side:
```shell
ffplay stefan.y4m -vf "[in]pad=iw*2:ih[left];movie=stefan_cif.y4m[right];[left][right]overlay=w"
```

Play cropped video:
```shell
ffplay -vf "crop=3840:2160:0:0" input.mp4
```
    
Play raw video with vertical flip:
```shell
ffplay -f rawvideo -pix_fmt gray -video_size 2048x2048 -vf "vflip" video.raw
```
    
Play raw bayer video:
```shell
ffplay -f rawvideo -pix_fmt bayer_rggb8 -video_size 2048x2048  video.raw
```

Display single raw YUV frame:
```shell
ffplay -pix_fmt nv12 -video_size 1024x768 frame.yuv
```

Display single 16-bit RAW frame as a grayscale image:
```shell
ffplay -pix_fmt gray16le -video_size 1024x768 frame.raw
```

PCM audio formats:
```console
$ ffplay -hide_banner -formats | grep PCM
 DE  alaw            PCM A-law
 DE  f32be           PCM 32-bit floating-point big-endian
 DE  f32le           PCM 32-bit floating-point little-endian
 DE  f64be           PCM 64-bit floating-point big-endian
 DE  f64le           PCM 64-bit floating-point little-endian
 DE  mulaw           PCM mu-law
 DE  s16be           PCM signed 16-bit big-endian
 DE  s16le           PCM signed 16-bit little-endian
 DE  s24be           PCM signed 24-bit big-endian
 DE  s24le           PCM signed 24-bit little-endian
 DE  s32be           PCM signed 32-bit big-endian
 DE  s32le           PCM signed 32-bit little-endian
 DE  s8              PCM signed 8-bit
 DE  u16be           PCM unsigned 16-bit big-endian
 DE  u16le           PCM unsigned 16-bit little-endian
 DE  u24be           PCM unsigned 24-bit big-endian
 DE  u24le           PCM unsigned 24-bit little-endian
 DE  u32be           PCM unsigned 32-bit big-endian
 DE  u32le           PCM unsigned 32-bit little-endian
 DE  u8              PCM unsigned 8-bit
 DE  vidc            PCM Archimedes VIDC
```
Additional `s16le` options:
```console
$ ffplay -hide_banner -help demuxer=s16le
Demuxer s16le [PCM signed 16-bit little-endian]:
    Common extensions: sw.
pcm demuxer AVOptions:
  -sample_rate       <int>        .D.........  (from 0 to INT_MAX) (default 44100)
  -ch_layout         <channel_layout> .D.........  (default "mono")
```

Play raw audio:
```shell
$ ffplay -f s16le -sample_rate 44100 -ch_layout mono   mono.raw
$ ffplay -f s16le -sample_rate 48000 -ch_layout stereo stereo.raw
```

FFmpeg devices:
```console
$ ffmpeg -hide_banner -devices
Devices:
 D. = Demuxing supported
 .E = Muxing supported
 ---
  E audiotoolbox    AudioToolbox output device
 D  avfoundation    AVFoundation input device
 D  lavfi           Libavfilter virtual input device
  E sdl,sdl2        SDL2 output device
 D  x11grab         X11 screen capture, using XCB
```

Play on macOS via [audiotoolbox](https://github.com/FFmpeg/FFmpeg/blob/master/libavdevice/audiotoolbox.m#L289):
```shell
# List output devices 
ffmpeg -hide_banner -i audio.wav -f audiotoolbox  -list_devices true -

# Default output device
ffmpeg -hide_banner -i audio.wav -f audiotoolbox -

# Output device #0
ffmpeg -hide_banner -i audio.wav -f audiotoolbox 0
ffmpeg -hide_banner -i audio.wav -f audiotoolbox -audio_device_index 0 -

# Output device #2
ffmpeg -hide_banner -i audio.wav -f audiotoolbox 2
ffmpeg -hide_banner -i audio.wav -f audiotoolbox -audio_device_index 2 -
```

Record on macOS via [avfoundation](https://github.com/FFmpeg/FFmpeg/blob/master/libavdevice/avfoundation.m#L1275):
```shell
# List input devices
ffmpeg -f avfoundation -list_devices true -i ""

# Record
ffmpeg -f avfoundation -i ":1" record.wav
```
