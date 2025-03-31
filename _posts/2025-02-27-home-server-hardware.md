---
title: "Home Server Hardware (HP Z240 SFF) - Why Used Workstations Make Great Home Servers"
categories:
  - blog 
tags:
  - home-server
---

tl;dr: The HP Z240 SFF Workstation makes a great budget home server with ECC, Intel AMT and space for 2x3.5" HDDs.

I've been running some form of home server since at least 2018, it's a great learning experience and allows me to self-host many useful services - a win-win! My chosen hardware platform has evolved over the years, starting with an original Raspberry Pi, then moving up to server hardware before finally settling with small form factor PCs. This post is going to outline some of my thoughts regarding home server hardware, with another post soon on the software side.

Small Form Factor PCs are great - they take up a lot less space compared to full towers, still have reasonable expansion and are widely available used. They aren't quite as compact as the [TinyMinyMicro](https://www.servethehome.com/introducing-project-tinyminimicro-home-lab-revolution/) format, but I believe the greater expansion options are worth the compromise - particularly for NAS use-cases.

Currently, I'm using an HP Z240 SFF Workstation which I believe makes an ideal server for the following reasons:

- Cheap and readily available used on eBay
- Lots of expansion: 2x3.5" drive bays, 2x2.5" drive bays (one is instead of an optical drive), 1x M.2 NVMe slot as well as multiple PCIe slots and SATA ports.
- ECC support (CPU and RAM dependent)
- Intel AMT support (CPU dependent)
- Low power consumption (~15W with no hard drives, ~25W with 2x3.5" HDDs and a 2.5gigabit NIC)
- Intel Quick Sync hardware-accelerated transcoding (CPU dependent) which is particularly useful for Jellyfin/Plex and Frigate.
- It's small!

![Exterior of the HP Z240 SFF](/assets/images/2025-02-27-home-server-hardware/exterior.jpg)

To elaborate, I'm using ZFS, and it's *generally* recommended to use ECC (Error correction code) memory if you can. Although there's lots of debate about this online. It certainly doesn't hurt to use ECC, although it's quite rare outside of server hardware, which is why it's particularly noteworthy these HP SFFs support it. ECC requires a Xeon or i3 CPU.

Additionally, Intel AMT (Active Management Technology) is a really handy feature. Which introduces basic remote management functionality independent of the operating system, typically a feature only found on servers. The exact functionality is dependent on which CPU you have installed, you'll either have full remote desktop functionality or just serial-over-LAN. I find it useful to be able to force my machine to reboot if the operating system freezes.

My specific machine has the following specifications:

- CPU: Intel i3-7100 - 2 cores / 4 threads
- RAM: 16GB DDR4 ECC
- OS drive: 500GB Crucial MX500
- Data drives: 2x4TB WD Red Plus (CMR)
- Also: a 2.5 gigabit NIC which bridges the built-in NIC and allows me to have higher speed connectivity between the server and my PC.

![Interior of the HP Z240 SFF](/assets/images/2025-02-27-home-server-hardware/interior.jpg)

I find these specs perfectly fine for my use case - Ubuntu Server with ZFS and around 25 Docker containers.

Some notes on the initial setup:

- I used the [HP Image Assistant](https://ftp.ext.hp.com/pub/caps-softpaq/cmit/HPIA.html) to update the BIOS and AMT firmware easily.
- Intel AMT can be reset within the BIOS, and then configured from the boot menu.
- Use the [MeshCommander firmware installer](https://www.meshcommander.com/meshcommander/firmware) to install MeshCommander onto Intel AMT, which providers a more powerful interface. TLS can also be configured using MeshCommander.

Overall, I'm planning on sticking with this machine for a while longer (I've used variations of this machine since 2021). The next logical upgrade is the HP Z2 G4 SFF which is very similar but supports Coffee Lake Refresh CPUs.
