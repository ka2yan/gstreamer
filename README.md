# gstreamer

## Device
Raspberry Pi2
Camera : Logitech WebCam C270
Mic : Buauty BU-11

## Install
```
$ sudo apt-get install gstreamer1.0 gstreamer1.0-tools
```

## Youtube live
Check youtube live rtmp URL and Stream Key.

```
gst-launch-1.0 -v v4l2src \
    ! video/x-raw,width=1280,height=960,framerate=30/1 \
    ! omxh264enc \
    ! h264parse \
    ! flvmux name=mux \
    alsasrc device=hw:0 ! queue ! audioconvert ! voaacenc ! aacparse ! mux. \
    mux. ! rtmpsink location="rtmp://<youtube_rtmp_url>/<key> live=1 flashver=FME/3.0%20(compatible;%20FMSc%201.0)"
```


# Device info
```
$ lsusb 
Bus 001 Device 006: ID 0a12:0001 Cambridge Silicon Radio, Ltd Bluetooth Dongle (HCI mode)
Bus 001 Device 005: ID 8086:0808 Intel Corp. 
Bus 001 Device 004: ID 046d:0825 Logitech, Inc. Webcam C270
Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp. SMSC9512/9514 Fast Ethernet Adapter
Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp. 
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

$ v4l2-ctl -d /dev/video0 --list-formats-ext
ioctl: VIDIOC_ENUM_FMT
	Index       : 0
	Type        : Video Capture
	Pixel Format: 'YUYV'
	Name        : YUYV 4:2:2
		Size: Discrete 640x480
			Interval: Discrete 0.033s (30.000 fps)
            ...
        Size: Discrete 1280x720
			Interval: Discrete 0.133s (7.500 fps)
			Interval: Discrete 0.200s (5.000 fps)
		Size: Discrete 1280x960
			Interval: Discrete 0.133s (7.500 fps)
			Interval: Discrete 0.200s (5.000 fps)

	Index       : 1
	Type        : Video Capture
	Pixel Format: 'MJPG' (compressed)
	Name        : Motion-JPEG
		Size: Discrete 640x480
			Interval: Discrete 0.033s (30.000 fps)
            ...
    	Size: Discrete 1280x720
			Interval: Discrete 0.033s (30.000 fps)
			Interval: Discrete 0.040s (25.000 fps)
			Interval: Discrete 0.050s (20.000 fps)
			Interval: Discrete 0.067s (15.000 fps)
			Interval: Discrete 0.100s (10.000 fps)
			Interval: Discrete 0.200s (5.000 fps)
		Size: Discrete 1280x960
			Interval: Discrete 0.033s (30.000 fps)
			Interval: Discrete 0.040s (25.000 fps)
			Interval: Discrete 0.050s (20.000 fps)
			Interval: Discrete 0.067s (15.000 fps)
			Interval: Discrete 0.100s (10.000 fps)
			Interval: Discrete 0.200s (5.000 fps)
```
