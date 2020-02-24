Tested in Ubuntu 18.04 and Python 3.6

### Create Python virtual environment if you want
```
virtualenv venv
source venv/bin/activate
```


### Install Gstreamer dependencies
```
sudo apt-get install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-doc gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio
```
(source:)[https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c]


### Link Gstreamer packages to virtual environment in Linux
If you are using virtual environment in Linux, link the gi Python package from system's Python to the virtual environment's Python 
[Gist](https://gist.github.com/jegger/10003813)
```
cd venv/lib/python3.6/site-packages
ln -s /usr/lib/python3/dist-packages/gi
cd ../../../..
```


---
### Install dependencies
```
pip install -r requirements.txt
```