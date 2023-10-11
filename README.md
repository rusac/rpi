### [View project on github](https://github.com/rusac/rpi)  

# Basic setup

### OS
Download image for Raspbian, ubuntu, etc.  
Write image to SD card to be used in Pi

### SSH

a) Copy an empty "ssh" file to main OS directory on SD card  

Then follow this guide to set up public key authentication: https://www.raspberrypi.com/documentation/computers/remote-access.html#ssh  

b) Setting up public key authentication:  
```
ssh-keygen  
ssh-copy-id <USERNAME>@<IP-ADDRESS>  #copy keygen to target device  
```
c) Edit sshd_config file:  
```
PasswordAuthentication no  
```
Change  
```
PermitRootLogin prohibit-password  
```
to  
```
PermitRootLogin no  
```
Change port to some value above 1024: ex.  
```
Port 12399
```
When done:  
```
sudo systemctl restart sshd 
```
d) To connect:  
```
ssh -p 12399 user@192.168.0.xxx  
```

### Wifi

a) create file "wpa_supplicant.conf" and copy it to main OS directory on SD card  
b) add the following:  
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev  
country=<Insert 2 letter ISO 3166-1 country code here>  
update_config=1  

network={
        ssid="networkname"
        psk="networkpassword"
}
```
Use command line to encrypt the password and use the result in place of the plain text version:  
```
$ wpa_passphrase "networkpassword"  
```
*Use quotations to help avoid issues with special characters  

c) To find the device on the network use:  
```
sudo nmap -sn 192.168.0.1/24  (or 192.168.0.0/24)
```

# Ubuntu Server

Install Ubuntu Server LTS 64bit (or 32 bit for Pi2) using guide here:  
https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi  
apt-get update  
apt-get upgrade

# Docker  

Install Docker using guide here:  https://docs.docker.com/engine/install/  
Docker Engine install using repository:  https://docs.docker.com/engine/install/ubuntu/  

1. Add docker repository
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```
2. Add the repository to Apt sources:
```
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update  
```
3. Install the Docker packages #(incl. engine, cli, and compose).  
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin  
```
4. Verify that the Docker Engine installation is successful  
```
sudo docker run hello-world  
```
5. Container management  

Basic Docker Commands
https://docs.docker.com/get-started/docker_cheatsheet.pdf  



# Jellyfin  

1. Create directories for media to be mounted to  
```
mkdir /mnt/Audio  
mkdir /mnt/Movies  
mkdir /mnt/TV
mkdir /home/user/dockercontainerinfo/config   #in user directory due to permissions  
mkdir /home/user/dockercontainerinfo/cache    # ^^^  
etc  
```
2. Mount storage devices and edit /etc/fstab file so devices are mounted on boot  
a) Determine UUID for attached storage devices  
```
blkid
```
b) Ensure mount points are ready such as /mnt/Videos, /mnt/Movies, etc  

c) Edit /etc/fstab so that devices are mounted on boot  
```
LABEL=writable  /       ext4    discard,errors=remount-ro       0 1
LABEL=system-boot       /boot/firmware  vfat    defaults        0       1
UUID=UUIDnumbergoeshere  /mnt/Videos    ext4 defaults 0 0
```
3. Download image via docker
```
docker pull jellyfin/jellyfin
```  
4. Use Docker Compose to set up and mount volumes, ie. edit the docker-compose.yml file  
```
version: '3.5'
services:
  jellyfin:
    image: jellyfin/jellyfin
    container_name: jellyfin
    user: uid:gid             # uid = user id; gid = group id; type "id" at prompt to find values
    network_mode: 'host'
    volumes:
      - /home/user/dockerdirectory/config:/config
      - /home/user/dockerdirectory/cache:/cache
      - /mnt/mediamedia:/media
      - /mnt/harddrive/videos/documentaries:/documentary
      - /mnt/audiomusic:/audio:ro
      - /mnt/othermusic:/audio2:ro
    restart: 'unless-stopped'
    # Optional - alternative address used for autodiscovery
    environment:
      - JELLYFIN_PublishedServerUrl=http://example.com
    # Optional - may be necessary for docker healthcheck to pass if running in host network mode
    extra_hosts:
      - "host.docker.internal:host-gateway"
