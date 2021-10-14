# Azure IoT Hub
```
$ az iot hub create --name $iotHubName -g iot-poc --sku F1 --partition-count 2
$ az iot hub device-identity create --hub-name $iotHubName --device-id TruckDevice
$ az iot hub device-identity create --hub-name $iotHubName --device-id AirplaneDevice
> az iot hub device-identity create --hub-name $iotHubName --device-id ContainerDevice

$ az iot hub device-identity connection-string show --hub-name $iotHubName --device-id TruckDevice --output tsv
HostName=IoT-01.azure-devices.net;DeviceId=TruckDevice;SharedAccessKey=9A9lKeymAkuuEXcVhT8xIKgMhP/neOCMAp15CujIPaU=
$ az iot hub device-identity connection-string show --hub-name $iotHubName --device-id AirplaneDevice --output tsv
HostName=IoT-01.azure-devices.net;DeviceId=AirplaneDevice;SharedAccessKey=tdxA+Tbu8czqgK4k9KFQmXNHvw0ojjEhWXrgGNiYH8w=
$ az iot hub device-identity connection-string show --hub-name $iotHubName --device-id ContainerDevice --output tsv
HostName=IoT-01.azure-devices.net;DeviceId=ContainerDevice;SharedAccessKey=SrhmOV2JHneja+jokxqW74CcKoyPiHn34h1zwUMP6T0=

$ mkdir simulator
$ cd simulator

$ dotnet new console
$ dotnet restore

$ dotnet add package Microsoft.Azure.Devices.Client
$ dotnet add package Microsoft.Azure.Devices.Shared
$ dotnet add package Newtonsoft.Json
$ code Program.cs
$ dotnet run
```
# Device Provisioning Service
- A DPS service can be linked to one or  more IoT hubs
- A system for managing your certs and your enrollments
- Each device will have a cert for the validation
- Enrollments
  - Individual
    - For devices that have a unique configuration
    - Requires greater security than the connection strings
  - Group
    - One root cert
    - Numbers of leaf certs
  - Allocation policy
    - Multiple IoT hubs
    - Direct the incoming data to different hubs based on a policy
- Proof of possession
  - Verification certificate - generated from root cert. To prove that you own the root cert.
- Create DPS
  - on Azure Portal find DPS
  - Create DPS
  - Link to IoT-Hub (with owner permission)
  - Manage allocation policy
  - Copy IDScope string - 0ne0025F07F
  - Creating x.509 cert
    - Go to cloud shell with bash
```
# create certificates directory
$ mkdir certificates
# navigate to certificates directory
$ cd certificates
# download helper script files
$ curl https://raw.githubusercontent.com/Azure/azure-iot-sdk-c/master/tools/CACertificates/certGen.sh --output certGen.sh
$ curl https://raw.githubusercontent.com/Azure/azure-iot-sdk-c/master/tools/CACertificates/openssl_device_intermediate_ca.cnf --output openssl_device_intermediate_ca.cnf
$ curl https://raw.githubusercontent.com/Azure/azure-iot-sdk-c/master/tools/CACertificates/openssl_root_ca.cnf --output openssl_root_ca.cnf
# update script permissions so user can read, write, and execute it
$ chmod 700 certGen.sh
$ ./certGen.sh create_root_and_intermediate
```
 - Download ./certs/CW3-IoT.root.ca.cert.pem
 - Configure DPS to trust the root cert
   - Add the previous step pem file for the cert.
 - Proof of Possession
   - Generate the verification code
```
$ ./certGen.sh create_verification_certificate <verification-code>
```
   - Download ./certs/verification-code.cert.pem
   - Verify the cert with verification pem in the detail pane
 - Create a group enrollment
   - Setting -> manage enrollment
   - Choose default values 
   - Use the root cert and leave 2nd cert blank
  - Save

# Create sensor devices
```
$ mkdir client
$ cd client
$ dotnet new console
$ dotnet restore
$ dotnet add package Microsoft.Azure.Devices.Client
$ dotnet add package Microsoft.Azure.Devices.Provisioning.Client
$ dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Amqp
$ Dotnet run
```
# Create Azure Container Registry
```
$ az acr create -g iot-poc -n IotAcr01 --sku Basic
$ az acr update -n IotAcr01 --admin-enabled true
$ az acr credential show -n IotAcr01
```
# Create edge device
```
$ az iot hub device-identity create --device-id iot-poc-01 --hub-name ioT-01 --edge-enabled
$ az iot hub device-identity show-connection-string --device-id iot-poc-01 --hub-name IoT-01
```
# Some useful commands for docker, iot edge and etc.
```
$ docker ps
$ docker stop <containerid>
$ docker rm <containerid>
$ docker rmi <imageid>
$ stop iotedge
$ sudo systemctl stop iotedge
#Check the iot edge system running
$ sudo systemctl status iotedge
$ sudo systemctl poweroff
#Service logs
$ sudo journalctl -u iotedge
#Configuration and connection
$ sudo iotedge check
$ sudo iotedge logs nameOfAgent
$ sudo docker image ls
$ sudo docker container ls
# Install curl
$ sudo apt install curl
```

