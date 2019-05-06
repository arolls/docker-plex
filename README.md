# docker-plex
Files to run Plex Media Server with Docker and autostart with systemd

NOTE: In my experience it can take a few *minutes* for Plex to start on boot. You can monitor the progress using the command: docker ps -a

# Operating System
This guide refers to Ubuntu 18.04 but with some minor modifications you should be able to get this to run under RHEL/CentOS. The information is available elsewhere but I wanted to group it in to one place for new users.

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

# Setting autostart using systemd
- Place 'plex.service' from this repo in to:
```
sudo cp plex.service /etc/systemd/system/
```
Notice that this file references the install location so if you have a custom install location you will need to modify this file also.

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
    environment:
      - TZ=GB
    network_mode: host
    volumes:
      - /opt/docker-plex/config:/config
      - /opt/docker-plex/transcode:/transcode
      - /home/user/media:/data
```
