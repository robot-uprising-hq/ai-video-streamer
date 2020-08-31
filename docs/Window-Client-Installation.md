# Windows client installation

1. Create Python virtual environment
    ```
    python -m venv venv
    venv\Scripts\activate.bat
    ```
1. Go to https://gstreamer.freedesktop.org/data/pkg/windows/ and choose the latest version packages. Download both the developer (devel) package and the default package.
Example: gstreamer-1.0-mingw-x86-1.16.2.msi
         gstreamer-1.0-devel-mingw-x86-1.16.2.msi
         (08-03-2020)
1. Install the downloaded gstreamer packages, during installation, choose the **COMPLETE** package.
1. Run these command prompt lines:
    ```sh
    cd C :\gstreamer\1.0\x86_64\bin
    ```
1. Search for the `gst-launch-1.0.exe `file and go to that directory in the command prompt. 
1. Run a test:
    ```sh
    gst-launch-1.0.exe videotestsrc ! autovideosink
    ```
