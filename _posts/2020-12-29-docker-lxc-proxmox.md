---
title: "Running Docker in a LXC Container on Proxmox"
header:
  image: /assets/images/2020-12-29-header.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com/photos/fBZOVyF-96w)"
  teaser: /assets/images/2020-12-29-header.jpg
categories:
  - blog
---

While it is possible to run docker on the host OS, it makes more sense to run Docker inside a virtual machine or container. However, virtual machines have a higher overhead, so I wanted to try to run Docker inside a LXC container.

Running Docker inside is not supported by default, but by simply enabling two features, Docker will happily run inside an unprivileged container.

Via command line, open the container's config file at `/etc/pve/lxc/xxx.conf` and add the line: 
```
features: keyctl=1,nesting=1
```

Or using the GUI, logged in as the root user:
![Proxmox GUI](/assets/images/2020-12-29-proxmox.jpg)

After installing Docker in the LXC container, Docker containers should be able to run without issue.