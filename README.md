#pizero-webcam-tutorial
A step-by-step tutorial for setting up a Raspberry Pi Zero W computer as a webcam

> **OctoPrint** is the leading web software for controlling 3D printers, created/maintained by Gina Häußge
>
> **OctoPi** is a Raspberry-specific distro of OctoPrint, maintained by Guy Sheffer
>
> **mjpg-streamer** is a fork of a popular streaming service as maintained by Jackson Liam

## Overview
Although a standard OctoPi installation seems to work fine with a single camera (Raspberry Pi 3), adding a second webcam can sometimes add to the complexity. Adding a webcam to an OctoPi install on a Raspberry Pi Zero or Zero W is asking too much of the single core processor; it's best to offload this work if possible.

Using the approach here, it should be possible to build a dedicated Raspberry Pi Zero W—based webcam. In theory, you could have several views of your 3D printer and include them within the OctoPrint web interface to offer different viewpoints of the work being accomplished.

## Installation
1. Visit the [downloads page](https://www.raspberrypi.org/downloads/raspbian/) to get the latest **Raspbian Stretch Lite** image
2. Use [Etcher](https://etcher.io) to burn this image to a 4GB microSD card that you've installed in your workstation, noting that it should automatically eject the card when finished
3. Remove and re-insert the microSD card so that it's mounted again
4. Using `Finder` or the `Terminal` program, create a new `/Volumes/boot/wpa_supplicant.conf` file. Add a line with `country=US` so that it indicates your own country code then add the following at the end of the file (editing with your own zone/password information but keeping the quotation marks).

```
country=US

network={
    ssid="your_SSID"
    psk="your_PSK"
    key_mgmt=WPA-PSK
}

ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
```

Additionally, create an empty file in `/Volumes/boot` called `ssh`.

&nbsp;5. Use Finder's eject feature to safely eject the microSD card, removing it and putting it into the Raspberry Pi Zero W computer

&nbsp;6. Install the Raspberry Pi—based webcam with the micro ribbon cable

&nbsp;7. Power the Raspberry Pi Zero W with a 5V micro USB adapter or USB cable, noting the activity of the onboard LEDs

&nbsp;8. Remote into the computer by running `ssh pi@raspberrypi.local` using `raspberry` as the default password

&nbsp;9. Remotely on the Raspberry, run `sudo raspi-config` to:

* change the hostname to something like `picam1` under the `2 Network Options` -> `N1 Hostname` section
* change the default password to something you can remember under the `1 Change User Password` section
* change the `localisation` settings to minimally update the **wi-fi country code** and **timezone** but also the **locale** information and the **keyboard layout**
* change the **bootup** options to log in automatically to the console under the `3 Boot Options` -> `B1 Desktop / CLI` -> `B2 Console Autologin` option
* change the `Interfacing Options` -> `P1 Camera` to enable it
* select `Advanced Options` -> `A1 Expand Filesystem`
* choose `Finish`

&nbsp;10. Having rebooted after that session, note that the hostname and (optionally) the password would have changed as you remote back into the Raspberry with `ssh pi@picam1.local`, for example

&nbsp;11. Remotely, run `sudo apt-get update` as well as the following command

&nbsp;12. `sudo apt-get upgrade -y`

&nbsp;13. `sudo apt-get install git -y`

&nbsp;14. `sudo apt-get install cmake libjpeg8-dev -y`

&nbsp;15. `cd ~`

&nbsp;16. `git clone https://github.com/jacksonliam/mjpg-streamer.git`

&nbsp;17. `cd mj*/mj*`

&nbsp;18. `make`

&nbsp;19. `sudo make install`

&nbsp;20. Run `sudo nano /etc/rc.local` and add this before the exit command:

```
cd /home/pi/mjpg-streamer/mjpg-streamer-experimental
export LD_LIBRARY_PATH="$(pwd)"
./mjpg_streamer -i "input_raspicam.so" -o "output_http.so -w ./www"
```

&nbsp;21. Run `sudo reboot` and wait two minutes

&nbsp;22. Try [http://picam1.local:8080](http://picam1.local:8080) and [http://picam1.local:8080/?action=stream](http://picam1.local:8080/?action=stream)

![Main page screenshot](https://user-images.githubusercontent.com/15971213/41619412-03dfb6c0-73bb-11e8-948d-6bd9de4ff219.png)![Stream screenshot](https://user-images.githubusercontent.com/15971213/41619461-349ba80a-73bb-11e8-86c6-de0d446dc16d.png)

## Follow-up
From here, you may want to install the [MultiCam plugin](https://plugins.octoprint.org/plugins/multicam/) to take advantage of your external webcam, as hosted by a Raspberry Pi Zero W computer.

--


|Description|Version|Author|Last Update|
|:---|:---|:---|:---|
|pizero-webcam-tutorial|v1.0.1|OutsourcedGuru|June 18, 2018|

|Donate||Cryptocurrency|
|:-----:|---|:--------:|
| ![eth-receive](https://user-images.githubusercontent.com/15971213/40564950-932d4d10-601f-11e8-90f0-459f8b32f01c.png) || ![btc-receive](https://user-images.githubusercontent.com/15971213/40564971-a2826002-601f-11e8-8d5e-eeb35ab53300.png) |
|Ethereum||Bitcoin|