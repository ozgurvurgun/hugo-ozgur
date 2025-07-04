---
title: How to install nixos to a vultr instance
description:
date: 2019-07-17 
draft: false
tags:  nix #nixos #vultr #howto #en
---


Nixos is a Linux distro working in nix ecosystem. 

> NixOS is a GNU/Linux distribution that aims to improve the state of the art in system configuration management.
<!--more-->

You need to create an instance with the configuration at the below:

- doesn't matter which server.
- doesn't matter which location.
- in the server type option, select iso library and find Nixos 18.x
nixos.18.x)
- doesn't matter server size but at least we need 1GiB ram.
- doesn't matter all of the other options.


After you deploy the server, you wait until complete server initialization. 

Go to the products page and click your server name. You should see view console icon right top of the server management page. Click it and connect the terminal.

Possibly you would face some keyboard layout problems after connecting the terminal from browser. Don't get stuck that problem. We need only copy/paste initial configuration.

Vultr machines have the legacy boot option. Firstly we need to create disk partitions.

We need to know the device name for creating a partition. You can find run `fdisk -l` command. My device name is `/dev/vda`

Create an MBR partition table.

`parted /dev/vda -- mklabel msdos`

Add root partition

`parted /dev/vda -- mkpart primary 1MiB -8GiB`

And, add a swap partition

`parted /dev/sda -- mkpart primary linux-swap -8GiB 100%`


After that, you need to make formatting configuration. 

For initializing ext4 format for the root partition.

`mkfs.ext4 -L nixos /dev/vda1`

For creating swap partition run mkswap.

`mkswap -L swap /dev/vda2`



Before installation, you need to make some little configurations. Mounting nix disk and activation swap area.

Mount nixos iso to /mnt folder.

`mount /dev/disk/by-label/nixos /mnt`

Activate swap area.

`swapon /dev/vda2`

And now we can generate the initial config.

`nixos-generate-config --root /mnt`

This command generates all initial disk and device configuration for you.

After creating the initial configuration completed, you need to change a few configurations. At first, you need to set `boot.loader.grub.device` option.

Open `/mnt/etc/nixos/configuration.nix` file with your favorite editor vim or nano.

Find that option and set as `boot.loader.grub.device = "/dev/vda";` and enable the grub with this option ` boot.loader.grub.enable = true;`

At the last, you need to enable OpenSSH service for connecting to your server with ssh after installation. And you need to add firewall access to 22 port.

For that, you can add that options your configuration file.

```
services.openssh.enable = true;
services.openssh.permitRootLogin = "yes";
networking.firewall.allowedTCPPorts=[ 22 ];
```

Ok. Now you can save and exit the file, and install your Nixos.

`nixos-install`

It asks to you for root password after you run command. 

If everything went well, you should remove installation media from machine and reboot. Go to your Vultr server manage page and remove iso from `settings->custom iso` section.
The server automatically rebooted after that.

Congrats. You can now connect your Nixos server over ssh.

