---
layout: article
uuid: 57547BCA-2BEB-4F71-9660-C46D4880D567
title: Getting Started with the RaspberryPi
name: getting-started-with-the-raspberrypi
created_at: 2012-07-09
updated_at: 2012-07-09
categories: linux arm rpi
---

Where to get it
===

If you have to have it ordered today, you can pay somewhere in the range of $70 to $100 and
[get it from ebay](http://www.ebay.com/sch/i.html?_nkw=raspberry+pi).

Otherwise you'll have to get on the waiting list at one of

  * [Newark element14 (US)](http://www.element14.com/community/groups/raspberry-pi?ICID=hp_raspberry)
  * [Allied Electronics (US)](http://www.alliedelec.com/RaspberryPi/)
  * [Farnell element14 (UK)](http://uk.farnell.com/raspberry-pi)
  * [RS DesignSpark (UK)](http://uk.rs-online.com/web/generalDisplay.html?id=raspberrypi).

The sites go down or are disabled from time-to-time due to the high demand.
The sooner you can get on, the better.

You can also run an [RPi Emulator](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=26&t=5743)
which is available on [sourceforge](http://sourceforge.net/projects/rpiqemuwindows/).

Accessories
---

You'll likely want to pick up a few things from [adafruit](http://adafruit.com), [monoprice](http://monoprice.com), and Amazon:

  * a [breakout board](http://adafruit.com/products/801)
  * a [breadboard](http://adafruit.com/products/65)
  * some fancy [breadboarding wire](http://adafruit.com/products/153)
  * maybe the [RPi merit badge](https://www.adafruit.com/products/906) for your laptop bag
  * perhaps a [RPi enclosure](https://www.adafruit.com/products/859)
  * [micro usb cable](http://www.monoprice.com/products/search.asp?keyword=micro+usb)
  * [usb wall charger](http://www.monoprice.com/products/search.asp?keyword=usb+wall+charger)
  * usb-serial adapter - Tripp Lite, StarTech, Pluggable, and Sewell have all served me well
    * [Tripp Lite](http://www.amazon.com/Keyspan-USA-19HS-Hi-Speed-supports-Sequence/dp/B0000VYJRY) - supposedly works even on Windows (and of course Mac and Linux)
      * Spendy, but better build quality
      * Texas Instruments Chipset
      * Works on Mac, Linux, and Windows
      * [Drivers](http://www.tripplite.com/en/products/model.cfm?txtModelID=3914)
    * [Pluggable](http://www.amazon.com/Plugable-Adapter-Prolific-PL2303HX-Chipset/dp/B00425S1H8/ref=pd_cp_e_1), [Sewell](http://www.amazon.com/USB-Serial-Adapter-PL2303HX-Chipset/dp/B0071OSXYS/ref=pd_cp_e_3) - works in Mac / Linux (maybe windows?)
      * Prolific PL2303HX Chipset
      * Works on Mac, Linux (maybe windows?)
      * [Drivers](http://prolificusa.com/pl-2303hx-drivers/) [2](http://plugable.com/drivers/prolific/)
    * [StarTech](http://www.amazon.com/StarTech-com-Serial-Adapter-Retention-ICUSB2321F/dp/B004ZMYTYC/ref=sr_1_10?s=electronics&ie=UTF8&qid=1341858108&sr=1-10&keywords=ftdi+usb+to+serial)
      * FTDI Chipset
      * Works on Mac, Linux (maybe windows, but some report trouble)
      * [Virtual COM Port Drivers](http://www.ftdichip.com/Drivers/VCP.htm)

What do with it?
===

So you've waited a long three to six months and your RPi
(as it is effectionately called in the community)
has arrived.

Now what to do?

Well, that's up to you, but there are some [ideas floating around](http://www.designspark.com/theme/raspberrypi).


Getting Started
===

I've found that the community documentation is a bit fragmented so I'm compiling a list of notes.

Downloading a System Image (Arch)
---

If you have your RPi in hand then the next thing you'll want to do is put an image on the SD Card to play with.

I recommend doing the polite thing and downloading via BitTorrent,
but nothing is stopping your from being a bit-hogger and sapping the main servers.

If you haven't used BitTorrent before, it's easy enough use with Transmission.
  * Mac [Transmission.app](http://www.transmissionbt.com/download/) or `brew install transmission`
  * Linux `apt-get install transmission`
  * [Transmission for Windows](http://sourceforge.net/projects/wintransmission/files/latest/download)

Having played with both Arch and Debian, Arch seems to be much much faster, but we can download both.

    RPI_ARCH_RELEASE="13-06-2012"
    cd ~/Downloads
    wget http://downloads.raspberrypi.org/images/archlinuxarm/archlinuxarm-${RPI_ARCH_RELEASE}/archlinuxarm-${RPI_ARCH_RELEASE}.zip
    # or transmission-cli -m "http://downloads.raspberrypi.org/images/archlinuxarm/archlinuxarm-${RPI_ARCH_RELEASE}/archlinuxarm-${RPI_ARCH_RELEASE}.zip.torrent"
    unzip archlinuxarm-${RPI_ARCH_RELEASE}.zip -d ./

Loading the Image to an SD Card
---

  0. Get a blank (or blankable) SD card (or MicroSD with adapter), but don't plug it in to your computer yet (or unplug it if you have).

  1. Run `ls -l /dev/rdisk* /dev/sd*` and note the output. Particularly you're looking for the highest value of disk such as `/dev/rdisk0s3` or `/dev/sdb2`

  2. Insert the SD card into your computer

  3. Run `ls -l /dev/rdisk* /dev/sd*` again and look for the newest highest value such as `/dev/rdisk1s3` or `/dev/sdc2` or possibly `/dev/mmcblk0p1`
    * On OSX ignore `/dev/sdt`

  4. Unmount the disk if it was automounted. Note that graphical tools may also eject (power off) the disk
        # OSX uses `diskutil umount`
        sudo diskutil umount /dev/rdisk1s1
        sudo diskutil umount /dev/rdisk1s2
        # Linux uses umount
        sudo umount /dev/sdc2
        sudo umount /dev/sdc3

  5. Now copy the disk image to the disk (not a partition) so `/dev/rdisk1` rather than `/dev/rdisk1s3` and `/dev/sdc` rather than `/dev/sdc2`

          RPI_ARCH_RELEASE="13-06-2012"
          sudo dd bs=1m if=./archlinuxarm-${RPI_ARCH_RELEASE}/archlinuxarm-${RPI_ARCH_RELEASE}.img of=/dev/rdisk1

  6. If the drive automounts when `dd` completes, unmount it again (as per above)

  7. Eject-o el sd-card-o

Configuring Arch
---

  0. Login as root:root
  1. Change your password with `passwd`
  2. Create an arch key with
          pacman -S haveged # generates entropy based on CPU ticks
          haveged -w 1024
          pacman-key --init # must have LOTS of entropy to generate key
          pkill haveged
          pacman -Rs haveged

  4. Update and install normal packages. Note that you should NOT CANCEL the upgrade to continue the upgrade
          pacman -Syyu
          # do NOT CANCEL the current opertation to update pacman first (or any other package)
          # NOTE: I had to manually replace `/etc/pam.d/login` with `/etc/pam.d/login.pacman` after the install

          pacman -Sy \
            sudo \
            openssh \
            git \
            base-devel \
            vim \
            zsh \
            screen \
            rsync \
            mlocate \
            htop \
            openssl \
            nodejs \
            sqlite \

  5. Create a user (and make sure to put him in the group `wheel`), give it sudo, and disable root ssh.

          adduser mynewuser
          visudo # uncomment the first `wheel` line
          vim /etc/ssh/sshd_config # uncomment / update the following line
          # set PermitRootLogin no

  6. Update the RPi Firmware and allocate 192/64 MiB RAM for CPU/GPU.

          wget http://goo.gl/1BOfJ -O /usr/bin/rpi-update && chmod +x /usr/bin/rpi-update
          rpi-update 192
          reboot

  7. Resize the sd card partition to its full size

          sudo fdisk -l /dev/mmcblk0 # note the Start of the root (probably second) partition
          sudo fdisk /dev/mmcblk0
          > d
          > 2
          > n
          > p
          > 2
          > # the number which is the start of the root partition
          > w

          sudo reboot
          resize2fs /dev/mmcblk0p2

Hardware Hacking
===

When you get to the point that you want to start having your RPi interact with the world around you,
you're going to want something like the [sensor pack from adafruit](http://www.adafruit.com/products/176)
and I'll save you some time by telling you that the links you'll need are right here:

  * [Low-Level Peripherals](http://elinux.org/Rpi_Low-level_peripherals)
  * [Verified Peripheral Pinout](https://projects.drogon.net/raspberry-pi/wiringpi/pins/)
  * [CPU Pinout](http://elinux.org/RPi_BCM2835_Pinout)
  * [CPU Signals](http://elinux.org/RPi_BCM2835_Signals)
  * [Raspberry Pi Schematics](http://www.raspberrypi.org/wp-content/uploads/2012/04/Raspberry-Pi-Schematics-R1.0.pdf)
  * [Broadcom BCM2835 Datasheet (pdf)](http://farnell.com/datasheets/1521578.pdf)

Das Blinkin' Lights (how to connect an LED)
---

0. Connect a red breadboard wire (or any wire) to GPIO 17 on the main header and a back breadboard wire to ground.

1. Wrap a resistor (red red brown gold should do) around one end of an LED.

2. Test the LED by connecting the longer positive (hot) lead to the 5v power and the other to the ground wire.

3. Now attach the long lead (positive) to the red wire. The light will probably be off.

      sudo su -

      ls /sys/class/gpio
      echo "17" > /sys/class/gpio/exports

      ls /sys/class/gpio/
      ls /sys/class/gpio/gpio17
      echo "out" > /sys/class/gpio/gpio17/direction # means that this GPIO sources (outputs) 3.3v

      while true
      do
        echo "0" > /sys/class/gpio/gpio17/value
        sleep 1
        echo "1" > /sys/class/gpio/gpio17/value
        sleep 0.5
      done

4. The LED should now be blinking on and off. Huzzah!


Serial Port
---

By default the TTL serial port is setup for debugging (precisely what `ctrl+alt+f1` emulates with a desktop system).
To use it for communication with something such as a GPS or Accelerometer you need to disable it as a login prompt.
See Joshua Elliott's blog post on [Raspberry Pi Serial](http://joshuacelliott.ruhoh.com/code/linux/raspberry-pi-serial/) for more info.
