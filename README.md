# Ansible Role: Homeflix


## Description

This Ansible role deploys a full **HomeFlix media server stack** with Docker containers:

- **Jellyfin:** Media server  
- **Radarr:** Movies manager  
- **Sonarr:** TV shows manager  
- **Prowlarr:** Indexers manager  
- **Recyclarr:** Syncs TRaSH-Guides settings to Sonarr/Radarr  
- **qBittorrent:** Download client  
- **Seer:** Media requester  
- **Flaresolverr:** Cloudflare bypass tool  
- **Bazarr:** Subtitle manager  


## Requirements
- Linux host
- Docker & Docker Compose
- Ansible ≥ 2.14
- Traefik (optional for Reverse Proxy)


## Variables

| Name | Description | Default | Notes |
|----------|------------|---------|-------|
| `homeflix_timezone` | Server timezone | `Europe/Bucharest` | Optional |
| `homeflix_apps_volume` | Path to store application config | `/opt/homeflix` | Automatically created |
| `homeflix_data_volume` | Path to media storage | `/mnt/homeflix` | Automatically created |
| `homeflix_data_folders` | Media subfolders | `downloads, movies, tv` | Automatically created |
| `homeflix_jellyfin_domain` | Jellyfin domain (for reverse proxy) | `localhost` | Update if using Traefik |
| `homeflix_jellyfin_server_name` | Name displayed in Jellyfin UI | `HomeFlix` | Optional |
| `homeflix_jellyfin_users` | List of users with passwords | `username: Homeflix / password: StrongPassword123!` | Encrypted passwords recommended |
| `homeflix_jellyfin_libraries` | Media libraries | Movies & TV |  |
| `homeflix_prowlarr_indexers` | List of preconfigured indexers | 1337x, EZTV, LimeTorrents, The Pirate Bay, showRSS | based on Cardigann configuration |
| `homeflix_qbittorrent_password` | Default qBittorrent password | `adminadmin` | Change after deployment |
| `homeflix_bazarr_api_key` | Default Bazarr API key | `463482c2b8172db0ba2736f6a0b3dbc7` | Generate a new key after deployment |
| `homeflix_jellyfin_api_key` | Default Jellyfin API key | `fae9789ce96847aeb834dec33c67ee8e` | Generate a new key after deployment |
| `homeflix_puid` | UID for container user | `1000` | Matches host user |
| `homeflix_pgid` | GID for container group | `1000` | Matches host group |
| `homeflix_docker_services` | List of container definitions | See defaults | Includes ports, images, and Traefik labels |
| `homeflix_jellyfin_port` | HTTP port for Jellyfin | `8096` | Optional HTTPS port: `8920` |
| `homeflix_prowlarr_port` | HTTP port for Prowlarr | `9696` | Optional |
| `homeflix_qbittorrent_port` | WebUI port for qBittorrent | `8080` | Also exposes torrent ports `6881/tcp/udp` |
| `homeflix_radarr_port` | WebUI port for Radarr | `7878` | Optional |
| `homeflix_sonarr_port` | WebUI port for Sonarr | `8989` | Optional |
| `homeflix_bazarr_port` | WebUI port for Bazarr | `6767` | Optional |
| `homeflix_seerr_port` | WebUI port for Seer | `5055` | Optional |


## Notes

- Traefik reverse proxy network is optional — enable by uncommenting `homeflix_traefik_network` variable.  
- Always use **encrypted passwords** in host_vars for sensitive services.  
- Make sure **ports are available** and **media folders exist** before deployment.  
- <b>After deployment, update API keys and passwords to secure values.</b>


## Knowing issues

- For Radarr, the `Rename movies` option cannot be enabled via API and it has to be enabled manually from Web UI
- The `Root folder` for Radarr and Sonarr has to be set manually from Web UI
- For Bazarr, the `Language profile` has to be set manually
- Seerr cannot be configured using API.
- <b>The role does not contain any VPN</b>


## Example Playbook

```yaml
- hosts: servers
  become: true
  roles:
    - homeflix
```


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

