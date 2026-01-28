# Ansible Role: HomeFlix

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
      +-----------------+
      |                 |
      v                 v
  Radarr (Movies)   Sonarr (TV Shows)
      |                 |
      +------> Recyclarr (TRaSH Rules)
      |                 |
      +------> Bazarr (Subtitles)
      |                 |
      +------> qBittorrent (Download)
                        |
                        v
                  Jellyfin (Streaming)
```

## Requirements

- Linux OS with Debian distro (e.g Ubuntu)
- Docker & Docker Compose
- Ansible ≥ 2.18
- Traefik (optional)

## Variables

The role use the following variables

| Name | Description | Default |
|------|-------------|---------|
| `homeflix_timezone` | Timezone used by all Docker containers. | `Europe/Bucharest` |
| `homeflix_qbittorrent_password` | Default qBittorrent admin password | `adminadmin` |
| `homeflix_bazarr_api_key` | Bazarr API key used for automation. | `463482c2b8172db0ba2736f6a0b3dbc7` |
| `homeflix_jellyfin_api_key` | Jellyfin API key used by Ansible for user and policy management. | `fae9789ce96847aeb834dec33c67ee8e` |
| `homeflix_jellyfin_host_domain` | Public domain used by Traefik to expose Jellyfin over HTTPS. | *(empty)* |
| `homeflix_jellyfin_users` | List of Jellyfin users to add. | `homeflix` user and `pass123` password |
| `homeflix_jellyfin_plugins` | List of Jellyfin plugins to install. | *(empty)* |
| `homeflix_jellyfin_custom_css` | Custom CSS injected into Jellyfin UI. | *(empty)* |
| `homeflix_jellyfin_server_name` | Jellyfin server display name. | `HomeFlix` |
| `homeflix_jellyfin_splash_screen_enabled` | Enable or disable Jellyfin splash screen. | `false` |
| `homeflix_jellyfin_libraries_extra` | Additional Jellyfin libraries beyond defaults. | *(empty)* |
| `homeflix_prowlarr_indexers_extra` | Extra Prowlarr indexers appended to defaults. | *(empty)* |
| `homeflix_puid` | UID used inside Docker containers. | `1000` |
| `homeflix_pgid` | GID used inside Docker containers. | `1000` |
| `homeflix_apps_volume` | Host path where application configs are stored. | `/opt/homeflix` |
| `homeflix_data_volume` | Host path where media data is stored. | `/mnt/homeflix` |
| `homeflix_data_folders` | Media folders created under data volume. | `downloads, movies, tv` |
| `homeflix_bazarr_port` | Bazarr web UI port. | `6767` |
| `homeflix_jellyfin_port` | Jellyfin HTTP port. | `8096` |
| `homeflix_jellyfin_port_https` | Jellyfin HTTPS port. | `8920` |
| `homeflix_prowlarr_port` | Prowlarr web UI port. | `9696` |
| `homeflix_qbittorrent_port` | qBittorrent web UI port. | `8080` |
| `homeflix_radarr_port` | Radarr web UI port. | `7878` |
| `homeflix_seerr_port` | Overseerr web UI port. | `5055` |
| `homeflix_sonarr_port` | Sonarr web UI port. | `8989` |
| `homeflix_docker_services` | List of Docker services deployed by the role. | bazarr, jellyfin, radarr, etc. |
| `homeflix_jellyfin_login_disclaimer` | Text shown on Jellyfin login screen. | predefined |
| `homeflix_jellyfin_libraries` | Default Jellyfin libraries. | Movies, TV Shows |
| `homeflix_prowlarr_indexers` | Default Prowlarr indexers configuration. | LimeTorrents, 1337x, etc. |


## Notes

- Traefik reverse proxy network is optional. The `jellyfin_host_domain` will work only if exist `traefik_network` variable.  
- Each service has default user `admin` and a default password `admin` (for qBittorent, the password is `adminadmin`). **After first deploy, the passowrds have to be changed.**
- Jellyfin and Bazarr have a default API key that needs to be generated after first deploy and save them as variables (check `default.yml`). Do same thing with qBittorrent password.
- Always use **encrypted passwords** in playbook for sensitive services.  
- Make sure **ports are available** before deployment.  
- <b>After deployment, update API keys and passwords to secure values.</b>
- <b>The role does not use any VPN for download. Use on your own</b>

## Playbook example

```yaml
- hosts: servers
  roles:
    - role: homeflix
      vars:
        homeflix_timezone: UTC
        homeflix_jellyfin_server_name: HomeFlix
```

## Tags

When running the playbook to deploy the Homeflix services, specific tags can be used to perform targeted actions.

| Name | Description |
|------|-------------|
| `update_docker_compose` | Update docker-compose file and redeploy services |
| `update_bazarr` | Update Bazarr service |
| `update_jellyfin` | Update Jellyfin service |
| `update_jellyfin_branding` | Update Jellyfin branding (custom CSS, splash screen, etc.) |
| `update_jellyfin_libraries` | Update Jellyfin media libraries (scan for new/removed media) |
| `update_jellyfin_plugins` | Download and install Jellyfin plugins |
| `update_jellyfin_server_name` | Update the Jellyfin server name in its configuration |
| `update_jellyfin_users` | Add or remove Jellyfin user accounts |
| `update_prowlarr` | Update Prowlarr service |
| `update_prowlarr_indexers` | Add or remove Prowlarr indexers (RSS/automation sources) |
| `update_qbittorrent` | Update qBittorrent service |
| `update_radarr` | Update Radarr service |
| `update_recyclarr` | Update Recyclarr service |
| `update_seerr` | Update Seer service |
| `update_sonarr` | Update Sonarr service |

## Known issues

- The `Rename movies` option for Radarr has to be enabled manually from Web UI
- The `Root folder` for Radarr and Sonarr has to be set manually from Web UI
- The `Language profile` For Bazarr has to be created manually from Web UI
- The `Seerr` service has to be configured manually

## How to contribute

We welcome contributions! Here’s how you can help improve this role:

**1. Fork the repository**    
  Click the `Fork` button at the top of the repository.

**2. Clone your fork locally**

```bash
git clone https://github.com/cornelcristea/ansible-role-homeflix.git
cd ansible-role-homeflix
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

