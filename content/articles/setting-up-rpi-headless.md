---
title: "Setting up a Headless, WiFi'd Raspberry Pi"
date: 2018-01-13T13:20:37Z
draft: false
tags: ["raspberry", "pi", "linux"]
categories: ["linux"]
---


Sometimes you just get stuck in situations where you don't have access to an HDMI port or mouse/keyboard when setting
up a raspberry pi. Luckily, there are a good number of solutions at your disposal to install the OS and connect to
wifi without any human intervention. This works for any wifi-enabled pi, including ones with wifi over usb.

## Obtaining an Image

The first step is to obtain an operating system image for your new raspberry pi. For that you can head over to the 
[downloads page](https://www.raspberrypi.org/downloads/) and pick up a fresh copy of raspbian. I decided on Stretch Lite,
and downloaded the torrent.

## Flashing the Image

The next step is to flash the image onto your SD card of choice. To do this, you can refer to the pi documentation: 
[installing operating system images](https://www.raspberrypi.org/documentation/installation/installing-images/). I would
recommend looking to the advanced section and finding the command line instructions. They're fairly straight forward 
and have the added bonus of avoiding having to install any additional software. On macOS, the command line instructions
are the following:

Start by finding the number of the disk you are flashing to (ex `disk2`), and un-mount it. Replace any capitals with
your corresponding values.

    diskutil list
    diskutil unmountDisk /dev/disk<SDCARD_NUMBER>

Then, flash the image

    sudo dd bs=1m if=<IMAGE_LOCATION> of=/dev/rdisk<SDCARD_NUMBER> conv=sync

You can press CTRL-T to check the progress. It should copy in a matter of minutes.

{{< asciinema gVwyGkgKxHPuOCn4YOPcR7Evn >}}

## Final Configuration

The last thing(s) you need to do is to enable ssh by default. The raspberry pi disables it on new builds by default,
but it is essential for us to enable it to be able to log in. To allow ssh, simply navigate to the sd card and touch a
new file called `ssh`.

    cd /Volumes/boot/
    touch ssh

Now, we need to do a similar thing for the WiFi settings. Once again, navigate to the sd card and create a new file.

    cd /Volumes/boot/
    touch wpa_supplicant.conf
    
Add the following details into that file, replacing the SSID and PASSWORD as needed.

    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    network={
        ssid="SSID"
        psk="PASSWORD"
        key_mgmt=WPA-PSK
    }
    
Now that your config is set up, you can plug your sd back into the pi along with a power source and watch your router, 
waiting for a new device to pop up. Once it pops up on the network, simply ssh into the device: `ssh pi@192.168.1.150` 
with password `raspberry`.

## Some Next Steps

- change the default password using the `passwd` command
- install [alexa](https://github.com/alexa/alexa-avs-sample-app) and get your AI on
- improve security by [disabling passwords and the root account](/articles/rpi-security/#password)
- mitigate brute force attacks with [fail2ban](/articles/rpi-security/#fail2ban)
- block malicious connections with [ufw](/articles/rpi-security/#ufw)