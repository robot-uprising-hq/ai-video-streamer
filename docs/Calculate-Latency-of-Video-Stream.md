### Video stream latency calculation
To get the latency of the video stream point the Raspberry Pi camera to your PC's monitor and have a stop watch (any online stop watch, search for one) also on the monitor. Then take images of your monitor with your mobile phone. If possible use the "pro" mode of your camera to set the ISO-setting to a big value and the shutter speed to a small one. This makes the image a bit dark but you get sharp images of the fast changing millisecond numbers on the stop watch.

| ![Setup](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/raw/master/docs/images/latency-detection-setup.jpg) | ![Latency](https://gitlab.com/jsalli/robot-uprising-ai-raspberry-pi-video-streamer/-/raw/master/docs/images/latency_image.jpg) |
|:---:|:---:|
| Setup to calculate latency. Raspicam pointing to a monitor which has online stop watch and the streamed video on it. | Image of the latency. Substract the time of last streamed image (22:25:90) from the time of current stop watch time (22:26:10). That gives us 200ms for latency. |

With Raspberry Pi 2B and a i7-3770 CPU @ 3.40GHz × 8 client the latency is about from 160ms to 220ms with gst-launch-1.0 command and couple of tens of milliseconds more with the Python script.