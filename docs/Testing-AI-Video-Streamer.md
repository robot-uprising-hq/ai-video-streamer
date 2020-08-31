# Testing AI Video Streamer

This document shows four ways to send and receive video stream from Raspberry Pi Gstreamer

---

## 1. Sending to multicast IP-address
Your router should support multicasting for this to work. In this example the multicast IP-address is 224.1.1.1 but in your router it might be a different IP-address.

### Raspberry Pi sending stream
```sh
raspivid -w 1080 -h 1080 -b 25000000 -cd MJPEG -fps 40 -n -t 0 -o - | gst-launch-1.0 -v fdsrc ! "image/jpeg,width=1080,height=1080",framerate=40/1 ! jpegparse ! rtpjpegpay ! udpsink host=224.1.1.1 auto-multicast=true port=5200
```

### Client receiving the stream
```sh
gst-launch-1.0 udpsrc multicast-group=224.1.1.1 auto-multicast=true port=5200 ! application/x-rtp,encoding-name=JPEG,payload=26 ! rtpjpegdepay ! jpegdec ! autovideosink
```

---

## 2. Sending to multiple predefined IP-addresses
### Raspberry Pi sending stream
```sh
raspivid -n -t 0 -w 1640 -h 1232 -fps 40 -b 1700000 -o - | gst-launch-1.0 -v fdsrc ! h264parse ! rtph264pay config-interval=1 pt=96 ! multiudpsink clients=[CLIENT-IP-WITHOUT-THESE-BRACKETS]:[PORT-WITHOUT-THESE-BRACKETS]
```

### Client receiving the stream
```sh
gst-launch-1.0 udpsrc port=5200 ! application/x-rtp, payload=96 ! rtpjitterbuffer ! rtph264depay ! avdec_h264 ! autovideosink sync=false text-overlay=false
```

---

## 3. Sending to one predefined IP-address
### Raspberry Pi sending stream
```sh
raspivid -n -t 0 -w 1640 -h 1232 -fps 40 -b 1700000 -o - | gst-launch-1.0 -v fdsrc ! h264parse ! rtph264pay config-interval=1 pt=96 ! udpsink host=[CLIENT-IP-WITHOUT-THESE-BRACKETS] port=5200
```

### Client receiving the stream
```sh
gst-launch-1.0 udpsrc port=5200 ! application/x-rtp, payload=96 ! rtpjitterbuffer ! rtph264depay ! avdec_h264 ! autovideosink sync=false text-overlay=false
```

---

## 4. Sending to one predefined IP-address and receiving with Python script
### Raspberry Pi sending stream
```sh
raspivid -n -t 0 -w 1640 -h 1232 -fps 40 -b 1700000 -o - | gst-launch-1.0 -v fdsrc ! h264parse ! rtph264pay config-interval=1 pt=96 ! udpsink host=[CLIENT-IP-WITHOUT-THESE-BRACKETS] port=5200
```

### Client receiving the stream
```sh
python udp_stream-client.py
```

---

## Development
Some sources say using rpicamsrc should be a bit faster than using raspivid and the pipe command (The "|" sign) in the earlier command. The latency in tested setup was about the same with both commands. Using the rpicamsrc was maybe something like 10-20ms faster but due to the latency measurement being not that accurate I can't say that for sure.

You can run command `gst-inspect-1.0 rpicamsrc` to find all options you can give to the rpicamsrc-module. The rpicamsrc-module uses raspivid and the available options passed by the rpicamsrc to the raspivid can be found from [here](https://www.raspberrypi.org/documentation/raspbian/applications/camera.md)
