# murphywants-ansible-role-Freshrss

Purpose: Setup the freshrss docker container using rootless podman on its own partition

```
Image: 'lscr.io/linuxserver/freshrss'
tag: latest # unless otherwise specificed in the variables
```

This role will:
- Create a new ZFS volume for freshrss
- Prestage all the files and folders required for operations
  - Folder structure for volume, backups and scripts
  - Deploy the docker-compose file
- Install nginx and implement the correct reverse proxy
  - TODO - Deploying certs
- Deploy a database dump script to run daily at 10PM
- Start docker via docker compose

## Requirements/Dependencies
Uses the following modules:
- community.general
  - community.general.zfs
  - community.general.ufw
- community.docker.docker_compose

Uses the following roles:
- [murphywants-ansible-role-component-docker](https://github.com/MurphyWants/murphywants-ansible-role-component-docker): To install and configure the docker baseline

## Tags
Ansible role template with the following actions & tags:

Tag | Description
--- | ---
Setup/Baseline | Setup the application and apply the configuration baseline
Start | Start required services
Stop | Stop required services
Backup | Run the backup for the component or application or OS
Update | Update the component applications
Remove | Remove configurations and applications
Purge | Remove all configurations and applications relating to the app/component

## Variables
Variable | Default Value | Description
---|---|---
FRESHRSS_PUID | 10001 | https://docs.linuxserver.io/general/understanding-puid-and-pgid
FRESHRSS_GUID | 10001 | https://docs.linuxserver.io/general/understanding-puid-and-pgid
FRESHRSS_HTTP_PORT | 8080 | Host port for port 80 port mapping
FRESHRSS_HTTPS_PORT | 8081 | Host port for port 443 port mapping
FRESHRSS_PATH | "/mnt/freshrss/" | The location of the container persistent data. A parition will be created and mounted here if the folder doens't exist. 
FRESHRSS_STORAGE_ZFS_POOL | "apps_pool" | The Linux hosts ZFS pool name, where the partiiton will be created from
FRESHRSS_STORAGE_ZFS_FS | "freshrss" | The name of the filesystem that ZFS is creating
FRESHRSS_PODMAN_SERVICE_ACCOUNT | "srv_freshrss" | Placeholder service account, todo later
FRESHRSS_CONTAINER_VERSION | "latest" | Can be used to specify a specific version of the docker container to pull and use
FRESHRSS_FQDN | 'freshrss.domain.com' | Change this to your fully qualified domain name
FRESHRSS_TIMEZONE | "America/New_York" | Timezone of the container
FRESHRSS_SSL_CERT_PATH | <unset> | Requried for nginx config
FRESHRSS_SSL_KEY_PATH | <unset> | Requried for nginx config

## Example Playbook

install_freshrss.yml
```
- hosts: app_freshrss # Assuming the 'app_freshrss' group exists
  gather_facts: yes
  become: yes
  roles:
  - role: murphywants-ansible-role-Freshrss
```

## Example Inventory

static.ini
```
[all]
rhel-freshrss.domain.example.com

[app_freshrss]
rhel-freshrss.domain.example.com

[app_freshrss:vars]
FRESHRSS_URL="freshrss.domain.example.com"
```

# TODO List
- Setup borg backups
- TOOD breakout nginx & certs into their own component role
- Setup FS snapshots and offloading
