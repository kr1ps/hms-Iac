# hms-Iac

Terraform and Ansible project to setup an automated Home Media Server stack running on Docker and deploy to a vcenter VM server. This project is an adaptation to my own ecosystem https://github.com/kr1ps/MediaHomeServices from a complete ansible project that you can find in this link https://github.com/ahembree/ansible-hms-docker

## Getting started

- [Container list](#container-list)
- [Other features](#other-features)
- [Supported platforms](#supported-platforms)
- [Requirements](#requirements)
- [Installation](#installation)
- [Content layout](#content-layout)
- [Configuration](#configuration)
- [Connecting the containers](#connecting-the-containers)

## Container list

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

## Other features

- Automated Docker installation
- Automatic container updates
- Dynamic DNS updates
- Wildcard SSL certificate generation
- Support for multiple network shares

## Supported platforms

- Debian based systems (Debian 9, Ubuntu 20.04+, etc.)

## Requirements

- You own a domain name and are able to modify DNS A and TXT records (if you want SSL and/or dynamic DNS)
- You have a Cloudflare account with the correct zones and API keys configured (if dynamic DNS and/or SSL is enabled)
- Slight familiarity with editing config files
- Slight familiarity with Linux (installing packages, troubleshooting, etc)
- `root` or `sudo` access
- Supported Platform
- 4 CPU Cores
- Minimum 4GB RAM
- Minimum 8GB free disk space
- Python 3.8 (Recommended, minimum Python 3.6)
- Ansible (minimum 2.9)
- If you plan to make Plex and/or Overseerr available outside your local network, the following ports must be forwarded in your router to the IP of the server that will be running these containers:
  - Instructions for forwarding ports to the correct device is outside the scope of this project as every router/gateway has different instructions.
  - This is in no way guaranteed to be the best or most secure way to do this, and this assumes your ISP does not block these ports
  - `80/tcp` (HTTP)
  - `443/tcp` (HTTPS)
  - `32400/tcp` (Plex)

---

## Please note

Setting up the individual container configurations, such as for Sonarr, Radarr, Overseerr, Prowlarr, etc. are outside the scope of this project. The purpose of this project is to ensure the necessary base containers are running.

## Content Layout

By default, the content is laid out in the following directory structure:

Generated compose file location: `/opt/kr1ps-services/docker-compose.yml`

Container data directory: `/opt/kr1ps-services/apps`

Default mount path for local share: `/mnt/kr1ps-services/`


## Installation

It is recommended to read and follow this guide entirely as there is a lot of configuration options that are required to get the system up and running to its full potential.

1. Install git and clone the repository:

   Ubuntu:

   ```bash
   # Install git if not already installed
   sudo apt-get install git -y
   ```

   ```bash
   # Clone the repository and then go into the folder
   git clone https://github.com/kr1ps/hms-iac.git
   cd ansible-hms-docker/
   ```

2. Install Ansible if not installed already:

   CentOS, Fedora, Alma, Rocky, Red Hat

   ```bash
   sudo yum install python38
   ### (If you wish to stay on Python 3.6, run `sudo yum install python3-pip` and then `pip3 install -U pip`)
   sudo pip3 install ansible
   ```

   Ubuntu, Debian

   ```bash
   sudo apt-get install python38
   ### (If you wish to stay on Python 3.6, run `sudo apt-get install python3-pip` and then `pip3 install -U pip`)
   sudo pip3 install ansible
   ```

3. Edit the `vars/default.yml` file to configure settings and variables used in the playbook.


ansible-playbook -u '{{user-name}}' -i '{{static-ip}},' ./ansible-hms/hms.yml  --key-file '~/.ssh/id_ed25519'