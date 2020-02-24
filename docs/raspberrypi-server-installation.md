Tested with a clean install of 2019-09-26-raspbian-buster-lite.zip on Raspberry Pi 2 Model B


---
## Setting up
```
sudo apt update && sudo apt upgrade
```

```
sudo raspi-config
```
In Raspi-Config change the following:
- 7 Advanced Options -> A1 Expand Filesystem
- 7 Advanced Options -> A3 Memory Split -> 256MB (This might help Rpi camera can handle 3280 x 2464 resolution)
- 5 Interfacing Options -> P1 Camera -> Enable Camera


---
## The server (Raspberry Pi) installation
### Install GStreamer
```
sudo apt install gstreamer1.0-tools \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```


---
### Test video streaming
#### Start GStreamer on the Raspberry Pi and send stream to one client
```
raspivid -n -t 0 -w 1640 -h 1232 -fps 40 -b 1700000 -o - | gst-launch-1.0 -v fdsrc ! h264parse ! rtph264pay config-interval=1 pt=96 ! udpsink host=[CLIENT-IP] port=5200
```
Replace the `[CLIENT-IP]` with the IP-address of the client receiving the UDP video stream. Make sure any firewall or router is not blocking UDP protocol to clients port 5200. You can change the port to be what ever you want.

#### Send to multiple clients
```
raspivid -t 999999 -h 480 -w 640 -fps 40 -b 1700000 -o - | gst-launch-0.10 -v fdsrc fd=0 ! h264parse ! rtph264pay ! multiudpsink clients=[CLIENT1-IP]:[CLIENT1-PORT],[CLIENT2-IP]:[CLIENT2-PORT]
```



---
## Optional streaming method. Install Raspberry Pi camera as GStreamer source, gst-rpicamsrc plugin
(source:)[https://github.com/thaytan/gst-rpicamsrc]

```
sudo apt install autoconf automake libtool pkg-config libgstreamer1.0-dev
sudo apt install libgstreamer-plugins-base1.0-dev libraspberrypi-dev

sudo apt install git
git clone https://github.com/thaytan/gst-rpicamsrc.git
cd gst-rpicamsrc/
./autogen.sh –prefix=/usr –libdir=/usr/lib/arm-linux-gnueabihf/
make
sudo make install

sudo nano /etc/ld.so.conf

added line "include [PATH-TO-gst-rpicamsrc-FOLDER]" to /etc/ld.so.conf without quotes
```

You can now send the video stream with the following command
```
gst-launch-1.0 rpicamsrc bitrate=1700000 sensor-mode=4 ! h264parse ! rtph264pay config-interval=1 pt=96 ! udpsink host=[CLIENT-IP] port=5200
```
Some sources say using rpicamsrc should be a bit faster than using raspivid and the pipe command (The "|" sign) in the earlier command. The latency in my setup was about the same with both commands. Using the rpicamsrc was maybe something like 10-20ms faster but due to the latency measurement being not that accurate I can't say that for sure.

You can run command `gst-inspect-1.0 rpicamsrc` to find all options you can give to the rpicamsrc-module. The rpicamsrc-module uses raspivid and the available options passed by the rpicamsrc to the raspivid can be found from [here](https://www.raspberrypi.org/documentation/raspbian/applications/camera.md)


---
### Rapsberry Pi camera info
https://www.raspberrypi.org/documentation/raspbian/applications/camera.md

https://www.raspberrypi.org/documentation/hardware/camera/