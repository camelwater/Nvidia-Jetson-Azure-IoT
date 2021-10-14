*Wifi installation*

**1st try (4/10)**
Check all devices on bus
```
$ lsusb 
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo reboot now
```
Driver Installation
```
$ git clone https://github.com/lwfinger/rtl8723bu.git
$ cd rtl8723bu
$ source dkms.conf
$ sudo mkdir /usr/src/$PACKAGE_NAME-$PACKAGE_VERSION
$ sudo cp -r core hal include os_dep platform dkms.conf Makefile rtl8723b_fw.bin /usr/src/$PACKAGE_NAME-$PACKAGE_VERSION
$ sudo apt install dkms
$ sudo dkms add $PACKAGE_NAME/$PACKAGE_VERSION
$ sudo dkms autoinstall $PACKAGE_NAME/$PACKAGE_VERSION
$ sudo reboot now
#unplug the ethernet cable
$ ifconfig wlan0
#did not see the wlan0: driver installation did not work
$ nmcli d 
$ nmcli r wifi on
$ nmcli d wifi list
$ nmcli d wifi connect [SSID] password [PASSWORD]
```
Did not work; no wlan0

**Wifi installation 2nd try (4/10)**
```
$ lsusb 
$ git clone https://github.com/cilynx/rtl88x2bu.git
$ cd rtl88x2bu
VER=$(sed -n ‘s/\PACKAGE_VERSION=”\(.*\)”/\1/p’ dkms.conf)
$ sudo rsync -rvhP ./ /usr/src/rtl88x2bu-${VER}
```
No luck - still no wlan0

**Wifi installation 3rd try (4/11)**

The problem of the first two tries are: 
- Did not download and install the correct driver for the wifi adapter - EW-7811 UN V2
- What need be done is:
  - Find the right version of the driver with proper support of the chip
  ```
  $ lsusb show all usb ports
  #for driver messages
  $ dmesg 
  #display the status of the kernel modules
  $ lsmod 
  $ lsmod | grep 8192 
  $ dkms status check 
  $ dkms installation status
  #find the kernel version: 
  $ uname -r 
  
  ```
  My Nvidia Jetson Nano is 4.9.201-tegra

  - Install the driver
```
#The git repo - Try the following steps:
$ git clone https://github.com/lwfinger/rtl8188eu.git
#Add a dkms.conf file
#PACKAGE_NAME="rtl8188eu"
#PACKAGE_VERSION="1.0"
#BUILT_MODULE_NAME[0]="8188eu"
#DEST_MODULE_NAME[0]="rtl8188eu"
#DEST_MODULE_LOCATION[0]="/kernel/drivers/net/wireless"
#AUTOINSTALL="yes"
$ sudo cp -r ./rtl8188eu /usr/src/rtl8188eu-1.0
$ sudo dkms add rtl8188eu/1.0
$ sudo dkms build rtl8188eu/1.0
$ sudo dkms autoinstall rtl8188eu/1.0
$ systemctl reboot
$ nmcli d 
#(yay!) see the wlan0
#Now turn on the wlan0
nmcli r wifi on
nmcli d wifi list
nmcli d wifi connect [ssid] password [xx]
```
 [5:07pm] wifi works finally!

