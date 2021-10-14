# Update object detection mode
```
#!/bin/bash

MY_MODEL_URL=$1
#Delete the contents of the CUSTOM_VISION_AI directory to remove existing model and TensorRT engine
rm -rf /data/misc/storage/Intelligent-Video-Analytics-with-NVIDIA-Jetson-and-Microsoft-Azure/services/CUSTOM_VISION_AI/*

#Navigate to CUSTOM_VISION_AI directory
cd /data/misc/storage/Intelligent-Video-Analytics-with-NVIDIA-Jetson-and-Microsoft-Azure/services/CUSTOM_VISION_AI

#Download the exported model from CustomVision.AI
#Note: It is important that the link to your model is quoted when running this command!
sudo wget -O model.zip $MY_MODEL_URL

#"https://irisscuprodstore.blob.core.windows.net/m-ad5281beaf20440a8f3f046e0e7741af/e3ebcf6e22934c7e89ae39ffe2049a46.ONNX.zip?sv=2017-04-17&sr=b&sig=A%2F9raRar12TSTCvH7D72OxD6mBqvRY5doovtwV4Bjt0%3D&se=2020-04-15T20%3A43%3A26Z&sp=r"

#Unzip the model.zip that we just downloaded
unzip model.zip

#Install dos2unix
sudo apt install -y dos2unix

#Convert labels.txt to Unix format (otherwise DeepStream will append '/r' to object value)
dos2unix labels.txt

#Remove the old container
docker rm -f NVIDIADeepStreamSDK

```
After loading the new model, check the Deepstream log
```
docker logs -f NVIDIADeepStreamSDK
```

# Streaming Object Detection
```
sudo cp /opt/nvidia/deepstream/deepstream-5.1/samples/configs/deepstream-app/source8_1080p_dec_infer-resnet_tracker_tiled_display_fp16_nano.txt /opt/nvidia/deepstream/deepstream-5.1/samples/configs/deepstream-app/source8_1080p_dec_infer-resnet_tracker_tiled_display_fp16_nano_rtsp_streaming.txt
```
update the config file
```
tiled-display]
enable=1
rows=1
columns=1
width=1280
height=720
gpu-id=0
#(0): nvbuf-mem-default - Default memory allocated, specific to particular platform
#(1): nvbuf-mem-cuda-pinned - Allocate Pinned/Host cuda memory, applicable for Tesla
#(2): nvbuf-mem-cuda-device - Allocate Device cuda memory, applicable for Tesla
#(3): nvbuf-mem-cuda-unified - Allocate Unified cuda memory, applicable for Tesla
#(4): nvbuf-mem-surface-array - Allocate Surface Array memory, applicable for Jetson
nvbuf-memory-type=0

[source0]
enable=1
#Type - 1=CameraV4L2 2=URI 3=MultiURI 4=RTSP
type=4
uri=rstp://127.0.0.1:8554/test
num-sources=1
#drop-frame-interval=2
gpu-id=0
# (0): memtype_device   - Memory type Device
# (1): memtype_pinned   - Memory type Host Pinned
# (2): memtype_unified  - Memory type Unified
cudadec-memtype=0
```
Launch RSTP server
```
$ ./test-launch "nvarguscamerasrc ! nvvidconv ! nvv4l2h264enc ! h264parse ! rtph264pay name=pay0 pt=96"
```
Run test program
```
$ ./deepstream-test5-app -c configs/deepstream-app/source8_1080p_dec_infer-resnet_tracker_tiled_display_fp16_nano_rtsp_streaming.txt
```
Some useful command to check the Nvidia system status
```
$ sudo tegrastats --interval <ms> --verbose
```
