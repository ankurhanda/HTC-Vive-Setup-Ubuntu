# HTC-Vive-Setup-Ubuntu
Step by step guide to setting up HTC Vive on Ubuntu

Ankur Handa and Clemens Eppner 

## Installing Steam

Get the latest versin of Steam from [Steam Store](https://www.steamvr.com/en/)

## Installing SteamVR 

Make sure you have an up-to-date NVIDIA driver (on 20th Feb 2020, we had NVIDIA driver version 430.64 installed on our Ubuntu 16.04). We installed the driver via `NVIDIA-Linux-x86_64-430.64.run` file we obtained from the NVIDIA website. You will need to make sure it also installs 32-bit GL libraries, so click on Yes when it prompts during the installation process.

### Troubleshooting
If you do not install 32-bit GL libraries, you may get the following error 

```
You are missing the following 32-bit libraries, and Steam may not run:libGL.so.1
```


For further requirements please check [https://github.com/ValveSoftware/SteamVR-for-Linux](https://github.com/ValveSoftware/SteamVR-for-Linux)

Make sure you have SteamVR beta (and not linux_temp) version installed otherwise the SteamVR apps and OpenVR will not work. 

Search for SteamVR in the steam application, you should see the following image:

![SteamVR search](/images/steam_vr_search.png) 

Click on Launch to install it. Now, to ensure that you install SteamVR beta, right click on the SteamVR app and click on properties. 

![SteamVR properties](/images/steam_vr_properties.png) 

You will see another window pop up and now click on **BETAS** and you can select beta from the options presented in the menu 

![SteamVR Beta](/images/steam_vr_beta.png) 


## Installing OpenVR 

Most of the process mentioned below is from [this blog](https://celynwalters.github.io/Robotic-Telepresence/2016/10/19/Attempting-VR-in-Ubuntu/)

```
$ git clone https://github.com/ChristophHaag/openvr.git
$ cd openvr
$ mkdir build
$ cd !$
$ cmake -DCMAKE_BUILD_TYPE=Release ../
$ make

```

Now plug in the vive and set up the permissions as below: 

```
$ sudo chmod a+rw /dev/hidraw*
```

Create a shell file to set up the variables necessary for running openvr via steamvr. On our computer the file, called `run_openvr.sh` looks like this:

```
export openvr=~/workspace/codeopenvr
export steam=~/.steam
export steamvr=$steam/steam/steamapps/common/SteamVR

export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:\
/usr/lib/:\
/usr/lib32/:\
$openvr/lib/linux32/:\
$openvr/lib/linux64/:\
$steam/ubuntu12_32/steam-runtime/i386/lib/i386-linux-gnu/:\
$steam/ubuntu12_32/steam-runtime/amd64/lib/x86_64-linux-gnu/:\
$steamvr/bin/linux32/:\
$steamvr/bin/linux64/:\
$steamvr/drivers/lighthouse/bin/linux32/:\
$steamvr/drivers/lighthouse/bin/linux64/
```

We also need to set up the following permissions 

```
$ sudo vim /etc/udev/rules.d/83-hmd.rules
$ echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", MODE="0666", GROUP="plugdev"' >> test.txt 
$ echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="28de", MODE="0666", GROUP="plugdev"' >> test.txt
$ sudo cp test.txt /etc/udev/rules.d/83-hmd.rules
$ sudo udevadm control --reload-rules
```
The file `/etc/udev/rules.d/83-hmd.rules` may not exist, so it needs to be created first. 

source the `run_openvr.sh` file 

```
source run_openvr.sh
```

Now run the Steam server via 
```
$steamvr/bin/linux64/vrserver --keepalive
```
In another window, run this example 

```
~/.steam/steam/ubuntu12_32/steam-runtime/run.sh ~/workspace/code/openvr/samples/bin/linux64/hellovr_opengl
```
You should see the following image streamed in your Vive when you wear it. 
