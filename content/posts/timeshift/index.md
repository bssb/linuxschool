+++
title = 'Hourly btrfs snapshots using Timeshift'
date = 2024-05-10T11:21:58-04:00
tags = ['backup']
draft = true
+++

## Overview
Timeshift is not exactly a backup solution, but it makes rolling back a broken system much easier. If something happens during a *pacman -Syu* and the package database is corrupted, I use Timeshift to roll back a snapshot. I also don't use it for /home, only my root partition.
## Creating subvolumes
Timeshift requires you to have your root subvolume named @, and your home needs to either be a subvolume at @home or something on another drive.

Also, you need to have an entry in your /etc/fstab declaring your root partition (I didn't have one, because an Arch wiki page recommended removing it to avoid a "costly remount"). Most people probably already have this line present.

## Timeshift configuration

The GUI wizard is fairly straightforward but you can also use the command line (no wizard).

## Adding snapshots to GRUB's menu
Install the package grub-btrfs.

## Restoring a snapshot
If, for example, the system crashes during an upgrade and pacman breaks, here is how to restore a previous snapshot. What I do is I boot a [SystemRescue](https://www.system-rescue.org) USB and restore my snapshot from within the Timeshift GUI. It's straightforward if you haven't done it.

If you have configured grub-btrfs, then you can boot from another snapshot, then mount your original subvolume at /mnt/arch, chroot into it and fix what needs to be done, and then reboot. This way, you won't need SysRescueCd.

## Other notes
Timeshift is now maintained by the Linux Mint project.

