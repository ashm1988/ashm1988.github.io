---
layout: post
title: Container Notes
date: 2025-01-19 11:06 +1100
categories: [HomeLab, Containers]
tags: [homelab, truenas, Jellyfin]
---

# Containers
## Jellyfin 
### Notes
Running on Truenas 
Exposed port is 30013

### Plugins 
##### SkinManager 
- [Github](https://github.com/danieladov/jellyfin-plugin-skin-manager)
- [Repository](https://raw.githubusercontent.com/danieladov/JellyfinPluginManifest/master/manifest.json)
- Settings
{% raw %}
```xml
<?xml version="1.0" encoding="utf-8"?>                                                                                                                  <PluginConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">                                    <selectedSkin>@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/base.css');@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/accentlist.css');</selectedSkin>
  <options>
    <string>@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/fixes.css');</string>
    <string>true</string>
    <string>@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/jf_font.css');</string>
    <string>false</string>
    <string>/* Rounding */</string>
    <string>Default</string>
    <string>@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/smallercast.css');</string>
    <string>true</string>
    <string>/* Compact list */</string>
    <string>Compact List</string>
    <string>@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/header/header_transparent.css');</string>
    <string>true</string>
    <string>/* Login frame */</string>
    <string>Login Minimalistic</string>
    <string>/* Input fields */</string>
    <string>No Border</string>
    <string>/* Watched/Unwatched */</string>
    <string>Corner</string>
    <string>/* Theme */</string>
    <string>Dark</string>
    <string>/* Title page */</string>
    <string>Simple with Logo</string>
    <string>/* Progress bar */</string>
    <string>Floating</string>
    <string>@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/effects/hoverglow.css');</string>
    <string>false</string>
    <string>@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/effects/glassy.css');</string>
    <string>true</string>
    <string>@import url('https://cdn.jsdelivr.net/gh/CTalvio/Ultrachromic/effects/pan-animation.css');</string>
    <string>false</string>
    <string>@import url('https://ctalvio.github.io/Monochromic/backdrop-hack_style.css');</string>
    <string>false</string>
    <string>.backdropImage {filter: blur($px);}</string>
    <string>120</string>
    <string>.backdropImage {filter: saturate($%);}</string>
    <string>30</string>
    <string>.backdropImage {filter: contrast($%);}</string>
    <string>30</string>
    <string>.backdropImage {filter: brightness($%);}</string>
    <string>20</string>
    <string>#loginPage {background: url($) !important;}</string>
    <string />
    <string>:root {--accent: $;}</string>
    <string>#6279cd</string>
    <string>:root {--rounding: $px;}</string>        
    <string>12</string>
  </options>
</PluginConfiguration>
```
{% endraw %}


## Traefik Labels

Set of labels to add to container running on the same host as traefik to automatically add to traefik. 
```yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
    # DNS ports
      - "53:53/tcp"
      - "53:53/udp"
      - "67:67/udp"
      - 8123:80
    environment:
      TZ: 'Australia/Sydney'
    volumes:
      - /home/ash/docker/pihole/data:/etc/pihole/
      - /home/ash/docker/pihole/data/dataetc-dnsmasq.d:/etc/dnsmasq.d/
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.pihole.entrypoints=http"
      - "traefik.http.routers.pihole.rule=Host(`pihole.local.ashmcfarlane.com`)"
      - "traefik.http.services.pihole.loadbalancer.server.port=80"

      - "traefik.http.routers.pihole.middlewares=pihole-https-redirect"
      - "traefik.http.middlewares.pihole-https-redirect.redirectscheme.scheme=https"

      - "traefik.http.routers.pihole-secure.middlewares=add-admin"
      - "traefik.http.middlewares.add-admin.addprefix.prefix=/admin"

      - "traefik.http.routers.pihole-secure.entrypoints=https"
      - "traefik.http.routers.pihole-secure.rule=Host(`pihole.local.ashmcfarlane.com`)"
      - "traefik.http.routers.pihole-secure.tls=true"
      - "traefik.http.routers.pihole-secure.service=pihole"
      - "traefik.docker.network=proxy"
    networks:
      - proxy

networks:
  proxy:
    external: true
```

