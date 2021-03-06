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

Search for SteamVR in the steam application, you should see the following image:

![SteamVR search](/images/steam_vr_search.png) 

Click on Launch to install it. Make sure you have SteamVR beta (and not linux_temp) version installed otherwise the SteamVR apps and OpenVR will not work. Now, to ensure that you install SteamVR beta, right click on the SteamVR app and click on properties. 

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

### Running HelloVR example with OpenVR 

Now run the Steam server via 
```
$steamvr/bin/linux64/vrserver --keepalive
```
In another window, run this `hellovr_opengl` example 

```
~/.steam/steam/ubuntu12_32/steam-runtime/run.sh ~/workspace/code/openvr/samples/bin/linux64/hellovr_opengl
```
You should see the following images streamed in your Vive when you wear it 

![HelloVR view1](/images/hellovr_1.png) 

![HelloVR view2](/images/hellovr_2.png) 

### Running TrackingCamera example with OpenVR 

To run this example, you have to enable the camera via `SteamVR` as shown in the gif below: 

![Enable Camera](/images/enable_camera.gif) 

Once the camera is enabled, you should be able to play the tracking camera example via 

```
~/.steam/steam/ubuntu12_32/steam-runtime/run.sh ~/workspace/code/openvr/samples/bin/linux64/tracked_camera_openvr_sample
```

and see the following images 

![Tracking Camera1](/images/tracking_example_1.png) 

![Tracking Camera2](/images/tracking_example_2.png) 


## Installing pyopenvr 

If you'd like to play these examples in python then we recommend to install this (unofficial) version of [pyopenvr](https://github.com/cmbruns/pyopenvr).

This requires Python 3.5+ and many other dependencies including `pillow, pyopengl, glfw` etc. 

In addition, it is important to change `c_void_p` in this [line](https://github.com/cmbruns/pyopenvr/blob/master/src/openvr/__init__.py#L1728-L1733) in the pyopenvr code to `c_uint32` otherwise any python example will crash with a message that might look like this:

```
  self.texture.handle = self.resolve_texture_id
TypeError: cannot be converted to pointer
```

This is also mentioned in this particular [issue](https://github.com/cmbruns/pyopenvr/issues/20) in the pyopenvr repository. 

Install `pyopenvr` via 
```
python setup.py install 
```
Make sure that the `PYTHONPATH` variable is set up correctly:

```
export PYTHONPATH=~/workspace/code/pyopenvr/src:$PYTHONPATH
```
Now you should be able run the samples in python via 

```
~/.steam/steam/ubuntu12_32/steam-runtime/run.sh python src/samples/glfw/hellovr_glfw.py
```
and see the following image 

![HelloVR PyOpenVR](/images/hello_vr_pyopenvr.png)
