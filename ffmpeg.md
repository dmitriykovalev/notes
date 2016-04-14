FFmpeg
======

Convert and scale:

    ffmpeg -i input.avi -vf scale=-1:720 -pix_fmt yuv420p -f yuv4mpegpipe output.y4m

Change video container (copy video and audio)

    ffmpeg -i timescapes.webm  -vcodec copy -acodec copy timescapes.avi

Count video/audio packets:

    ffprobe -show_packets timescapes.webm | grep codec_type=video | wc -l

Convert yuyv422 to yuv420p:

    ffmpeg -f rawvideo -pix_fmt yuyv422 -s 1280x720 -i capture-1000.yuv \
           -f rawvideo -pix_fmt yuv420p capture-1000-original.yuv

Play raw yuv formats:

    ffplay -pixel_format yuyv422 -video_size 1280x720 capture-1000.yuv
    ffplay -pixel_format yuv420p -video_size 1280x720 capture-1000.ivf.yuv

Packet information:

    ffprobe -pretty -unit -show_packets timescapes.webm

Stream information:

    ffprobe -show_streams timescapes.webm


Display two Videos Side by Side:

    ffplay stefan.y4m -vf "[in]pad=iw*2:ih[left];movie=stefan_cif.y4m[right];[left][right]overlay=w"

Measure PSNR:

    ffmpeg -i stefan.y4m -vf "movie=stefan_cif.y4m [ref], [ref]psnr=stats_file=stats.log" -f rawvideo -y /dev/null

    ffmpeg -i stefan.y4m -vf "movie=stefan_cif.y4m, psnr=stats_file=stats.log" -f rawvideo -y /dev/null

Record video from video camera:

    ffmpeg -f v4l2 -i /dev/video0 out.mp4


