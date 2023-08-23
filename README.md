# murphywants-ansible-role-Freshrss

Purpose: Setup the freshrss docker container using rootless podman on its own partition

```
Image: 'lscr.io/linuxserver/freshrss'
tag: latest # unless otherwise specificed in the variables
```

This role will:
- Create a new ZFS volume for freshrss
  - TODO - Do the same using LVM and XFS
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
- ansible.builtin
- ansible.posix
- community.docker.docker_compose

## Variables
Variable | Default Value | Description
---|---|---
FRESHRSS_PUID | 10001 | https://docs.linuxserver.io/general/understanding-puid-and-pgid
FRESHRSS_GUID | 10001 | https://docs.linuxserver.io/general/understanding-puid-and-pgid
FRESHRSS_HTTP_PORT | 8080 | Host port for port 80 port mapping
FRESHRSS_HTTPS_PORT | 8081 | Host port for port 443 port mapping
FRESHRSS_PATH | "/mnt/freshrss/" | The location of the container persistent data. A parition will be created and mounted here if the folder doens't exist. 
FRESHRSS_STORAGE_LVM_ZFS | "ZFS" | Placeholder if I also implement LVM support
FRESHRSS_STORAGE_ZFS_POOL | "apps_pool" | The Linux hosts ZFS pool name, where the partiiton will be created from
FRESHRSS_STORAGE_ZFS_FS | "freshrss" | The name of the filesystem that ZFS is creating
FRESHRSS_STORAGE_ZFS_QUOTA | 0 | Placeholder, if I ever want to add quotas
FRESHRSS_BACKEND | "DOCKER" | Placeholder, if I ever want to add podman support
FRESHRSS_PODMAN_SERVICE_ACCOUNT | "srv_freshrss" | Placeholder service account, todo later
FRESHRSS_CONTAINER_VERSION | "latest" | Can be used to specify a specific version of the docker container to pull and use
FRESHRSS_FQDN | 'freshrss.domain.com' | Change this to your fully qualified domain name
FRESHRSS_BACKUPS_BORG_CONFIGURE | "True" | Placeholder, todo deploy borg backups of this application stack
FRESHRSS_BACKUPS_FS_SNAPSHOTS_CONFIGURE | "True" | Placeholder, todo zfs snapshots and/or lvm snapshots

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

# Modules Required
- ansible-galaxy collection install community.general
  - community.general.zfs
  - community.general.ufw
- ansible-galaxy collection install community.docker
  - community.docker.docker_compose

