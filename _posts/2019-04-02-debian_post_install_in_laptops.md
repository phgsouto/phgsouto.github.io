---
title: Debian post-install in laptops
layout: post
---

# Preface

This guide have the objective of providing a guide in how to install [Debian](http://debian.org) on a laptop.

The laptop that I use is an Lenovo ThinkPad x240, configuration as below:
* CPU: Intel i5-4300U (4) @ 2.900GHz
* GPU: Intel integrated graphics
* RAM: 8 GB single channel
* Disk: 240 GB SanDisk SSD PLUS SATA

The Debian version that I choose to run is [Debian Testing](https://wiki.debian.org/DebianTesting) (Currently version 10, Buster).

Lets go then!

# Things to know about Debian

Debian is a [Free](https://www.debian.org/intro/free) Operating system, this meaning that it'll not include any non-free software included in the installation media. This meaning that some hardware components of your machine may not function directly after installation.

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

# Updating the system
