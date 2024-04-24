+++
title = 'Why I Stopped Using Nixos'
date = 2024-04-23T17:55:58-04:00
draft = false
+++

In my opinion NixOS is a poor choice for desktop Linux and here I will explain why.

I used NixOS for about 4 months and had a configuration that I liked, but ultimately I switched back to Arch.

- Poor documentation. I have to read the source code just to learn how to install and configure certain basic packages? I thought NixOS was supposed to save me time but maybe I misunderstood. I'd rather spend a couple hours doing a manual system setup than spend days trying to figure out how to configure it declaratively.
- Home-manager doesn't support all the apps with configuration files that I use. If I have to keep a dotfiles folder anyway, then I might as well use dotfiles instead of home-manager, which really just convolutes the config and makes it harder to understand. With Arch, nothing is sugarcoated and so it's easy to fix any issues that arise.
- If I have to back up my home folder anyway, I might as well backup my system. I don't need to be able to deploy it in minutes, I can Wait an hour. My desktop isn't a mission critical system or anything like that.


NixOS does have many pros which benefit some people, like reproducibility, and quick deployments. But it turns out, those things don't really matter much to me.