# hms-Iac

Terraform and Ansible proyect to setup an automated Home Media Server stack running on Docker and deploy to a vcenter vm server.

## Getting Started

- [Installation](#installation)
- [Content layout](#content-layout)
- [Configuration](#configuration)
- [Connecting the containers](#connecting-the-containers)
- [Only generate config files](#only-generate-config-files)

## Container List

- Plex: media server
- Sonarr: tv series management
- Radarr: movie management
- Bazarr: subtitle management
- Prowlarr: tracker management
- Deluge: download client
- Tautulli: analytics
- Traefik: reverse proxy (with SSL support from Let's Encrypt if configured)
- Portainer: container management GUI
- Overseerr: request platform
- Watchtower: automatic container updates (if enabled)
- Cloudflare-ddns: dynamic dns (if enabled)