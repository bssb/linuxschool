+++
title = 'Automated system backups with borg and borgmatic'
date = 2024-05-08T18:17:13-04:00
tags = ['backup']
draft = false
+++

This article will cover the basics of setting up a borg repository, creating backups, and how to run them automatically.

## Initialize the repository

You should have a separate borg repository for each system you want to back up. Borg docs say that it's possible to share a repository with multiple hosts, but not recommended. 

My laptop has a secondary drive that I am dedicating for backups, and I have mounted it at /backup. I'm going to create a repository called *borg-hostname*. My laptop's hostname is *p* because I like single-letter names.

{{< highlight bash >}}
sudo borg init --encryption=none /backup/borg-p
{{< / highlight >}}
## Create an archive

Whenever you want to back up your files, you create a new archive inside your repository. Deduplication makes incremental backups unnecessary. Just take a full backup every time, and borg figures out which files have been changed, only storing one copy of each file. Before deduplication, incremental backups were difficult to comprehend.

Since this command takes up so many lines, I made a script file to contain it:

{{< highlight bash >}}
#!/usr/bin/env sh
ROOT_DIR="/"
borg create --stats --progress \
    --exclude "/boot/efi/*" \
    --exclude "/backup/*" \
    --exclude "/dev/*" \
    --exclude "/mnt/*" \
    --exclude "/proc/*" \
    --exclude "/run/*" \
    --exclude "/tmp/*" \
    --exclude "/sys/*" \
    --exclude "/var/cache/pacman/pkg/*" \
    --exclude "/var/run/*" \
    --exclude "/var/lock/*" \
    /backup/borg-p::{hostname}-{now:%Y-%m-%d} $ROOT_DIR
{{< / highlight >}}

Borg fills in *{hostname}* and  *{now:%Y-%m-%d}* with the hostname and date. You don't have to change them manually.

## Optional: test your backup

Now try and restore the backup you just created. Create a new partition somewhere that you can boot from. If you need to resize your partitions to make room, you can boot 
[SystemRescue](https://www.system-rescue.org/Download/) from USB to do that.

My new empty partition is mounted at /mnt/test.

borg-p is the name of my repository and p-2024-04-29 is the archive I created in the previous step. You have to cd into the target directory first.

```
cd /mnt/test
sudo borg extract /backup/borg-p::p-2024-04-29
```

Now edit /mnt/test/etc/fstab and update the UUID of the root filesystem to the new partition.

This part varies a little bit depending on your boot loader configuration, but you'll want to copy your existing boot loader entry for Arch into a new entry, and change the UUID of root partition just like you did for fstab.

Reboot. If you're able to boot into the restored partition, great! Proceed to the next step for automation instructions.

## Automate backups with borgmatic

I'm actually not going to be using the borg script I wrote in the first section. That was just a test to make sure borg works. Instead of calling borg directly, I'm going to use borgmatic which will in turn call borg.

To generate the default borgmatic config files, run:
```
sudo borgmatic config generate
```

That generates a rather long config however, and most of the sections aren't needed.
My config.yaml is very simple, this is what it looks like:
{{< highlight yaml >}}
repositories:
    - path: /backup/borg-p
      label: local

patterns:
    - R /
    - '- /boot/efi/*'
    - '- /backup/*'
    - '- /dev/*'
    - '- /mnt/*'
    - '- /proc/*'
    - '- /run/*'
    - '- /tmp/*'
    - '- /sys/*'
    - '- /var/cache/pacman/pkg/*'
    - '- /var/run/*'
    - '- /var/lock/*'
{{< / highlight >}}

You can run borgmatic manually to create an archive:
```
sudo borgmatic create
```

or set up a systemd timer to run at intervals:
```
sudo systemctl enable borgmatic.timer
```
By default this is run daily. You can change the interval by editing the timer:
```
sudo systemctl edit borgmatic.timer
```

## Offsite backups
In this article, I backed up my files to another hard drive inside of my laptop. Ideally, I should be backing up to a drive that is not connected to my computer. Preferably over the network, because I don't want to plug in a USB drive manually every time. BorgBase has plans starting at $2/month, and that's what I'm planning on setting up soon.
## Resources
- [Official borg documentation](https://borgbackup.readthedocs.io/en/stable/index.html)
- [Borgmatic documentation](https://torsion.org/borgmatic/)
- [Borg backup - Arch Wiki](https://wiki.archlinux.org/title/Borg_backup)
- [Borgmatic - Arch Wiki](https://wiki.archlinux.org/title/Borgmatic)
