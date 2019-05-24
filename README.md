# docker-plex
Files to run Plex Media Server with Docker and autostart under Ubuntu 18.04

# Prerequisties
Install docker: https://docs.docker.com/install/linux/docker-ce/ubuntu/

Install UFW (Ubuntu Firewall)
```
sudo apt-get install -y ufw
```

# Install Directory & Files
For this guide I used the install location:
```
/opt/docker-plex/
```
But you can use any directory you wish.

# Installation
- Create empty directories for transcode and config and place the docker-compose.yml here also:
```
sudo mkdir /opt/docker-plex /opt/docker-plex/transcode /opt/docker-plex/config
```
- Move the docker-compose.yml from this repo in to the directory /opt/docker-plex
```
sudo cp docker-compose.yml /opt/docker-plex
```

# Firewall Rules
- Place the file 'plexmediaserver' in:
```
/etc/ufw/applications.d
```
- Enable the rules with:
```
sudo ufw app update plexmediaserver && ufw allow plexmediaserver-all
```

# Data Volumes
Notice in docker-compose.yaml I've mounted /mnt in the servers local file system to /data inside the Plex container. You must do this so that the Docker container can see your media.

This is because Plex inside the container is looking for media in a directory called /data inside the container.  So you need to tell docker to mount your local media directory in to the Plex container at this location.

You must modify the docker compose file to ensure that you are mounting the correct path to your media in to the container. If your server has media in the directory:
```
/home/user/media/
```
For example then you should modify the docker-compose.yaml to mount that directory:
```
version: '2'
services:
  plex:
    container_name: plex
    image: plexinc/pms-docker:latest
    restart: always
    environment:
      - TZ=GB
    network_mode: host
    volumes:
      - /opt/docker-plex/config:/config
      - /opt/docker-plex/transcode:/transcode
      - /home/user/media:/data
```

# Autostart with systemd
I have included a file plex.service if you wish to use systemd to start your plex container on boot. I prefer to use the built in Docker restart policy "always" as using systemd seem to introduce a wait time for the Plex container to start when the system booted.
