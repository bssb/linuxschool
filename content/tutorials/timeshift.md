+++
title = 'Timeshift using btrfs'
date = 2024-04-27T11:21:58-04:00
draft = true
+++

## Creating subvolumes
Timeshift requires you to have your root subvolume named @, and your home needs to either be a subvolume at @home or something on another drive.

Also, you need to have an entry in your /etc/fstab declaring your root partition (I didn't have one, because an Arch article told me to take it out to speed up a "costly reboot"). Most people probably already have this line present.

## Timeshift configuration

The GUI wizard is fairly straightforward but you can also use the command line (no wizard).


## Adding snapshots to GRUB's menu
Install the package grub-btrfs.
