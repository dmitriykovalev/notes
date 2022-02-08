# FFmpeg

## ffmpeg

Show available codecs, only encoders, only decoders, container formats, pixel formats:

    ffmpeg -codecs
    ffmpeg -encoders
    ffmpeg -decoders
    ffmpeg -formats
    ffmpeg -pix_fmts

Put raw h264/h265 stream in mp4 container at 10 fps:

    ffmpeg -r 10 -i video.h264_or_h265 -c:v copy -f mp4 output.mp4

Put raw h264/h265 stream in mp4 container at 10 fps using stdin/stdout:

    cat video.h264 | ffmpeg -r 10 -f h264 -i - -c:v copy -movflags frag_keyframe+empty_moov -f mp4 - > output.mp4
    cat video.h265 | ffmpeg -r 10 -f hevc -i - -c:v copy -movflags frag_keyframe+empty_moov -f mp4 - > output.mp4

Extract raw h264 stream from mp4 container:

    ffmpeg -i input.mp4 -vcodec copy -bsf h264_mp4toannexb -f h264 output.h264

Save frames as image files:

    ffmpeg -i input.mp4 -y -f image2 frame%04d.png

Save keyframes as image files:

    ffmpeg -skip_frame nokey -i input.mp4 -vsync 0 -y -f image2 keyframe%04d.png

Save signle frame as image file:

    ffmpeg -ss 1.0 -i input.mp4 -frames:v 1 -f singlejpeg - > frame.jpg

Convert and scale:

    ffmpeg -i input.avi -vf scale=-1:720 -pix_fmt yuv420p -f yuv4mpegpipe output.y4m

Change video container (copy video and audio):

    ffmpeg -i timescapes.webm  -vcodec copy -acodec copy timescapes.avi

Convert yuyv422 to yuv420p:

    ffmpeg -f rawvideo -pix_fmt yuyv422 -s 1280x720 -i video422.yuv \
           -f rawvideo -pix_fmt yuv420p video420.yuv

Calculate PSNR:

    ffmpeg -i stefan.y4m -vf "movie=stefan_cif.y4m [ref], [ref]psnr=stats_file=stats.log" -f rawvideo -y /dev/null

    ffmpeg -i stefan.y4m -vf "movie=stefan_cif.y4m, psnr=stats_file=stats.log" -f rawvideo -y /dev/null

Record video from V4L2 video source (e.g. web camera):

    ffmpeg -f v4l2 -i /dev/video0 out.mp4

Lossless png compression from video.raw to video.avi:

    ffmpeg -f rawvideo -pix_fmt gray -video_size 2048x2048 -i video.raw -vf "vflip" -c:v png video.avi  

Lossless single threaded compression from video.raw to video.avi

    ffmpeg -f rawvideo -pix_fmt gray -video_size 2048x2048 -i video.raw -vf "vflip" -threads 1 -c:v png video.avi  

Decompression from video.avi back to video.raw:

    ffmpeg -i video.avi -f rawvideo -pix_fmt gray -video_size 2048x2048 -vf "vflip" video.raw

Convert YUV frame to PNG:

    ffmpeg -pixel_format nv12 -video_size 1024x768 -i image.yuv -y -f image2 image.png
    
Convert YUV frame to JPEG with specified quality (2-32):

    ffmpeg -pixel_format nv12 -video_size 1024x768 -i image.yuv  -y -f image2 -qscale:v 2 image.jpg
    


## ffprobe

Packet information:

    ffprobe -pretty -unit -show_packets input.mp4

Count video/audio packets:

    ffprobe -show_packets input.mp4 | grep codec_type=video | wc -l

Stream information:

    ffprobe -show_streams input.mp4

Print information in JSON format:

    ffprobe -i input.mp4 -v quiet -of json -show_format -show_streams


## ffplay

Play v4l2 device:

    ffplay -f v4l2 /dev/video0

Play v4l2 device using separate ffmpeg process:

    ffmpeg -f v4l2 -i /dev/video0 -f yuv4mpegpipe -pix_fmt yuv420p - | ffplay -

Play raw yuv formats:

    ffplay -pix_fmt yuyv422 -video_size 1280x720 capture422.yuv
    ffplay -pix_fmt yuv420p -video_size 1280x720 capture420.yuv

Display two videos side by side:

    ffplay stefan.y4m -vf "[in]pad=iw*2:ih[left];movie=stefan_cif.y4m[right];[left][right]overlay=w"

Play cropped video:

    ffplay -vf "crop=3840:2160:0:0" input.mp4
    
Play raw video with vertical flip:

    ffplay -f rawvideo -pix_fmt gray -video_size 2048x2048 -vf "vflip" video.raw
    
Play raw bayer video:

    ffplay -f rawvideo -pix_fmt bayer_rggb8 -video_size 2048x2048  video.raw

Display single raw YUV frame:

    ffplay -pix_fmt nv12 -video_size 1024x768 frame.yuv

Display single 16-bit RAW frame as a grayscale image:

    ffplay -pix_fmt gray16le -video_size 1024x768 frame.raw
 
