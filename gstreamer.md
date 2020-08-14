# GStreamer

Play video file:
```
DISPLAY=:0 gst-launch-1.0 playbin uri=https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_5mb.mp4
```

Save raw h264 frames from video:
```
gst-launch-1.0 filesrc location=test.mp4 ! qtdemux ! h264parse ! multifilesink location="frame%d.bin"
```

Save single frame from camera:
```
gst-launch-1.0 v4l2src device=/dev/video0 num-buffers=1 ! jpegenc ! filesink location=test.jpg
```

Play video from camera using Wayland protocol:
```
gst-launch-1.0 v4l2src ! 'video/x-raw,width=1280,height=720,framerate=30/1' ! waylandsink
```
