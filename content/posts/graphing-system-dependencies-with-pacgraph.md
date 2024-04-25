+++
title = 'Tracking Down Unneeded Dependencies With pacgraph'
date = 2024-04-24T06:35:38-04:00
draft = true
+++

After you've removed a few packages, you can run use this oneliner to remove any orphaned dependencies:

{{< highlight bash >}}
    sudo pacman -Rns $(pacman -Qtdq)
{{< / highlight >}}

Or my script that includes error checking in case there are no orphans:


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

I use this bash alias to quickly run it:
{{< highlight bash >}}
alias pg='pacgraph -f /tmp/pacgraph && feh /tmp/pacgraph.svg'
{{< / highlight >}}

That can go in your ~/.bashrc or wherever else your aliases are stored.
