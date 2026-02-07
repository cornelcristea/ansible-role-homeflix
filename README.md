# Ansible Role: HomeFlix

## Table of Contents

- [Description](#description)
- [Requirements](#requirements)
- [Variables](#variables)
- [Notes](#notes)
- [Playbook example](#playbook-example)
- [Known issues](#known-issues)
- [How to contribute](#how-to-contribute)

## Requirements

- Docker
- Traefik (optional)

The following role is recommened to be used for Traefik:<br>
https://galaxy.ansible.com/ui/standalone/roles/cornelcristea/traefik/

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

## Variables

The role use the following variables

| Name | Description | Default |
|------|-------------|---------|
| `homeflix_timezone` | Timezone used by all Docker containers. | `Europe/Bucharest` |
| `homeflix_qbittorrent_password` | Default qBittorrent admin password | `adminadmin` |
| `homeflix_bazarr_api_key` | Bazarr API key used for automation. | `463482c2b8172db0ba2736f6a0b3dbc7` |
| `homeflix_jellyfin_api_key` | Jellyfin API key used for automation. | `c62af39cf6634c6ca52c92b02f618b84` |
| `homeflix_jellyfin_host_domain` | Public domain used by Traefik to expose Jellyfin over HTTPS. | *(empty)* |
| `homeflix_jellyfin_users` | List of Jellyfin users to add. | `homeflix` user and `pass123` password |
| `homeflix_jellyfin_plugins` | List of Jellyfin plugins to install. | *(empty)* |
| `homeflix_jellyfin_custom_css` | Custom CSS injected into Jellyfin UI. | *(empty)* |
| `homeflix_jellyfin_server_name` | Jellyfin server display name. | `HomeFlix` |
| `homeflix_jellyfin_splash_screen_enabled` | Enable or disable Jellyfin splash screen. | `false` |
| `homeflix_jellyfin_libraries_extra` | Additional Jellyfin libraries beyond defaults. | *(empty)* |
| `homeflix_prowlarr_indexers_extra` | Extra Prowlarr indexers appended to defaults. | *(empty)* |
| `homeflix_data_folders_extra` | Estra media folders. | *(empty)* |

## Notes

- Make sure **ports are available** before deployment.  
- **Traefik** reverse proxy network is optional. The `jellyfin_host_domain` will work only if `traefik_network` variable exists.
- Each service has a default user `admin` with password `admin` (for qBittorent, the password is `adminadmin`). 
- Jellyfin and Bazarr have a default API key used for automation store in variable.
- **After first deployment, change the password for default user, generate new API keys and save them as encrypted in playbook**
- **No VPN is used for downloads - use it on your own risks**

## Playbook example

```yaml
- name: Deploy HomeFlix
  hosts: servers
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
| `update_homeflix_dirs` | Update apps and data directories |
| `update_bazarr` | Update Bazarr service |
| `update_jellyfin` | Update Jellyfin service |
| `update_jellyfin_branding` | Update Jellyfin branding (custom CSS, splash screen) |
| `update_jellyfin_libraries` | Add or delete Jellyfin libraries |
| `update_jellyfin_plugins` | Download and install Jellyfin plugins |
| `update_jellyfin_server_name` | Update the Jellyfin server name in its configuration |
| `update_jellyfin_users` | Add or delete Jellyfin user accounts |
| `update_prowlarr` | Update Prowlarr service |
| `update_prowlarr_indexers` | Add or delete Prowlarr indexers |
| `update_qbittorrent` | Update qBittorrent service |
| `update_radarr` | Update Radarr service |
| `update_recyclarr` | Update Recyclarr service |
| `update_seerr` | Update Seer service |
| `update_sonarr` | Update Sonarr service |

## Known issues
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

**We appreciate your contributions!**
