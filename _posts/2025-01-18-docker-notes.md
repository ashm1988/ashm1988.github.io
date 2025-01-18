---
layout: post
title: Docker notes
date: 2025-01-18 15:47 +1100
categories: [HomeLab, Docker]
tags: [homelab, docker]
---

# Docker 
## Docker Commands
### Adjust Docker ps format 
`docker ps` defaults to presenting its output in the table format shown in the previous examples. You can create your own format instead by using Go template syntax. Both standalone and table-based templates are supported, using --format 'TEMPLATE' and --format 'table TEMPLATE' syntax respectively.

Create a docker config files at `~/.docker/conifg.json`
```json
}
        "psFormat": "table \{\{.Names\}\}\\t\{\{.Ports\}\}\\t\{\{.Status\}\}\\t\{\{.ID\}\} "
}
``` 

### Upgrade Docker 
https://docs.wavemaker.com/learn/on-premise/upgrade/docker-upgrade