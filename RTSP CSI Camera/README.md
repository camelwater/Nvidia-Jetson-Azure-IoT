```
# cd to the current user home - admin 
$ cd projects 
$ mkdir rtsp_server
$ cd rtsp_server
$ git clone https://github.com/GStreamer/gst-rtsp-server.git
$ sudo apt-get install libgstrtspserver-1.0 libgstreamer1.0-dev
#cd to the examples folder
$ gcc test-launch.c -o test-launch $(pkg-config --cflags --libs gstreamer-1.0 gstreamer-rtsp-server-1.0)
#might need comment out this line:
#gst_rtsp_media_factory_set_enable_rtcp (factory, !disable_rtcp);
$ ./test-launch "nvarguscamerasrc ! nvvidconv ! nvv4l2h264enc ! h264parse ! rtph264pay name=pay0 pt=96"
```

