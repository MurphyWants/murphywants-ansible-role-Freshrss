# ansible-role-template

## Plan

### Prestage
- Need to include external vaults file
- Variable to determine if lvm or zfs. Assert variable is either 'zfs' or 'lvm'
  - TODO check if one was already set in migration
- Variable to determine if podman or docker. Assert variable is either 'docker' or 'podman'
  - TODO check if one was already set in migration
- TODO test if freshrss is already installed
  - stop running cronjobs

### Partitioning - ZFS
- if zfs
  - Variable for ZFS pool. Assert pool exists.
  - Make ZFS filesystem, change mountpoint to {{ FRESHRSS_VOLUME_DIR }}
    - #sudo zfs create podman-apps/freshrss
  - mkdir {{ FRESHRSS_VOLUME_DIR }}
    - #sudo zfs set mountpoint=/mnt/freshrss podman-apps/freshrss
  - TODO quota

### Partitioning - LVM
- if LVM
  - keep what we have

### Pre-reqs: Make required folders

- mkdir {{ FRESHRSS_VOLUME_DIR }}/volumes/config for freshrss apps
- mkdir {{ FRESHRSS_VOLUME_DIR }}/backups to generate backup data, database dumps etc
- mkdir {{ FRESHRSS_VOLUME_DIR }}/scripts to dump scripts into
- mkdir {{ FRESHRSS_VOLUME_DIR }}/docs to dump notes, docker compose into
- sudo mkdir -p /mnt/freshrss/{volumes/config,backups,scripts,docs}
- If selinux, flip selinux booleans

### Podman - TODO later
- Install podman
- Podman: download image

### Install and setup Docker
- Install docker
  - apt install docker docker.io docker-compose
- Copy docker compose file to /mnt/freshrss/docs/docker-compose.yml
  - Has to be named docker-compose.yml
  - #docker-compose up -d

### Install nginx and configure
- Install nginx
- Firewalls rules: open port 80 & 443
- Copy Config
- Generate Certs - if certs don't exist or expired
- Restart nginx

### Setup scripts
- TODO Dump database
  - TODO setup cron
- TODO borg backup script
  - TODO encryption key via vault
- TODO ZFS send/recieve?
  - TODO can I schedule this?



# Modules Required
- ansible-galaxy collection install community.general
  - community.general.zfs
  - community.general.ufw
- ansible-galaxy collection install community.docker
  - community.docker.docker_compose

