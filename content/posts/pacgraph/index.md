+++
title = 'Identifying unused dependencies with pacgraph'
date = 2024-04-24T06:35:38-04:00
draft = false
+++

Pacgraph provides a graph of your system's package dependencies. You can find it in the AUR:
{{< highlight bash >}}
paru -S pacgraph
{{< / highlight >}}

It's helped identify packages that I had no idea were installed. 

{{< highlight bash >}}
sudo pacman -R packages_to_remove
{{< / highlight >}}

After you've removed a few packages, you can run use this one-liner to remove any orphaned dependencies:

{{< highlight bash >}}
sudo pacman -Rns $(pacman -Qtdq)
{{< / highlight >}}

Or my script remove_orphans.sh that includes error checking in case there are no orphans:

{{< highlight bash >}}
#!/bin/bash
orphans=$(pacman -Qtdq)
if [ -z "$orphans" ]
then
    echo "No orphans to remove"
else
    sudo pacman -Rns $orphans
fi
{{< / highlight >}}

And then you can run pacgraph a second time after removing some packages, it will show other packages that it didn't previously have room for.

I use this bash alias to quickly run it:
{{< highlight bash >}}
alias pg='pacgraph -f /tmp/pacgraph && feh /tmp/pacgraph.svg'
{{< / highlight >}}

That can go in your ~/.bashrc or wherever else your aliases are stored. Mine looks like this:

[![Dependency graph](pacgraph.svg)](pacgraph.svg)

I made the mistake of installing kde-applications when switching to Plasma 6, so now I have a lot of packages to remove.