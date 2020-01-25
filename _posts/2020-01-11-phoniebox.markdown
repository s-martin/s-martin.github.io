---
layout: post
title:  "My Phoniebox installation"
date:   2020-01-11 21:59:06 +0100
categories: phoniebox raspberrypi
---

**WORK IN PROGRESS**

For Christmas 2019 I built my kids a [Phoniebox][phoniebox] after a friend told me how much fun he had.

These are some notes for me to remember how I built it.

# What I wanted #

For my kids I wanted to use RFID cards. In addition the box should have buttons for `Play/Pause`, `FF`, `Rwd`, `Volume up` and `Volume down`.

I wanted the box portable, so I needed a power bank.

My friend has built a excellent custom-made case which fulfills my needs.

![case](/assets/images/IMG_20191223_232037747.jpg)

That [blog post][blog-instructions] (in German) provided lots of information and I used especially the On/Off description. Thanks for providing that info!

# Hardware #

I used the following hardware:
* Raspberry Pi 3 (doesn't need an active cooling)
* Ravpower ??? Powerbank
* Neuftech RFID reader
* USB sound card
* 5W Amplifier
* OnOffShim 
* Arcade buttons
* Power button
* ...

# Building #

I connected and soldered everything first, before I assembled the complete box to make sure everything works.

Wiring Sketch:
![sketch](/assets/images/IMG_20200111_212542257.jpg)

## Software installation ##

Software installation first, so everything can be tested.

1. Download latest [Raspian Lite image][raspian-image]
2. [Install][install-rpi] Raspberry Pi with [BalenaEtcher][balenaetcher]
3. Preconfigure WiFI in image, see [here][preconfigure-wifi]
4. Rename to *phoniebox* with `sudo nano /etc/hostname`
5. Login with ssh (default user name: `pi`, default password: `raspberry`)
6. Install Phoniebox software (for Raspian Buster)

    1. Use this one line command: `cd; rm buster-install-*; wget https://raw.githubusercontent.com/MiczFlor/RPi-Jukebox-RFID/master/scripts/installscripts/buster-install-default.sh; chmod +x buster-install-default.sh; ./buster-install-default.sh`

    2. See also [here for details][install-phoniebox].

    3. I use the *Classic* version as I don't use Spotify and it's supposed to be faster.

7. I configured the sound according to this [description][fix-sound].
8. To use the buttons I configured the GPIO settings according to [this manual][gpio-config], but without the shutdown, because I use the OnOffShim (see later steps). In the file `scripts/gpio-buttons.py` the pins can be configured and (de)activated. I don‘t use recording although the case and the hardware supports it.
9. Install [OnOffShim][onoffshim] software

    1. Use this one line command: `curl https://get.pimoroni.com/onoffshim | bash`

    2. Set `daemon_active=1`, `led_pin=25` and `hold_time=1` in file `/etc/cleanshutd.conf`

    3. Make sure the original Phoniebox shutdown script is used: Open `sudo nano /usr/bin/cleanshutd` and replace `shutdown -h +$shutdown_delay` with `/home/pi/RPi-Jukebox-RFID/scripts/playout_controls.sh -c=shutdown`. Be aware that `shutdown_delay` has no effect anymore.

10. Reboot with `sudo reboot`.

# Gotchas, lessons learned, etc. #

* It’s really important to solder as good as possible (I’m not very good though). If you have cold solder joints it may or may not work, which can be really annoying.
* Connecting everything together first to test it is really helpful, because you don’t want to assemble everything together in a (tiny) case and then something doesn’t work. 
* It really helps to sketch the wiring on a piece of paper, so you can fix possible issues or misconnections much faster.

**to be continued**

[blog-instructions]: http://splittscheid.de/selfmade-phoniebox/
[phoniebox]: https://github.com/MiczFlor/RPi-Jukebox-RFID
[raspian-image]: https://downloads.raspberrypi.org/raspbian_lite_latest
[preconfigure-wifi]: https://raspberrypi.stackexchange.com/questions/10251/prepare-sd-card-for-wifi-on-headless-pi
[install-rpi]: https://www.raspberrypi.org/documentation/installation/installing-images/
[balenaetcher]: https://www.balena.io/etcher/
[install-phoniebox]: https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/INSTALL-stretch#one-line-install-command
[fix-sound]: https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/Troubleshooting-FAQ#audio-is-not-working
[gpio-config]: https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/Using-GPIO-hardware-buttons#how-to-connect-the-buttons
[onoffshim]: https://shop.pimoroni.com/products/onoff-shim
