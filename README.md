# gstreamer

gst-launch-1.0 -v v4l2src \
    ! video/x-raw,width=1280,height=960,framerate=30/1 \
    ! omxh264enc \
    ! h264parse \
    ! flvmux name=mux \
    alsasrc device=hw:0 ! queue ! audioconvert ! voaacenc ! aacparse ! mux. \
    mux. ! rtmpsink location="rtmp://<youtube_rtmp_url>/<key> live=1 flashver=FME/3.0%20(compatible;%20FMSc%201.0)"
