---
title: "Home Server Software - Ubuntu Server, ZFS, Docker and GitOps"
categories:
  - blog 
tags:
 - home-server
---

My [last post](/blog/home-server-hardware) outlines my home server hardware, this post will outline my current software setup, and the rationale behind my choices. I aim to keep the software setup as straightforward as possible, to reduce the maintenance overhead.

## Operating System

I chose [Ubuntu Server](https://ubuntu.com/download/server), as the LTS version has been very stable since January 2022 when I initially installed it. Version 22.04.5 LTS is currently installed, which is still supported, but I do intend to upgrade to 24.04 LTS soon. I use [unattended-upgrades](https://wiki.debian.org/UnattendedUpgrades) to keep the OS and packages up-to-date.

Online, I frequently see [Proxmox](https://www.proxmox.com/) recommended for home servers. Proxmox is great, but I believe it can introduce unnecessary complexity for a lot of home setups, mine included. Having to maintain multiple guest operating systems, can significantly increase operational complexity in terms of passing through hardware devices, managing storage configuration and general OS maintenance and configuration. My home server's primary roles are file server and Docker host, which Ubuntu Server is well suited to, without needing a virtualisation layer.

## File System

I originally started with FreeNAS (now TrueNAS) as my NAS software running on Proxmox, however, Ubuntu Server supports ZFS as a native kernel module. You lose the WebUI, but the simplicity of being able to run everything on one operating system was worth it for me. Things are slightly different now with TrueNAS Scale (Linux based instead of BSD based), but being forced to managed everything through the CLI has been a great learning experience.

I also wanted to use Root-on-ZFS on my boot drive, to take advantage of the many benefits, particularly snapshots and native encryption. I followed [this guide for Ubuntu Root-on-ZFS](https://openzfs.github.io/openzfs-docs/Getting%20Started/Ubuntu/Ubuntu%2022.04%20Root%20on%20ZFS.html), as it's not built-in to the Ubuntu Server installer.

For managing snapshots, I use a tool called [Sanoid](https://github.com/jimsalterjrs/sanoid), which is responsible for maintaining hourly, daily and monthly snapshots. I have different retention policies for my different datasets.

My two 4TB data drives are setup as a basic ZFS mirror. I try not to accumulate too much data, so this setup has been sufficient for my needs. I then use ZFS native encryption to encrypt this dataset, as well as one dataset on my boot drive. This means that if the server is ever stolen, or I sell the drives, my personal data is not accessible. I use a [Systemd service to auto mount these encrypted datasets on boot](https://withblue.ink/2020/01/19/auto-mounting-encrypted-drives-with-a-remote-key-on-linux.html). Automatic unlocking does have disadvantages, but it is convenient. To be able to decrypt the drives, the server must have an internet connection, and access to the cloud storage account where the key file is hosted.

## Docker with GitOps

Containerisation makes managing the applications on my home server straightforward. I chose to use Docker, and have a Docker Compose file that defines all my services, with pinned container versions. This Compose file is stored on GitHub, where [Renovate](https://docs.renovatebot.com/) is configured to update container images following my defined policies. Renovate auto-updates patch [semver](https://semver.org/) releases, but for major and minor releases, it opens a PR. This gives me time to check if the release introduces any breaking changes before I apply it, which I think is a better approach than using a tool like [Watchtower](https://github.com/containrrr/watchtower) which always updates you to the latest version even when there are breaking changes. On the server itself, I have a simple cronjob which pulls the Compose file and applies the updates.

My most useful applications are:

- Nginx reverse proxy
- [Nextcloud](https://nextcloud.com/install/) with [Memories](https://memories.gallery/)
- [Home Assistant](https://www.home-assistant.io/) with [Mosquitto](https://mosquitto.org/) and [Zigbee2MQTT](https://www.zigbee2mqtt.io/)
- [Frigate](https://frigate.video/)
- [Jellyfin](https://jellyfin.org/)
- [Syncthing](https://syncthing.net/)
- [Forgejo](https://forgejo.org/)
- [Miniflux](https://miniflux.app/)

## Outside Access

The majority of my services don't need to be accessed outside my home network, and when they do, I use a VPN. However, there are a few services where it is beneficial to be able to access them without the VPN (eg. Home Assistant). For this I am lucky enough to have a dynamically allocated IPv4 address from my ISP. Therefore, I am able to port forward to the Nginx reverse proxy. It is configured to only allow external access to a limited number of applications, only from certain geographic locations, using [mutual TLS](https://www.apalrd.net/posts/2024/network_mtls/) where possible and using [fail2ban](https://github.com/fail2ban/fail2ban) to ban IP addresses that trigger any rules.

## Backups

ZFS makes backups super easy. Snapshots can be used for incremental backups. I used [Syncoid](https://github.com/jimsalterjrs/sanoid?tab=readme-ov-file#syncoid) via a cronjob to backup my data to an external drive, which I rotate with an identical drive so that there is always one copy that is offline (ransomware and hardware fault protection).

I also use [Rclone](https://rclone.org/) to backup crucial data off-site.

## Monitoring

In terms of monitoring, I use [Netdata](https://www.netdata.cloud/), which comes preconfigured with variety of collectors. Netdata has built-in alarms that alert me via email when anything it monitors becomes unhealthy.

To check backup jobs are running, I use [Healthchecks.io](https://healthchecks.io/). When a backup script runs, it sends a request to Healthchecks.io and then again once it finishes. If Healthchecks.io doesn't receive this request, it emails me letting me know the job has failed.

To check the services are online I use a combination of [uptime-kuma](https://github.com/louislam/uptime-kuma) and [hyperping](https://hyperping.com/).

I also use the [system_sensors Python script](https://github.com/Sennevds/system_sensors) which reports key metrics to a Home Assistant dashboard.

## Future

I frequently tweak this setup when I have time. A few things I have planned for the future:

- Upgrade to Ubuntu 24.04
- More services! There are [so many great open source selfhosted services](https://awesome-selfhosted.net/) that I'd like to try out.
- OIDC authentication with [Pocket ID](https://github.com/pocket-id/pocket-id). This would mean less logins to manage across services.
- Infrastructure as Code. My current setup has been configured mostly manually and with some scripts. I would like to look at replicating this setup with a tool such as Ansible.

Thanks for reading :)