```
5. Run jellyfin via docker:
```
docker compose up
docker compose up -d      # to run in background
docker compose down        # to shut docker container down
docker ps                # lists all containers
```
Access jellyfin via browser at 192.168.x.x:8096.  

https://stacktracing.com/jellyfin-with-docker-compose/  
https://fleetstack.io/blog/mastering-jellyfin-on-raspberry-pi-2023-guide
https://jellyfin.org/docs/general/installation/container/  

6. Set up jellyfin client on pi:

Download file from here: https://kodi.jellyfin.org/repository.jellyfin.kodi.zip  
and copy it to sd-card of client OS.  

Start Libreelec/Kodi and follow instructions here: https://jellyfin.org/docs/general/clients/kodi/  

# miniDLNA (use as alternative to jellyfin)  

1. Download image via docker
```
docker pull vladgh/minidlna
```  
2. Use Docker Compose to set up and mount volumes, ie. edit the docker-compose.yml file
```  
version: '3'
services:
  dlna:
    image: vladgh/minidlna
    container_name: minidlna
    restart: 'unless-stopped'
    # Use same settings for directories below as used for jellyfin;
    # Directories should be mounted beforehand in fstab
    volumes:
      - /mnt/movies:/media/movies
      - /mnt/audio:/media/audio
      - /mnt/video:/media/video
    network_mode: host
    environment:
      # Unsure if below is necessary
      #PUID=<your-uid>
      #PGID=<your-gid>
      MINIDLNA_MEDIA_DIR_1: /media/audio
      MINIDLNA_MEDIA_DIR_2: /media/video
      MINIDLNA_FRIENDLY_NAME: pc
      MINIDLNA_MAX_CONNECTIONS=7
      MINIDLNA_SERIAL=15161881
      MINIDLNA_MODEL_NUMBER=1
      # Default port is 8200
      MINIDLNA_PORT=8200  
```

3. Run miniDLNA via docker:
```  
docker compose up
docker compose up -d      # to run in background
docker compose down        # to shut docker container down
docker ps                # lists all containers
```
4. Access miniDLNA from client via host ip address at port 8200  

5. Error msg to figure out:
```
monitor_inotify.c:223: warn: WARNING: Inotify max_user_watches [8192] is low or close to the number of used watches [1729] and I do not have permission to increase this limit.  Please do so manually by writing a higher value into /proc/sys/fs/inotify/max_user_watches.
```

# sonarr  

Create docker compose file as with jellyfin. Enter the following and adjust:  
```
version: "3.7"

services:
  sonarr:
    container_name: sonarr
    image: ghcr.io/hotio/sonarr
    ports:
      - "8989:8989"
    environment:
      - PUID=1000
      - PGID=1000
      - UMASK=002
      - TZ=Etc/UTC
    volumes:
      - /<host_folder_config>:/config
      - /<host_folder_data>:/data
```

# piCamera
Timelapse of plant growth especially

# RPI Wireless Printer server
See these two websites with clear explanations  
-[https://pimylifeup.com/raspberry-pi-print-server/](https://pimylifeup.com/raspberry-pi-print-server/)  
-[https://www.techradar.com/how-to/computing/how-to-turn-the-raspberry-pi-into-a-wireless-printer-server-1312717](https://www.techradar.com/how-to/computing/how-to-turn-the-raspberry-pi-into-a-wireless-printer-server-1312717)

# Local networked media server
## Hard drive idle issue
## jellyfin
-[https://pimylifeup.com/raspberry-pi-jellyfin/](https://pimylifeup.com/raspberry-pi-jellyfin/)
## miniDLNA
-[https://itigic.com/install-and-configure-dlna-minidlna-server-on-linux/](https://itigic.com/install-and-configure-dlna-minidlna-server-on-linux/)  
-[https://itigic.com/install-and-configure-dlna-minidlna-server-on-linux/](https://openwrt.org/docs/guide-user/services/media_server/minidlna)


# Random useful CLI commands  

nmap -sn 192.168.0.1/24  
hostname -I  
ip addr  
cat /etc/os-release  
df -Th  
lsblk  





# piHole

# RetroPi

# rsync
