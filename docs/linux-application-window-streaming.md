Tested on Ubuntu 18.04

[One source of information](http://www.einarsundgren.se/gstreamer-basic-real-time-streaming-tutorial/)

## Streaming application window as UDP video stream

### 1) Find application window ID

Run the following command and then click on the application window you want to stream. The command shows you the application window's information.
```
xwininfo
```

This is an example print:
```
ubuntuman@ubuntuman:~$ xwininfo

xwininfo: Please select the window about which you
          would like information by clicking the
          mouse in that window.

xwininfo: Window id: 0x400000b "ArtificialInvaders"

  Absolute upper-left X:  215
  Absolute upper-left Y:  98
  Relative upper-left X:  10
  Relative upper-left Y:  38
  Width: 537
  Height: 463
  Depth: 24
  Visual: 0x4f
  Visual Class: DirectColor
  Border width: 0
  Class: InputOutput
  Colormap: 0x400000a (not installed)
  Bit Gravity State: ForgetGravity
  Window Gravity State: NorthWestGravity
  Backing Store State: NotUseful
  Save Under State: no
  Map State: IsViewable
  Override Redirect State: no
  Corners:  +215+98  -3088+98  -3088-639  +215-639
  -geometry 537x463+205+60
```
Window id in the above example is 0x400000b


### 2) Test applicatiion capturing
```
gst-launch-1.0 ximagesrc xid=0x400000b use-damage=0 ! video/x-raw,framerate=30/1 ! videoscale method=0 ! video/x-raw,width=640,height=480  ! ximagesink
```

### 3) Stream application window as UDP stream to OpenCV Python script
Start client receiving the UDP stream
```
gst-launch-1.0 udpsrc port=5200 ! application/x-rtp, payload=96 ! rtpjitterbuffer ! rtph264depay ! avdec_h264 ! autovideosink sync=false text-overlay=false
```


```
gst-launch-1.0 ximagesrc xid=0x400000b use-damage=0 ! video/x-raw,framerate=30/1 ! videoconvert ! x264enc tune=zerolatency bitrate=500 speed-preset=superfast ! h264parse ! rtph264pay config-interval=1 pt=96 ! udpsink host=127.0.0.1 port=5200
```



gst-launch-1.0 ximagesrc xid=0x2600446 use-damage=0 ! video/x-raw,framerate=30/1 ! videoconvert ! x264enc tune=zerolatency bitrate=500 speed-preset=superfast ! h264parse ! rtph264pay config-interval=1 pt=96 ! udpsink host=127.0.0.1 port=5200

gst-launch-1.0 ximagesrc xid=0x2600446 use-damage=0 ! video/x-raw,framerate=30/1 ! videoconvert ! x264enc tune=zerolatency bitrate=500 speed-preset=superfast ! rtph264pay config-interval=1 pt=96 ! udpsink host=127.0.0.1 port=5200

gst-launch-1.0 videotestsrc ! videobalance saturation=0 ! x264enc ! video/x-h264, stream-format=byte-stream ! rtph264pay ! udpsink host=127.0.0.1 port=5200


### RPi command to send
raspivid -n -t 0 -w 1640 -h 1232 -fps 40 -b 1700000 -o - | gst-launch-1.0 -v fdsrc ! h264parse ! rtph264pay config-interval=1 pt=96 ! udpsink host=[CLIENT-IP] port=5200
gst-launch-1.0 rpicamsrc bitrate=1700000 sensor-mode=4 ! h264parse ! rtph264pay config-interval=1 pt=96 ! udpsink host=[CLIENT-IP] port=5200
### Test shwoing
gst-launch-1.0 ximagesrc xid=0x2600446 use-damage=0 ! video/x-raw,framerate=30/1 ! videoscale method=0 ! video/x-raw,width=640,height=480  ! ximagesink
### MP4 Saving works
gst-launch-1.0 ximagesrc xid=0x2600446 use-damage=0 ! video/x-raw,framerate=30/1 ! videoconvert ! x264enc ! mp4mux ! filesink location=desktop.mp4 -e
### Showing MP4 works
gst-launch-1.0 filesrc location=desktop.mp4 ! qtdemux name=demux ! queue ! avdec_h264 ! videoconvert ! autovideosink
### Sending MP4 UDP works
gst-launch-1.0 filesrc location=desktop.mp4 ! qtdemux ! queue ! rtph264pay ! udpsink host=192.168.10.56 port=5200
gst-launch-1.0 udpsrc port=5200 ! application/x-rtp, payload=96 ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! autovideosink 
### Sending window UDP h264 stream works, 400ms latency and image corrupts in 10 secs
gst-launch-1.0 ximagesrc xid=0x2600446 use-damage=0 ! video/x-raw,framerate=30/1 ! videoconvert ! x264enc tune=zerolatency ! rtph264pay ! udpsink host=192.168.10.56 port=5200
gst-launch-1.0 ximagesrc xid=0x2600446 use-damage=0 ! video/x-raw,framerate=30/1 ! videoconvert ! x264enc tune=zerolatency bitrate=1024 quantizer=5 ! rtph264pay config-interval=1 pt=96 ! udpsink host=192.168.10.56 port=5200
gst-launch-1.0 udpsrc port=5200 ! application/x-rtp, payload=96 ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! autovideosink 



### TOIMII NOPEA
gst-launch-1.0 ximagesrc use-damage=0 ! video/x-raw,framerate=30/1 ! videoscale ! videoconvert ! x264enc tune=zerolatency bitrate=500 speed-preset=superfast ! rtph264pay ! udpsink host=127.0.0.1 port=5200
gst-launch-1.0 udpsrc port=5200 caps = "application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H264, payload=(int)96" ! rtph264depay ! decodebin ! videoconvert ! autovideosink
