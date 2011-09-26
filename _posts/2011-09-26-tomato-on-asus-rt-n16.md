---
layout: article
uuid: EE9F8897-70C4-4448-BF10-3839E8254798
title: Tomato on ASUS RT-N16
name: tomato-on-asus-rt-n16
created_at: 2011-09-26
updated_at: 2011-09-26
categories: thesystemisntdown
---

DON'T PANIC
===

Installing Tomato on the ASUS RT-N16 is one of the easiest things I've ever done in my life!

Don't let other tutorials or blog posts confuse you, this is as easy as cake!

Quick Setup
===

For those of you that have used DD-WRT before or otherwise don't need detailed instructions, it's this simple:

  0. Download and `unrar` [TomatoUSB's](http://tomatousb.org/download) Kernel 2.6 MIPSR2 Ext package (I used [Build 54][build-54]) as `tomato-rt-n16.trx`
  0. Set your IP address to **192.168.1.42**
  0. Plug into **port 1** on the router
  0. **Soft reset** the router using the ASUS software's reset button at <http://admin:admin@192.168.1.1/Advanced_SettingBackup_Content.asp>
  0. **Hard reset** the router using the [30/30/30 method][30-30-30-reset] with the **`WPS`** (not `restore`) button
  0. **Power cycle** while **holding the `restore`** (not `WPS`) button until the blue light on port 1 flashes repeatedly
  0. When `ping 192.168.1.1` confirms that the router is responding, load the firmware:

          tftp 192.168.1.1
          binary
          put tomato-rt-n16.trx
  0. **Wait 90 seconds** after tftp reports success before power cycling
  0. Set your IP address back to **DHCP**
  0. Power Cycle the router
  0. Enjoy Tomato

[build-54]: http://downloads.sourceforge.net/project/tomatousb/Experimental%20%28beta%29/K26-MIPSR2/tomato-K26USB-1.28.9054MIPSR2-beta-Ext.rar?r=http%3A%2F%2Ftomatousb.org%2Fdownload&ts=1317056590&use_mirror=iweb
[30-30-30-reset]: http://www.dd-wrt.com/wiki/index.php/Reset_And_Reboot#Hard_Reset_.28.2230.2F30.2F30_reset.22.29

Detailed Instructions
===

See <http://patricksheedy.net/blog/simple-tomato-firmware-install-on-asus-rt-n16-router/> and or <http://dd-wrt.ca/wiki/index.php/Asus_RT-N16>

Prereqs:

  * [30/30/30 reset](http://www.dd-wrt.com/wiki/index.php/Reset_And_Reboot#Hard_Reset_.28.2230.2F30.2F30_reset.22.29)
  * [Power Cycling a router](http://www.dd-wrt.com/wiki/index.php/Reset_And_Reboot#Power_Cycling)
  * [How to set your IP address in Ubuntu](http://www.howtogeek.com/howto/19541/how-to-assign-a-static-ip-to-an-ubuntu-10.04-desktop-computer/) or [the easy way](http://www.howtogeek.com/howto/ubuntu/change-ubuntu-server-from-dhcp-to-a-static-ip-address/)
  * [How to set your IP address in OS X](http://www.howtogeek.com/howto/22161/how-to-set-up-a-static-ip-in-mac-os-x/)
  * [How to set your IP address in Windows](http://www.howtogeek.com/howto/19249/how-to-assign-a-static-ip-address-in-xp-vista-or-windows-7/)
    * [tfpt in windows](http://www.dd-wrt.com/wiki/index.php/TFTP_flash#Windows)
