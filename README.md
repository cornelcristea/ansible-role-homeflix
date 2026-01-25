# Ansible Role: Homeflix

## Table of Contents

- [Description](#description)
- [Requirements](#requirements)
- [Variables](#variables)
- [Notes](#notes)
- [Example Playbook](#example-playbook)
- [Knowing issues](#knowing-issues)
- [How to Contribute](#how-to-contribute)

## Description

This Ansible role deploys a full **media server stack** as docker containers:

- **Bazarr:** Subtitle manager
- **Flaresolverr:** Cloudflare bypass tool
- **Jellyfin:** Media streaming
- **Prowlarr:** Indexers manager
- **qBittorrent:** Download client
- **Radarr:** Movies manager
- **Recyclarr:** Syncs TRaSH-Guides settings to Sonarr/Radarr
- **Seer:** Media requester
- **Sonarr:** TV shows manager 

The working flow of these services is described in the following diagram.

```
Seer (Requester)
      |
      v
Prowlarr (Indexers) --> Flaresolverr (Cloudflare bypass)
      |
      +----------------+
      |                |
      v                v
  Radarr (Movies)   Sonarr (TV Shows)
      |                |
      +------> Recyclarr (TRaSH Rules)
      |                |
      +------> Bazarr (Subtitles)
      |                |
      +------> qBittorrent (Download)
                        |
                        v
                  Jellyfin (Streaming)
```

## Requirements

- Linux OS with Debian distro (e.g Ubuntu)
- Docker & Docker Compose
- Ansible ≥ 2.14
- Traefik (optional for Reverse Proxy)


## Variables

| Name | Description | Default |
|----------|------------|---------|
| `homeflix_timezone` | Server timezone | `Europe/Bucharest` |
| `homeflix_jellyfin_domain` | Jellyfin domain (for reverse proxy) | `localhost` |
| `homeflix_jellyfin_server_name` | Name displayed in Jellyfin UI | `HomeFlix` |
| `homeflix_jellyfin_users` | List of users with passwords | See defaults |
| `homeflix_jellyfin_libraries` | Media libraries | Movies & TV Shows |
| `homeflix_prowlarr_indexers` | List of preconfigured indexers | 1337x, EZTV, LimeTorrents, The Pirate Bay, showRSS |
| `homeflix_qbittorrent_password` | Default qBittorrent password | See defaults |
| `homeflix_bazarr_api_key` | Default Bazarr API key | See defaults |
| `homeflix_jellyfin_api_key` | Default Jellyfin API key | See defaults |


## Notes

- Traefik reverse proxy network is optional — enable by uncommenting `homeflix_traefik_network` variable.  
- Each service has default `admin` user and its password is `admin` (for qBittorent, the password is `adminadmin`). **After first deploy, the passowrds have to be changed.**
- Jellyfin and Bazarr have a default API key that needs to be generated after first deploy and save them as variables (check `default.yml`). Do same thing with qBittorrent password.
- Always use **encrypted passwords** in host_vars for sensitive services.  
- Make sure **ports are available** and **media folders exist** before deployment.  
- <b>After deployment, update API keys and passwords to secure values.</b>


## Example Playbook

```yaml
- hosts: servers
  vars:
    homeflix_timezone: UTC
    homeflix_jellyfin_server_name: HomeFlix
  roles:
    - homeflix
```

## Tags

When running the playbook to deploy the Homeflix services, specific tags can be used to perform targeted actions.

| Name | Description |
|-----|-------------|
| `update_docker_compose` | Update docker-compose file and redeploy services |
| `update_bazarr` | Update Bazarr service |
| `update_jellyfin` | Update Jellyfin service |
| `update_jellyfin_libraries` | Update Jellyfin media libraries (scan for new/removed media) |
| `update_jellyfin_plugins` | Download and install Jellyfin plugins |
| `update_jellyfin_server_name` | Update the Jellyfin server name in its configuration |
| `update_jellyfin_users` | Update Jellyfin user accounts |
| `update_prowlarr` | Update Prowlarr service |
| `update_prowlarr_indexers` | Update Prowlarr indexers (RSS/automation sources) |
| `update_qbittorrent` | Update qBittorrent service |
| `update_radarr` | Update Radarr service |
| `update_recyclarr` | Update Recyclarr service |
| `update_seerr` | Update Seer service |
| `update_sonarr` | Update Sonarr service |


## Knowing issues

- For Radarr, the `Rename movies` option cannot be enabled via API and it has to be enabled manually from Web UI
- The `Root folder` for Radarr and Sonarr has to be set manually from Web UI
- For Bazarr, the `Language profile` has to be set manually
- Seerr cannot be configured using API.
- <b>The role does not contain any VPN</b>


## How to Contribute

We welcome contributions! Here’s how you can help improve this role:

**1. Fork the repository**    
  Click the `Fork` button at the top of the repository.

**2. Clone your fork locally**

```bash
git clone https://github.com/cornelcristea/ansible-homeflix.git
cd ansible-homeflix
```

**3. Create a new branch**

  ```bash
  git checkout -b my-feature-branch
  ```

  * Make your changes following Ansible best practices.
  * Add or update tests if applicable.
  * Update the README for any new variables or features.
  * Test your changes:
```bash
ansible-playbook test.yml -i inventory
```

**4. Commit and push your changes**

```bash
git add .
git commit -m "Add feature XYZ"
git push origin my-feature-branch
```

**5. Open a Pull Request (PR)**
 
Submit a PR from your branch. Include a clear description of your changes and why they’re needed.

### We appreciate your contributions!

