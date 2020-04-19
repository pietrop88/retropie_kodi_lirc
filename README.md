# Retropie + Kodi + Apple Remote

This repo contains my setup for Retropie, Kodi and Apple Remote (model A11565) using a Raspberry Pi 3.

## Retropie
The Retropie image for Raspberry 3 can be obtained [here](https://retropie.org.uk/download/). Download it and flash it on a sd card.
At the first boot configure an input controller to perform all the following steps (I used a keyboard).

## Kodi inside Retropie
All the steps to install Kodi inside Retropie are described [here](https://github.com/RetroPie/RetroPie-Setup/wiki/KODI).

I also configured the Yatse App to control Kodi as described [here](https://kodi.wiki/view/Webserver).

I also set a specific Kodi entry in the Retropie menu without moving to *Ports* section as described [here](https://github.com/RetroPie/RetroPie-Setup/wiki/KODI).

### YouTube AddOn inside Kodi
Install the Kodi YouTube AddOn and follow [these steps](https://github.com/jdf76/plugin.video.youtube/wiki/Personal-API-Keys) to enable the Google YouTube API to use with the AddOn.

## Input controller
Nevertheless Apple Remote works like a charm in Kodi, I did not find a way to use the Apple Remote as an input devices in Retropie. 

The system boots in Reptropie and with a simple menu selection the user can switch to Kodi. This menu selection cannot be performed with the Apple Remote and needs a Retropie configured controller. When I want to move my setup to use only Kodi, I do not want to bring also the Retropie controller so I configured a keyboard as a Retropie controller and then I use the [Universal Controller App](https://github.com/threebrooks/UniversalController) on my Android phone.
Please note that this app is not intended to replace a Retropie controller but it is very useful to navigate in the Retropie menu and select Kodi.

These are the steps when I move my setup to use Kodi only: 

* bring only the Raspberry Pi, power cable, hdmi cable and Apple Remote (optional, you can use Yatse App if you have configured it)
* Connect the Raspberry Pi to the monitor and start it
* Use the Universal Controller App to navigate the Retropie menu and select Kodi
* When you are in Kodi, you can use the Apple Remote or the Yatse App to control it

Please note that, before using them, both the Universal Controller App and the Yatse app must be configured as described [here](https://github.com/threebrooks/UniversalController) and [here](https://kodi.wiki/view/Webserver) respectively.


## Lirc and Apple Remote (A1156)
NOTE: all the following steps require the use of the shell. You can press *F4* to move from Retropie GUI to the shell or you can enable the Retropie SSH server ([guide](https://github.com/RetroPie/RetroPie-Setup/wiki/SSH)) to connect to it with another PC.

The **TSOP4838** sensor is employed as IR receiver and I connected it to the Raspberry Pi GPIO pins as illustrated in [this image](https://kodi.wiki/view/File:Gpioir.png).

### Retropie configuration
1. activate the lirc kernel module uncommenting the following line in `/boot/config.txt`:
   ```
   dtoverlay=dtoverlay=lirc-rpi
   ```
2. install `lirc` package:
   ```
   sudo apt update
   sudo apt install lirc
   ```
3. Change these two lines in `/etc/lirc/lirc_options.conf` as follows:
   ```
   driver = default
   device = /dev/lirc0
   ```
4. Enable systemd lirc service:
   ```
   sudo systemctl enable lircd.service
   ```
5. Reboot the system:
   ```
   sudo reboot 
   ```
6. Test pulse inputs from the IR driver
   ```
   sudo systemctl stop lircd.service
   sudo mode2 -d /dev/lirc0
   ```
   Clicking a button on the Apple Remote you will see some sequences of *pulse/space* strings. 
   Use `Ctrl+C` to exit and restart lircd systemd service
   ```
   sudo systemctl start lircd.service
   ```
7. Copy `apple.lirc.d` file in this repo to `/etc/lirc/lircd.conf.d/`. This file maps the button press signals to key symbols. You can test the key symbols after restarting the lircd service
   ```
   sudo systemctl restart lircd.service
   ```
   and using the command
   ```
   irw
   ```
   and pressing any Apple Remote buttons. `Ctrl+C` to exit.

8. Copy `Lircmap.xml` file in this repo to `~/.kodi/userdata/`. This file maps IR key symbols to Kodi commands.

9. Copy `appleremote.xml` file in this repo to `~/.kodi/userdata/keymaps/`. This file maps Kodi commands to actions in the different Kodi windows.

## 3D printed case
[Here](https://www.thingiverse.com/make:789112) you can find all the pictures and the info on the case that I printed. [Here](https://www.thingiverse.com/thing:971778) you will find the original file. I only made a small hole on the front face to host the IR receiver sensor. 

## External links

[https://kodi.wiki/view/LIRC](https://kodi.wiki/view/LIRC)

[https://kodi.wiki/view/Raspberry_Pi](https://kodi.wiki/view/Raspberry_Pi)

[https://www.instructables.com/id/Easy-Setup-IR-Remote-Control-Using-LIRC-for-the-Ra/](https://www.instructables.com/id/Easy-Setup-IR-Remote-Control-Using-LIRC-for-the-Ra/)