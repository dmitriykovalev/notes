# FFmpeg

## ffmpeg

Show available codecs, only encoders, only decoders, container formats, pixel formats:
```shell
ffmpeg -codecs
ffmpeg -encoders
ffmpeg -decoders
ffmpeg -formats
ffmpeg -pix_fmts
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
