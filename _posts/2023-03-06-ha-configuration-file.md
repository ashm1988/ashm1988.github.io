---
layout: post
title: HA Initial Setup
date: 2023-03-06 13:13 +1100
categories: [Home Assistant]
tags: [configuration, setup, hacs]
---

# Initial Setup steps 
## Install Visual Stuido Code
Install Visual Stuido Code do allow changing the ```configuration.yml``` file.  
Hit the [link](https://my.home-assistant.io/redirect/supervisor_addon/?addon=a0d7b954_vscode) to insall. 

## Enable connction via traefik/https
Add a ```file``` provider to the ```traefik.yml```.  

```yml
providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: true
    network: proxy
  file:
    filename: /dynamic.yml
    watch: true
```
Add the dynamic config file ```dynamic.yml```  
> Remember to add the ```dynamic.yml``` path in the docker-compose file.  

> Remember to add the dns name to dns server (pi-hole).  

```yml
---
tls:
  options:
    default:
      minVersion: VersionTLS13
      sniStrict: true

http:
  middlewares:
    secHeaders:
      headers:
        browserXssFilter: true
        contentTypeNosniff: true
        frameDeny: true
        sslRedirect: true
        stsIncludeSubdomains: true
        stsPreload: true
        stsSeconds: 31536000
        customFrameOptionsValue: SAMEORIGIN
    https-redirect:
      redirectScheme:
        scheme: https
  routers:
    home-assistant:
      service: home-assistant
      rule: "Host(`ha.local.ashmcfarlane.com`)"
      entryPoints:
        - https
      tls:
        certResolver: http
  services:
    home-assistant:
      loadBalancer:
        servers:
          - url: http://192.168.1.186:8123      
```

Update the configuration in Home Assistant to accept the connection.  

```yml
http:
  ip_ban_enabled: false
  login_attempts_threshold: 5
  use_x_forwarded_for: true
  trusted_proxies:
    - 192.168.1.0/24
    - 172.18.0.0/24
    - 127.0.0.1
    - ::1
    - fe80::/64
    - fe00::/64
    - fd00::/64
```
## Install HACS
HACS is the community store for Home Assistant. <https://www.youtube.com/watch?v=Q8Gj0LiklRE>  
- First enable advanced mode in the user profile. 
- Install Terminal/SSH access in the add-ons 
- Install HACS via the terminal
```shell
wget -O - https://get.hacs.xyz | bash -
```
- Restart HA
- Add HACS as an intergration (Settings -> Device & services)
- 