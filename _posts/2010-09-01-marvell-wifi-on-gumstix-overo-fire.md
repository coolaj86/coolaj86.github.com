---
layout: article
title: Setting up Gumstix Overo Fire
categories: gumstix
updated_at: 2010-09-01
---
Goal
====

Create a Gumstix experience that I can live with.

This is going to live in my living room accessible via wireless

Setup with the Out-of-Box system
====

First Things First
----

  # secure my gumstix from netbots (it will have a public IP)
  passwd

  # make vim usable
  echo ":set nocompatible" >> /etc/vimrc

  # make the filesystem searchable with locate
  updatedb

Wireless Networking
====

  * [Gumstix Wiki: Ad-hoc (P2P) WiFi - Outdated](http://www.gumstix.net/wiki/index.php?title=Creating_an_Ad-hoc_Wireless_Network)
  * [Gumstix Mailing List: Overo Fire WiFi Woes](http://old.nabble.com/WiFi-on-Overo-Fire-tt28865937.html#a29272620)
    * lists firmware file to download
    * gives valid instruction for hotspot access

Disable Bluetooth

    # (disable) bluetooth 
    /etc/init.d/bluetooth stop
    echo 0 > /sys/class/gpio/gpio164/value # reset
    echo 0 > /sys/class/gpio/gpio15/value  # disable
    update-rc.d -f bluetooth remove
    update-rc.d -f blueprobe remove

Errors You may Encounter
-----

Error:

    Configuring network interfaces... ADDRCONF(NETDEV_UP): wlan0: link is not ready
    udhcpc (v1.13.2) started
    run-parts: /etc/udhcpc.d/00avahi-autoipd exited with code 1
    Sending discover...
    Sending discover...
    Sending discover...
    run-parts: /etc/udhcpc.d/99avahi-autoipd exited with code 1
    No lease, failing

Fix:

    # This helps... kinda... I think, but it may be solely the manual config below that gets it working
    /etc/init.d/NetworkManager stop
    update-rc.d -f NetworkManager remove
    opkg remove networkmanager network-manager-applet

Error:

    can't create /var/lib/dhcp/dhclient.leases: No such file or directory

Fix:

    ln -s /var/lib/dhcp3 /var/lib/dhcp

Enable WiFi
----

Manually

    ESSID='Apt Rm 04 & 05' # CHANGE THIS to suite your situation
    ifdown -a
    ifconfig wlan0 down
    iwconfig wlan0 bit 18M fixed
    iwconfig wlan0 essid "${ESSID}"
    iwconfig wlan0 mode managed
    iwconfig wlan0 key off
    dhclient wlan0

`/etc/networking/interfaces`:

    allow-hotplug wlan0
    auto wlan0
    iface wlan0 inet dhcp
            pre-up iwconfig wlan0 mode managed
            pre-up iwconfig wlan0 essid "Apt Rm 04 & 05"
            pre-up iwconfig wlan0 key off

This also works, note that there are **no quotes**:

    allow-hotplug wlan0
    auto wlan0
    iface wlan0 inet dhcp
        wireless_mode managed
        wireless_essid Apt Rm 04 & 05
        wileless_key off


Better(?) Marvel Firmware
----

    wget http://old.nabble.com/file/p29272793/Marvell_firmware_9.70.7.zip
    unzip Marvell_firmware_9.70.7.zip
    locate sd8686
    mv 9.70.7.p0/sd8686-9.70.7.p0.bin /lib/firmware/sd8686.bin

DMZ
----

I have a dd-wrt router on which I added my Gumstix to have a static IP and be the DMZ.

  0. Log in to `dd-wrt`
  0. Click `services`
  0. Click `add` under `DHCP Server`
  0. Enter the gumstix `hwaddr`, and desired `hostname` and desired `ipaddr`
  0. 'save' first (do NOT `apply` first)
  0. okay, now 'apply'
  0. Click `NAT/QoS`
  0. Click `DMZ`
  0. Add gumstix
