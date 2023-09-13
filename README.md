### [View project on github](https://github.com/rusac/rpi)  

# Basic setup

### OS
Download image for Raspbian, ubuntu, etc.  
Write image to SD card to be used in Pi

### SSH

Copy an empty "ssh" file to main OS directory on SD card  

Then follow this guide to set up public key authentication: https://www.raspberrypi.com/documentation/computers/remote-access.html#ssh  

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

# Add the repository to Apt sources:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update  
```
2. Install the Docker packages #(incl. engine, cli, and compose).  
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin  
```
3. Verify that the Docker Engine installation is successful  
```
sudo docker run hello-world  
```

# Jellyfin  

1. Create directories for media to be mounted to  
```
mkdir /mnt/Audio  
mkdir /mnt/Movies  
mkdir /mnt/TV
mkdir /home/user/dockercontainerinfo/config
mkdir /home/user/dockercontainerinfo/cache  
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
4. Use Docker Compose to set up and mount volumes  

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




https://stacktracing.com/jellyfin-with-docker-compose/  
https://fleetstack.io/blog/mastering-jellyfin-on-raspberry-pi-2023-guide
https://jellyfin.org/docs/general/installation/container/  



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
cat /etc/os-release  
df -Th  
lsblk  





# piHole

# RetroPi

# rsync
