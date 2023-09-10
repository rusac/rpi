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

# piHole

# RetroPi

# rsync
