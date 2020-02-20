# HTC-Vive-Setup-Ubuntu
Step by step guide to setting up HTC Vive on Ubuntu

## Installing Steam

Get the latest versin of Steam from [Steam Store](https://www.steamvr.com/en/)

## Installing SteamVR 

Once you have installed Steam, the next step is to install [SteamVR](https://www.steamvr.com/en/)


Make sure you have an up-to-date NVIDIA driver (on 20th Feb 2020, we had NVIDIA driver version 430.64 installed on our Ubuntu 16.04). We installed the driver via xx.run file we obtained from the NVIDIA website. You will need to make sure it also installs 32-bit GL libraries, so click on Yes when it prompts during the installation process.

For further requirements please check [https://github.com/ValveSoftware/SteamVR-for-Linux](https://github.com/ValveSoftware/SteamVR-for-Linux)

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
