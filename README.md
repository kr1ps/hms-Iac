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
   cd ansible-hms-iac/
   ```

2. Install Ansible if not installed already:

   Ubuntu, Debian

   ```bash
   sudo apt-get install python38
   ### (If you wish to stay on Python 3.6, run `sudo apt-get install python3-pip` and then `pip3 install -U pip`)
   sudo pip3 install ansible
   ```
   Install Terraform if not installed already following this official steps:

    https://learn.hashicorp.com/tutorials/terraform/install-cli


3. Edit the `vars/default.yml` and file to configure settings and variables used in the playbook.

4. copy `ansible-hms/roles/kr1ps-services/defaults/example-main.yml` to the same directory with the name main.yml and setting those variables as the examples.

5. copy `example-terraform.tfvars` to the same directory with the name terraform.tfvars and set the information for your vcenter environment as the examples.



## Configuration

- Settings to configure:

  - `plex_claim_token` : (optional) your Plex claim code from https://plex.tv/claim
  - `hms_docker_domain` : the local domain name of the server to be used for proxy rules and SSL certificates (e.g. `home.local`)
  - `transmission_vpn_user` : the username of the VPN user
  - `transmission_vpn_pass` : the password of the VPN user
  - `transmission_vpn_provider` : the VPN provider (e.g. `nordvpn`, [see this page for the list of supported providers](https://haugene.github.io/docker-transmission-openvpn/supported-providers/#internal_providers))
  - `hms_docker_media_share_type` : the type of network share (`cifs`, `nfs`, `local`)

- Required settings for wildcard SSL certificate generation:

  - A supported DNS provider (e.g. Cloudflare), [you can find supported providers here along with their settings](https://doc.traefik.io/traefik/https/acme/#providers)
  - `traefik_ssl_enabled` : whether or not to generate a wildcard SSL certificate
  - `traefik_ssl_dns_provider_zone` : the zone of the DNS provider (e.g. `example.com`, this will default to the `hms_docker_domain` if not modified)
  - `traefik_ssl_dns_provider_code` : the code of the DNS provider (e.g. `cloudflare`, found at link above)
  - `traefik_ssl_dns_provider_environment_vars` : the environment variables, along with their values, of the DNS provider you're using (e.g. `"CF_DNS_API_TOKEN": "<token>"` if using `cloudflare`, found at link above)
  - `traefik_ssl_letsencrypt_email` : the email address to use for Let's Encrypt
  - `traefik_ssl_use_letsencrypt_staging_url` : whether or not to use the Let's Encrypt staging URL for initial testing (`yes` or `no`) (default: `yes`)
    - The certificate will say it is invalid, but if you check the issuer, it should come from the "Staging" server, meaning it worked successfully and you then change this value to `no` to use the production server and get a valid certificate.

- Required settings for the `hms_docker_media_share_type` of `cifs`:

  - `nas_client_remote_cifs_path` : the path to the network share (e.g. `//nas.example.com/share`)
  - `nas_client_cifs_username` : the username of the network share
  - `nas_client_cifs_password` : the password of the network share
  - `nas_client_cifs_opts` : the options for the network share (Google can help you find the correct options)

- Required settings for the `hms_docker_media_share_type` of `nfs`:

  - `nas_client_remote_nfs_path` : the path to the network share (e.g. `nas.example.com:/share`)
  - `nas_client_nfs_opts` : the options for the network share (Google can help you find the correct options)

- Required settings for using Cloudflare DDNS:

  - A Cloudflare account and Cloudflare configured as your domains DNS servers
  - `cloudflare_ddns_enabled` : `yes` or `no` to enable/disable Cloudflare DDNS (default: `no`)
  - `cloudflare_api_token` : the API token of the Cloudflare account
  - `cloudflare_zone` : the domain name of the Cloudflare zone (e.g. `example.com`)
  - `cloudflare_ddns_subdomain` : the subdomain record (e.g. `overseerr` would be created as `overseerr.example.com`) (default: `overseerr`)
  - `cloudflare_ddns_proxied` : `'true'` or `'false'` to enable/disable proxying the traffic through Cloudflare (default: `'true'`)

- Optional settings to configure:
  - If you with to use a more advanced configuration, you can run this command to replace the standard config with the default advanced config:

  ```bash
  cp roles/hmsdocker/defaults/main.yml vars/default.yml
  ```

---

### Running the infraestructure

```bash
# perfom a terraform init for get the folder rdy with all the requirement
terraform init

# perfom a terraform apply for deploy the infraestructure to a vcenter environment.
terraform apply

The last 2 command will deploy the base server on a new vm in the vcenter environment with the ip "172.18.0.24". you can change this ip in this file "ansible-hms/00-installer-config.yaml".

# To run it against a remote host, run the command:

ansible-playbook -u 'krips' -i '172.18.0.24,' ./ansible-hms/hms.yml  --key-file '~/.ssh/id_ed25519'

```

Once the playbook has finished running, it may take up to a few minutes for the SSL certificate to be generated (if enabled).

If you do not already have a "wildcard" DNS record setup for the domain you used on your LOCAL DNS server (such as `*.home.local`), create this record to point to the IP address of the server. If you enabled Cloudflare DDNS, an "overseerr" public A record will be created.

You can also create individual A records for each container listed in the table below.

If the above DNS requirements are met, you can then access the containers by using the following URLs (substituting `{{ domain }}` for the domain you used):

Plex: `https://plex.{{ domain }}`

Sonarr: `https://sonarr.{{ domain }}`

Radarr: `https://radarr.{{ domain }}`

Bazarr: `https://bazarr.{{ domain }}`

Overseerr: `https://overseerr.{{ domain }}`

Prowlarr: `https://prowlarr.{{ domain }}`

deluge: `https://deluge.{{ domain }}`

Tautulli: `https://tautulli.{{ domain }}`

Traefik: `https://traefik.{{ domain }}`





