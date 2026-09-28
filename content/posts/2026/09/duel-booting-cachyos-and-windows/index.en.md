---
title: "Duel Booting Cachyos and Windows"
subtitle: ""
date: 2026-09-28T13:35:39Z
lastmod: 2026-09-28T13:35:39Z
draft: true
authors: [Gaz]
description: ""
tags: ["duel-booting"]
categories: []
series: ["duel-booting-adventures"]
featuredImage: "featured-image.png"
---

In this post I will go through the process I went through to duel boot an existing Windows setup, I strongly recommend against using an existing setup as I ran into a number of roadblocks because I not want to reinstall windows, it is always better to reinstall windows with your partitions already setup, or using a separate drive entirely.

This post assumes that you already have an install of Windows and you will be adding linux to duel boot

## We need to disable some stuff

To successfully duel boot you will first need to disable secure boot (don't worry this can be re-enabled later) this isn't something I can guide you through as it depends on your motherboard so please refer to a guide for your specific motherboard or laptop.

Next the following needs to be disabled in windows
- Fast startup
- Hibernation (if left on it can cause drive locking issues)
- Bitlocker drive encryption (if you use it)

Fast startup and hibernation can both be disabled with the following command in an elevated command prompt

```powershell
powercfg /h off
```

Bitlocker encryption can be turned of by right clicking your drive in file explorer and going through the prompts to disable it.

## Reducing the size of a C drive

{{< admonition type=note title="Note" open=true >}}
Skip this part if you already have a windows partition of the correct size or you are installing on separate drive
{{< /admonition >}}

I want both OS' side by side on the same drive with the cost of ram and storage at the moment I simply cannot afford to have a separate drive just for linux to sit on and as mentioned in the previous post I will be sharing the additional drives between windows and linux for game storage.

In practice reducing the size of your C drive is a simple process, clear some space, open disk management, shrink volume, but windows likes to put some of its crucial files in strange places which is why I ran into some issues.

#### The issue

After windows had spent some time calculating how much space I could shrink the volume by, I was greeted with box that looked like

TODO: Get image of shrink volume dialog box

Now it looks like it's worked however I however at the time I had freed up over half the drive with the intent to split the drive evenly down the middle, but windows was only allowing me to shrink the volume by around 120GB enough for linux but not what I wanted.

After some research there was two things windows was holding the shrink for, its page file (essentially the Windows equivalent to swap) and system protection, yes this is the one that gives you those "useful" restore points, so back into windows dialog boxes I went.

Firstly open a run dialog box with Windows key + r and type in sysdm.cpl and hit enter

TODO: get image of run box

Open the advanced tab and under performance click settings

TODO: get image of advanced tab with circled performance setting button

Under the `virtual memory` section click on change

TODO: get picture of virtual memory section

Uncheck the box next to Automatically manage paging file size for all drives, select your C drive, and select the `no paging file` radio button.

Make sure you press the set button and accept the warning other wise this change will not save.

TODO: get image to show the above

Now to disable system protection, press Windows key + r again and type sysdm.cpl again

Click the System protection tab

Select the C drive and choose disable system protection

TODO: get image of system protection menu

After doing all this and rebooting I was then able to shrink my C drive to the size I wanted

{{< admonition type=note title="Note" open=true >}}
I highly recommend reverting these changes once you have shrunk you the drive to the size you need
{{< /admonition >}}

## Installing Linux

Now we are at the proverbial scary part and if you are following along I hope you have a good backup of everything important you have stored on windows.

Now as discussed in the previous post I have chosen Cachyos and they actually have a [really good guide](https://wiki.cachyos.org/installation/installation_on_root/) so I'm really not going to rewrite a whole guide that already exists but I will document the options I chose

I booted in UEFI mode as that matches my Windows install

For duel booting Limine seemed to be the best option in terms of support and ease of setup any part of the official guide where Limine needs something specific I just chose the default options I'll report back if I find any issues later down the line

I manually partitioned the space on the drive not only is this recommended in this situation I do this anyway

My partition table ended up looking like this

```
├─nvme0n1p5 259:5    0     4G  0 part /boot
├─nvme0n1p6 259:6    0  15.6G  0 part [SWAP]
└─nvme0n1p7 259:7    0 233.7G  0 part /
```
{{< admonition type=note title="Note" open=true >}}
This is the output of `lsblk` which is in GiB so bit different in representation to GB
{{< /admonition >}}

And for my desktop environment I chose KDE Plasma my regular desktop of choice with Linux desktops for day to day use.

Once I picked a password that was it for installing CachyOS, it booted up drivers included etc a few app installs later and it was ready for me to customise and get steam working on the NTFD drives but I'll cover that in another post.

## Getting back to windows

You'll notice that Limine has taken over boot loader duties at this stage it should have picked windows up automatically if it doesn't run this in a terminal

```sh
sudo limine-scan
```

I did also look into a simple way to get to windows from Linux without having to catch the prompt this can be done with efibootmgr

Firstly run the following

```sh
sudo efibootmgr
```

This should give you an output like this

```sh
BootOrder: 0002,0000,0003,0004,0005,0006
Boot0000* Windows Boot Manager	HD(1,GPT,b2c9cf0b-5bc8-44d0-ad45-2d633000387f,0x800,0x32000)/\EFI\Microsoft\Boot\bootmgfw.efi57494e444f5753000100000088000000780000004200430044004f0042004a004500430054003d007b00390064006500610038003600320063002d0035006300640064002d0034006500370030002d0061006300630031002d006600330032006200330034003400640034003700390035007d00000000050100000010000000040000007fff0400
Boot0002* Limine	HD(5,GPT,6c9f3f9c-6cec-48a8-b71c-bd5f9130b3cd,0x54a8a000,0x800000)/\EFI\limine\limine_x64.efi
Boot0003* UEFI OS	HD(5,GPT,6c9f3f9c-6cec-48a8-b71c-bd5f9130b3cd,0x54a8a000,0x800000)/\EFI\BOOT\BOOTX64.EFI0000424f
Boot0004* UEFI:CD/DVD Drive	BBS(129,,0x0)
```

Look for the one with `Windows Boot Manager` and make note of the number next to `Boot`, you can then use this to temporarily switch the boot order in Limine I pair this with a reboot command under and alias that looks like this.

```sh
sudo efibootmgr -n 0000 && reboot
```

The above set the next boot option to windows and then gracefully reboots my desktop into windows.

## Final thoughts

Honestly this has been less painful then the last time I tried duel booting linux and windows, although that was NixOS and windows so I do wonder if CachyOS should taking most if not all of the credit here

I do recommend just biting the bullet and doing a fresh install of windows if you going down the same route as me better yet get a separate drive for linux in heavily recommended.
