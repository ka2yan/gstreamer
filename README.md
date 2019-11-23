# gstreamer

## Device
Raspberry Pi2  
Camera : Logitech WebCam C270  
Mic : Buauty BU-11  

## Install
`$ sudo apt-get install gstreamer1.0 gstreamer1.0-tools`

## Youtube live
Check youtube live rtmp URL and Stream Key and insert below

```
gst-launch-1.0 -v v4l2src \
    ! video/x-raw,width=1280,height=720,framerate=30/1 \
    ! omxh264enc \
    ! h264parse \
    ! flvmux name=mux \
    alsasrc device=hw:0 ! queue ! audioconvert ! voaacenc ! aacparse ! mux. \
    mux. ! rtmpsink location="rtmp://<youtube_rtmp_url>/<key> live=1 flashver=FME/3.0%20(compatible;%20FMSc%201.0)"
```
- video/x-raw : Webcam C270 doesn't support H.264 encoding, below. use x-raw when you use YUV data.


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


-----------------------
## Facebook Live
Facebook Live requests RTMPS from 2019-11. Encoder cannot send with RTMP)

gstreamer(rtmpsink) support RTMPS. because librtmp supports RTMPS.

```
$ gst-launch-1.0 -v v4l2src \
    ! video/x-raw,width=1280,height=720,framerate=30/1 \
    ! omxh264enc \
    ! h264parse \
    ! flvmux name=mux \
    alsasrc ! audio/x-raw,format=S16LE,layout=interleaved,rate=48000,channels=1 ! queue ! audioconvert ! voaacenc ! aacparse ! mux. \
    mux. ! rtmpsink location="<facebook_rtmps_url>/<key> live=1 flashver=FME/3.0%20(compatible;%20FMSc%201.0)"

```
- <facebook_rtmps_url> : "rtmps://live-api-s.facebook.com:443/rtmp/

## Does gstreamer really use RTMPS ?
I think so.
```
$ cat /proc/<pid>/maps | grep librtmp
75a8a000-75aa1000 r-xp 00000000 b3:07 142169     /usr/lib/arm-linux-gnueabihf/librtmp.so.1
75aa1000-75ab1000 ---p 00017000 b3:07 142169     /usr/lib/arm-linux-gnueabihf/librtmp.so.1
75ab1000-75ab2000 r--p 00017000 b3:07 142169     /usr/lib/arm-linux-gnueabihf/librtmp.so.1
75ab2000-75ab3000 rw-p 00018000 b3:07 142169     /usr/lib/arm-linux-gnueabihf/librtmp.so.1

$ cat /proc/<pid>/maps | grep tls
75977000-75a73000 r-xp 00000000 b3:07 148172     /usr/lib/arm-linux-gnueabihf/libgnutls-deb0.so.28.43.4
75a73000-75a83000 ---p 000fc000 b3:07 148172     /usr/lib/arm-linux-gnueabihf/libgnutls-deb0.so.28.43.4
75a83000-75a88000 r--p 000fc000 b3:07 148172     /usr/lib/arm-linux-gnueabihf/libgnutls-deb0.so.28.43.4
75a88000-75a8a000 rw-p 00101000 b3:07 148172     /usr/lib/arm-linux-gnueabihf/libgnutls-deb0.so.28.43.4

$ cat /proc/<pid>/maps | grep crypt
72358000-723e6000 r-xp 00000000 b3:07 670516     /lib/arm-linux-gnueabihf/libgcrypt.so.20.0.3
723e6000-723f5000 ---p 0008e000 b3:07 670516     /lib/arm-linux-gnueabihf/libgcrypt.so.20.0.3
723f5000-723f6000 r--p 0008d000 b3:07 670516     /lib/arm-linux-gnueabihf/libgcrypt.so.20.0.3
723f6000-723fa000 rw-p 0008e000 b3:07 670516     /lib/arm-linux-gnueabihf/libgcrypt.so.20.0.3

$ netstat -a | grep live
tcp        0      0 <rasberrypi_ip>:36676      livestream-edgete:https ESTABLISHED
```

- librtmp will use gnutls in my raspberry pi.

