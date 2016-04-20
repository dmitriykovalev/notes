# FFmpeg

## ffmpeg

Put raw h264 stream in mp4 container at 10 fps:

    ffmpeg -r 10 -i video.h264 -c:v copy -f mp4 output.mp4

Put raw h264 stream in mp4 container at 10 fps using stdin/stdout:

    cat video.h264 | ffmpeg -r 10 -f h264 -i - -c:v copy -f mp4 - > output.mp4

Save frames as image files:

    ffmpeg -i input.mp4 -y -f image2 frame%04d.png

Save signle frame as image file:

    ffmpeg -ss 1.0 -i input.mp4 -frames:v 1 -f singlejpeg - > frame.jpg

Convert and scale:

    ffmpeg -i input.avi -vf scale=-1:720 -pix_fmt yuv420p -f yuv4mpegpipe output.y4m

Change video container (copy video and audio):

    ffmpeg -i timescapes.webm  -vcodec copy -acodec copy timescapes.avi

Convert yuyv422 to yuv420p:

    ffmpeg -f rawvideo -pix_fmt yuyv422 -s 1280x720 -i video422.yuv \
           -f rawvideo -pix_fmt yuv420p video420.yuv

Play raw yuv formats:

    ffplay -pixel_format yuyv422 -video_size 1280x720 capture422.yuv
    ffplay -pixel_format yuv420p -video_size 1280x720 capture420.yuv

Display two videos side by side:

    ffplay stefan.y4m -vf "[in]pad=iw*2:ih[left];movie=stefan_cif.y4m[right];[left][right]overlay=w"

Calculate PSNR:

    ffmpeg -i stefan.y4m -vf "movie=stefan_cif.y4m [ref], [ref]psnr=stats_file=stats.log" -f rawvideo -y /dev/null

    ffmpeg -i stefan.y4m -vf "movie=stefan_cif.y4m, psnr=stats_file=stats.log" -f rawvideo -y /dev/null

Record video from V4L2 video source (e.g. web camera):

    ffmpeg -f v4l2 -i /dev/video0 out.mp4

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

Play cropped video:

    ffplay -vf "crop=3840:2160:0:0" input.mp4
