+++
title = 'Automated system backups with borg and borgmatic'
date = 2024-05-08T18:17:13-04:00
draft = true
+++

This guide will cover setting up a borg repository, creating backups, and also show how to run them automatically.

## Initialize the repository

You should have a separate repository for each system you intend to back up. Borg documentation indicates that it's possible to share a repository with multiple hosts, but not recommended.

{{< highlight bash >}}
sudo borg init --encryption=none /backup/borg-hostname
{{< / highlight >}}

## Create an archive

Whenever you want to back up your files, you create a new archive inside your repository. Deduplication makes it unnecessary to do incremental backups, because borg figures out which files have been changed and only stores one copy of each file.

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

## Test your backup

Before going any further I think it's a good idea to see if that backup works. Create a new partition somewhere that you can boot from. If you need to resize your partitions to make room, you can boot [SystemRescue](https://www.system-rescue.org/Download/) from USB to do that.

The directory I mounted my new partition to is /mnt/test.

borg-p is the name of my repository and p-2024-04-29 is the archive I created in the previous step.
You have to cd into the target directory, there's no borg option to specify it.

```
cd /mnt/test
sudo borg extract /backup/borg-p::p-2024-04-29
```

Now it's important to edit /mnt/test/etc/fstab and update the UUID of the root filesystem to the new partition.

This part varies a little bit depending on your boot loader configuration, but you'll want to copy your existing boot loader entry for Arch into a new entry, and change the UUID of root partition just like you did for fstab.

Reboot. If you're able to boot into the restored partition, great! Proceed to the next step for automation instructions.

## Automate backups with borgmatic

I'm actually not going to be using the one-line script I wrote in the previous section. That was just to demonstrate backing up manually without borgmatic, and test to see that it works.

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

## Resources
- [Official borg documentation](https://borgbackup.readthedocs.io/en/stable/index.html)
- [Borgmatic documentation](https://torsion.org/borgmatic/)
- [Borg backup - Arch Wiki](https://wiki.archlinux.org/title/Borg_backup)
- [Borgmatic - Arch Wiki](https://wiki.archlinux.org/title/Borgmatic)
