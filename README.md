# Steps
- start with this readme
- then go to deepstream folder
  1. deepstream/Intro
  2. deepstream/Lesson 1
  3. deepstream/Lesson 2
  4. deepstream/Lesson 3
  5. deepstream/Lesson 4
  6. deepstream/Lesson 5
# Nvidia Jetson Nano & IoT
## Hardware
- [Nvidia Jetson nano developer kit](https://www.amazon.com/gp/product/B07PZHBDKT/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1) 
  - Nano board
  
 ![JetsonNanoBoard](https://user-images.githubusercontent.com/73182196/134941562-f46924e9-57db-41ad-beeb-c458e901ece0.PNG)

- [Samsung 128 GB microSD card](https://www.amazon.com/gp/product/B06XWZWYVP/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1)
- [Nvidia Jetson nano build case](https://www.amazon.com/gp/product/B07SXJHQD1/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1)
- [CSI camera](https://www.amazon.com/gp/product/B07TRFH72B/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1)
- [USB WiFi adapter](https://www.amazon.com/gp/product/B08F2ZNC6J/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1)
- Wireless Keyboard and Mourse
- HDMI cable 
- External monitor 
## Software
- [Nvidia Jetson Image](https://developer.nvidia.com/embedded/downloads)
- [Nvidia Jetson SDK manager](https://developer.nvidia.com/nvidia-sdk-manager)
- [Deepstream 4.0 & 5.1](https://developer.nvidia.com/deepstream-getting-started)
- [Tiny YOLO](https://github.com/ultralytics/yolov5)

## Study Diary
### Deepstream
3/27/2021
**dGpu - discrete GPU**
- did some google search; no clear answer but looks like some GeForce “Maxwell” based GPU with 1GB dedicated GDDR5 RAM for non gaming scenario
- More like it is iGPU vs dGPU scenario; mainly for integrated vs discrete

DeepStream SDK
- Based on open source GStreamer multimedia framework
- Supported by Jetson module and Nvidia dGPU adapter

**GStreamer**
- A framework for plugins, data flow, and media type handling/negotiation
- It is used to create streaming media applications. 
- Plugins are shared libraries that are dynamically loaded at runtime and can be extended and upgraded independently. 
- When arranged and linked together, plugins form the processing pipeline that defines the data flow for a streaming media application
- Open source plugins
  - GstFileSrc - Read data from a file: video data or images.
  - GstH264Parse - Parse the incoming H264 stream. For H265 codec, use H265Parse.
  - GstRtpH264Pay - Convert H264 encoded payload to RTP packets (RFC 3984).
  - GstUDPSink - Send UDP packets to the network. When paired with RTP payloader (GstRtpH264Pay) it can implement RTP streaming.
  - GstCapsFilter - Enforce limitations on data format without modifying data.
  - GstV4l2Src - Capture video from v4l2 devices.
  - GstQTMux - Merge streams (audio and video) into QuickTime(.mov) files.
  - GstFileSink - Write incoming data to a file in the local file system.
  - GstURIDecodeBin - Decode data from a URI into raw media. It selects a source element that can handle the given "uri" scheme and connects it to a decodebin.
- Hardware accelerators:
  - Gst-nvstreammux - Batch streams before sending for AI inference.
  - Gst-nvinfer - Run inference using TensorRT.
  - Gst-nvvideo4linux2 - Decode video streams using the hardware accelerated decoder (NVDEC); Encode RAW data in I420 format to H264 or H265 output video stream using hardware accelerated encoder (NVENC).
  - Gst-nvvideoconvert - Perform video color format conversion. The first Gst-nvvideoconvert plugin before Gst-nvdsosd plugin converts stream data from I420 to RGBA and the second Gst-nvvideoconvert plugin after Gst-nvdsosd plugin converts data from RGBA to I420.
  - Gst-nvdsosd - Draw bounding boxes, text, and region of interest (ROI) polygons.
  - Gst-nvtracker - Track object between frames.
  - Gst-nvmultistreamtiler - Composite a 2D tile from batched buffers.
  - Gst-nvv4l2decoder - Decode a video stream.
  - Gst-Nvv4l2h264enc - Encode a video stream.
  - Gst-NvArgusCameraSrc - Provide options to control ISP properties using the Argus API.
**TensorRT** 
- is a high performance neural network inference optimizer and runtime engine. 
- It focuses specifically on running a pre-trained network quickly and efficiently, using a GPU for the purpose of generating a result (i.e., scoring, detecting, regression, or inference).
- Deep learning models needs to be compliant with TensorRT in order to work with DeepStream

**Video Analytics Pipeline**
- DeepStream uses an extensible standard structure for metadata.
- The basic metadata structure, NvDsBatchMeta, starts with batch level metadata, created inside the required Gst-nvstreammux plugin. 
- Subsidiary metadata structures hold frame, object, classifier, and label data. 
- DeepStream also provides a mechanism for adding user-specific metadata at the batch, frame, or object level.
![Deepstream-pipeline](https://user-images.githubusercontent.com/73182196/134936197-abafd27d-4191-44f4-91d4-049508230d52.PNG)

Need check out nmap command
Need check out mamba, conda, pip, pipenv  command

**Install Deepstream 5.1**
```
$ sudo vi /etc/apt/sources.list.d/nvidia-l4t-apt-source.list
Update: deb https://repo.download.nvidia.com/jetson/common r32.5 main
$ sudo apt install \
libssl1.0.0 \
libgstreamer1.0-0 \
gstreamer1.0-tools \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly \
gstreamer1.0-libav \
libgstrtspserver-1.0-0 \
libjansson4=2.11-1

$ sudo apt update
$ sudo apt install deepstream-5.1

#Change the terminal color:
$ Echo ‘export TERM=xterm-mono’ >> ~/.bashrc

#Turn off Linux terminal for ls/grep color
#check ls and grep command
$ type -a ls
$ type -a grep

#Comment out “alias ls=’ls --color=auto’ in ~/.bashrc
$ nano ~/.bashrc
$ source ~/.bashrc

#Check CUDA version:
$ nvcc --version
$ /usr/local/cuda/bin/nvcc --version

#Boosting Jetson clockspeed:
$ sudo nvpmodel -m 0
$ sudo jetson_clocks
$ sudo -s cat /sys/kernel/debug/bpmp/debug/clk/clk_tree
$ sudo -s cat /sys/kernel/debug/clk/clk_summary

```

**Different Networks**
- YOLO - You Only Look Once
- Faster-RCNN in TensorRT
- SSD - Single Shot Detector 


**Build a basic video process pipeline with single neural network**
- Pipeline architecture
  - link to the image
- pipeline overview
  - Read the video data - GstFileSrc (in this example for reading the video file)
  - Parse the incoming H264 stream - GstH264Parse
  - Hardware accelerated decoder: NVDEC - Gst-nvv412decoder
  - Batch streams before sending for AI inference - Gst-nvstreammux
  - TensorRT inference - Gst-nvinfer
  - Video color format conversion (I420 to RGBA) - Gst-nvvideoconvert
  - Draw bounding boxes, text and ROI polygons - Gst-nvdsosd
  - Video clolor format conversion (RGBA to I420) - Gst-nvvideoconvert
  - Enforces limitations on data (?) - GstCapsFilter
  - Encodes RAW data in I420 format to H264 - Gst-nvv412h264enc
  - Converts H264 to RTP packets (RFC 3984) - GstRtpH264Pay
  - Sends UDP packets to the network for streaming - GstUDPSink
**Build a pipeline with multiple networks in series**
- Primary GPU Inference Engine (PGIE)
- Secondary GPU Inference Engine (SGIE)
- Process
  1. During the first inference - object detection inference (Gst-nvinfer), a region of interest (ROI) is determined and vertices provided within the metadata.
  2. When the object ROI is detected by the PGIE, a link is generated by Gst-nvtracker to tak the object between frames
  3. The SGIE takes the ROI (cropped image) as iput and provides an output with secondary identifying information (facial recognition)
  4. Deepstream_test2: one primary detector and 3 secondary detectors
  5. Pipeline architecture:
    a. link to the image
    b. pipeline overview
      * GstFileSrc - reads the video data from file
      * GstH264Parse - parses the incoming H264 stream
      * Gst-nvv4l2decoder - hardware accelerated decoder; decodes video streams using NVDEC
      * Gst-nvstreammux - batch video streams before sending for AI inference
      * Gst-nvinfer - (PGIE) runs inference using TensorRT
      * Gst-nvtracker - tracks object between frames
      * Gst-nvinfer (SGIE1) - runs inference using TensorRT
      * Gst-nvvideoconvert - performs video color format conversion (I420 to RGBA)
      * Gst-nvdsosd - draw bounding boxes, text and region of interest (ROI) polygons
      * Gst-nvvideoconvert - performs video color format conversion (RGBA to I420)
      * GstCapsFilter - enforces limitations on data (no data modification)
      * Gst-nvv4l2h264enc - encodes RAW data in I420 format to H264
      * GstRtpH264Pay - converts H264 encoded Payload to RTP packets (RFC 3984)
      * GstUDPSink - sends UDP packets to the network. When paired with RTP payloader (Gst-rtph264pay) it can implement RTP streaming
  6. Steps to Configure
    a. PGIE: dstest2_pgie_config.txt
    b. Object tracker: dstest2_tracker_config.txt
       * Tracker_config.yml is to define the low-level library used
    c. SGIE: dstest2_sgie1_config.txt
  7. add the 2nd SGIE network
    a. New segi_config.txt
      * 6 model files
      * Gie-unique-id
    b. Deepstream_test2_app.c
      * #define SGIE2_CONFIG_FILE "dstest2_sgie2_config.txt"
      * //car maker
        * guint sgie2_unique_id = 3;
      * Add some log information to show the color and maker of each car
**Build the multiple Stream Input**
### Boosting Jetson clockspeed
```
$ sudo nvpmodel -m 0
$ sudo jetson_clocks
#check CUDA version
$ nvcc --version
```
### Design
![Solution-Architecture](https://user-images.githubusercontent.com/73182196/134938593-bc760c05-ce7f-43c1-bc53-8187006a4b78.PNG)




