---
layout: post
title:  "My Phoniebox installation - revised"
date:   2020-03-04 21:21:06 +0100
categories: phoniebox raspberrypi
---

I built a [Phoniebox][phoniebox] for my kids, please see my [first blog post][first-post] for details. Overall the box works very good. But sometimes the box would restart out of the blue (sometimes multiple times in a row).

I suspect the amplifier [PAM8406][amp], connected to 5V (Pin 4) at the Pi, would sometimes draw too much current, so the whole Pi restarts. I have a second port at the installed powerbank I could use, but then the amplifier would be always on and drain the powerbank even if the box is switched off.

I finally decided to give the [Hifiberry Miniamp][miniamp] a try. So the amplifier would be turned off, if the Pi is off and I could also replace the USB sound card I have been using, because the case and the board of the soundcard are already a little bit broken and I wanted to replace it anyway.

# Hardware Installation #

* Remove the amplifier PAM8406 and the USB sound card
* Disconnect the button `PLAY` from pin 40 and connect it to pin 25
* Disconnect the button `PREV` from pin 38 and connect it to pin 24
* Disconnect the button `VOL DOWN` from pin 35 and connect it to pin 33
* Disconnect the button `VOL UP` from pin 36 and connect it to pin 32
* Disconnect the button `NEXT` from pin 37 and connect it to pin 18
* Connect the Miniamp to pin 1 (3V), pin 4 (5V), pin 14 (GND), pin 12 (GPIO18), pin 35 (GPIO19), pin 36 (GPIO16), pin 37 (GPIO26), pin 38 (GPIO20) and pin 40 (GPIO21).
* Connect the speakers to the Miniamp (`L-+ R+-`), see also the [datasheet][datasheet-miniamp].

    ![Miniamp](/assets/images/miniamp.jpg)

# Software configuration #

My Phoniebox is already installed, so I describe only the necessary changes here. For installation see my [first post][first-post].

* Check especially [instructions for volume control][miniamp-details] and maybe instructions from [Hifiberry website][miniamp-config]
* Enable Miniamp in `/boot/config.txt`

    ```
    config_hdmi_boost=4
    #dtparam=audio=on
    dtoverlay=hifiberry-dac
    ``` 

scripts/gpio_buttons.py folgendes
 austauschen
 _ siehe datein


dann so wie hier http://splittscheid.de/selfmade-phoniebox/#miniampsetup




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
[first-post]: https://s-martin.github.io/phoniebox/raspberrypi/2020/01/11/phoniebox.html
[amp]: https://leeselectronic.com/en/product/1810.html
[miniamp]: https://www.hifiberry.com/shop/boards/miniamp/
[datasheet-miniamp]: https://www.hifiberry.com/docs/data-sheets/datasheet-miniamp/
[miniamp-config]: https://www.hifiberry.com/docs/software/configuring-linux-3-18-x/
[miniamp-details]: https://support.hifiberry.com/hc/en-us/articles/205377202-Adding-software-volume-control
[miniamp-gpio]: https://www.hifiberry.com/docs/hardware/gpio-usage-of-hifiberry-boards/
[raspian-image]: https://downloads.raspberrypi.org/raspbian_lite_latest
[preconfigure-wifi]: https://raspberrypi.stackexchange.com/questions/10251/prepare-sd-card-for-wifi-on-headless-pi
[install-rpi]: https://www.raspberrypi.org/documentation/installation/installing-images/
[balenaetcher]: https://www.balena.io/etcher/
[install-phoniebox]: https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/INSTALL-stretch#one-line-install-command
[fix-sound]: https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/Troubleshooting-FAQ#audio-is-not-working
[gpio-config]: https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/Using-GPIO-hardware-buttons#how-to-connect-the-buttons
[onoffshim]: https://shop.pimoroni.com/products/onoff-shim
