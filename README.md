## Raspberry Pi video streaming
Repo to get video from Raspberry Pi with a Raspberry Pi Camera module V2 with a low latency. Use ethernet cable connection between Raspberry Pi and Client PC to minimize the latency of the video stream.

Original Python code from [here](https://gist.github.com/patrickelectric/443645bb0fd6e71b34c504d20d475d5a)

---
### Install Gstreamer to client
This computer is the client receiving the video stream.
Instructions for [Linux](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/blob/master/docs/linux-client-installation.md)

Instructions for [Windows](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/blob/master/docs/window-client-installation.md)


---
### Setup Raspberry Pi
See the instructions from [raspberrypi-server-installation.md](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/blob/master/docs/raspberrypi-server-installation.md)


---
### Test GStreamer command to see video stream
Make sure Raspberry Pi is sending stream video and then run this command
```
gst-launch-1.0 udpsrc port=5200 ! application/x-rtp, payload=96 ! rtpjitterbuffer ! rtph264depay ! avdec_h264 ! autovideosink sync=false text-overlay=false
```
You should see a live video stream


---
### Run Python script showing video stream with OpenCV
```
python udp_stream-client.py
```


---
### Video stream latency calculation
To get the latency of the video stream point the Raspberry Pi camera to your PC's monitor and have a stop watch (any online stop watch, search for one) also on the monitor. Then take images of your monitor with your mobile phone. If possible use the "pro" mode of your camera to set the ISO-setting to a big value and the shutter speed to a small one. This makes the image a bit dark but you get sharp images of the fast changing millisecond numbers on the stop watch.

| ![Setup](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/raw/master/docs/images/latency-detection-setup.jpg) | ![Latency](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/raw/master/docs/images/latency_image.jpg) |
|:---:|:---:|
| Setup to calculate latency. Raspicam pointing to a monitor which has online stop watch and the streamed video on it. | Image of the latency. Substract the time of last streamed image (22:25:90) from the time of current stop watch time (22:26:10). That gives us 200ms for latency. |

With Raspberry Pi 2B and a i7-3770 CPU @ 3.40GHz × 8 client the latency is about from 160ms to 220ms with gst-launch-1.0 command and couple of tens of milliseconds more with the Python script.


---
## Streaming application window

Instructions for [Linux](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/blob/master/docs/linux-application-window-streaming.md)

Instructions for [Windows](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/blob/master/docs/windows-application-window-streaming.md)


