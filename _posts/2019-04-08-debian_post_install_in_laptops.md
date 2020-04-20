---
title: Debian post-install in laptops
layout: post
---

# Preface

This guide have the objective of providing a guide in how to install [Debian](http://debian.org) on a laptop.

The laptop that I use is an Lenovo ThinkPad x240.

The Debian version that I choose to run is [Debian Testing](https://wiki.debian.org/DebianTesting) (Currently version 10, Buster).

Lets go then!

# Things to know about Debian

Debian is a [Free](https://www.debian.org/intro/free) Operating system, this meaning that it'll not include any non-free software included in the installation media. This meaning that some hardware components of your machine may not function directly after installation, and some multimedia files will not play out of the box.

In my case, the WiFi card (Intel Corporation Wireless 7260) driver was not installed by default. Don't worry, I'll teach how to install the proper driver on the next session.

# Installing the WiFi card driver

I like to install the firmware as soon as possible, to free me from the Ethernet cable.

Firstly, you need to figure out witch card you have. To do so, open up a terminal (konsole, gnome-terminal, xterm or similar) and execute the following command:

```bash
$ lspci | grep -i network
```

In my case I received the following response:
`03:00.0 Network controller: Intel Corporation Wireless 7260 (rev 83)`

Another common vendors are Atheros and Broadcom.

## Updating the sources.list

Although Debian will not include the non-free drivers in the install, it is possible to obtain the packages through the "official" repositories.
For that, we need to edit the `/etc/apt/sources.list`:

```bash
$ sudo nano /etc/apt/sources.list
```

If you installed debian without internet connection, your sources.list will be similar to this:

```sh
deb cdrom:[Debian GNU/Linux testing _Buster_ - Official Snapshot amd64 DVD Binary-1 20190327-09:46]/ buster contrib main

# deb http://security.debian.org/debian-security testing/updates main contrib
# deb-src http://security.debian.org/debian-security testing/updates main contrib

# deb http://deb.debian.org/debian/ testing main contrib
# deb-src http://deb.debian.org/debian/ testing main contrib
```

The only uncommented line is the cdrom line, this meaning that the only repository that you have ativated is the cdrom that was used to install the system.

So, in order to be able to use apt to get packages from the online repositories, we need to edit the file, by commenting the cdrom line and uncommenting the other ones. Also, we need to add the *non-free* branch to the repositories, because the packages that we want are on this branch.

After editing, the file must look like this:

```sh
#deb cdrom:[Debian GNU/Linux testing _Buster_ - Official Snapshot amd64 DVD Binary-1 20190327-09:46]/ buster contrib main

deb http://security.debian.org/debian-security testing/updates main contrib non-free
deb-src http://security.debian.org/debian-security testing/updates main contrib non-free

deb http://deb.debian.org/debian/ testing main contrib non-free
deb-src http://deb.debian.org/debian/ testing main contrib non-free
```

After saving the file, run the following command to refresh the apt database in your machine.

```sh
$ sudo apt update
```

## Installing the drivers

Now, the moment of truth: let's install the drivers for our WiFi card.
* For Intel, you must install the package `firmware-iwlwifi`
* For Realtek, you must install the package `firmware-realtek`
* For Atheros, you must install the package `firmware-atheros`

To install the package, run:
```sh
$ sudo apt install <package>
```

After the installation, reboot your machine and you must have WiFi capabilities.

## Updating the system

It is important to keep the system updated. To update the system, run:
```sh
$ sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y && sudo apt autoclean
```

What this command do:
* `update`: updates the internal catalog of software with the repositories information
* `upgrade -y`: upgrades the local packages with the new versions
* `autoremove -y`: removes the packages that are no longer needed
* `autoclean`: clean the local `*.deb` cache

After the execution, it is recommended, although not obligatory, to reboot the computer.

## Installing sensors and power management tools

Now we're going to install two packages that I consider essential for any machine type, specially laptops. The pachages are `tlp` and `lm-sensors`. TLP is an advanced power management tool, and `lm-sensors` is a hardware health monitoring package.

To install and start using them, run:

```sh
$ sudo apt install -y tlp lm-sensors
$ sudo systemctl enable tlp
$ sudo systemctl start tlp
$ sudo sensors-detect --auto
```

Tlp is the kind of software that you "install and forget". It is always running on the background and doing its thing.

If you want to see the information that lm-sensors is reading of your device, run `sensors`.

*Tip: run `watch sensors` if you want to continuously and automatically run the command*

## Enabling multimedia playback

For the same reason that the WiFi card driver was missing from the default debian installation, the multimedia codecs are missing as well. To install them, run the following:

```sh
$ sudo apt install -y libavcodec-extra
$ wget http://www.deb-multimedia.org/pool/non-free/w/w64codecs/w64codecs_20071007-dmo2_amd64.deb
$ sudo dpkg -i w64codecs_20071007-dmo2_amd64.deb
```
## And we're a go!

That's it, now you have a debian installation that can easily be used in day-to-day activities.

:)

