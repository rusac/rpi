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
cat /etc/os-release  
df -T  




# piHole

# RetroPi

# rsync
