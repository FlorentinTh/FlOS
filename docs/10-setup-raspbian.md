# Setup Raspbian

> Since not every project require a 64 bits architecture, a documentation to properly setup the Raspbian OS is still needed.

The latest Raspbian distribution image can be downloaded [here](https://www.raspberrypi.org/downloads/raspbian/). Such image can then be flashed on the SD card with the [Etcher](https://www.balena.io/etcher/) tool :

![Etcher UI](img/etcher.png "Etcher UI")

---
**Credentials :**

- username : **pi**
- password : **raspberry**

---

## 1. Initial Configuration

- Access to the configuration panel by using :

```bash
$ sudo raspi-config
```

- Change hostname ;
- Change local & time zone ;
- Enable ssh ;

- Finally, the last required step is to apply updates :

```bash
$ sudo apt-get update && sudo apt-get dist-upgrade -y
```

## 2. Required Tools

- Install several required packages :

```bash
$ sudo apt-get install -y \
    git apache2-utils python python-dev python-pip \
    libffi-dev apt-transport-https ca-certificates \
    build-essential libssl-dev curl gnupg2 \
    software-properties-common
```

## 3. Network

### 3.1. Wi-Fi

- To enable a Wi-Fi connection, first edit the following configuration file :

```bash
$ sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```

- Set the following configuration :

```nginx
country=CA      # change accordingly to the code for your country.

network={
    ssid=""     # Insert your SSID inside quotes.
    scan_ssid=1 # Set to 1 if the SSID is hidden, 0 otherwise.
    psk=""      # Insert your password inside quotes.
}
```

- Finally, reconfigure the wireless interface with :

```bash
$ sudo wpa_cli -i wlan0 reconfigure
```

### 3.2. Static IP

- To setup a static IP address, first edit the following configuration file :

```bash
$ sudo nano /etc/dhcpcd.conf
```

- Add the following lines to the end of the file :

```nginx
interface <eth0|wlan0>
static ip_address=<your_@IP_here>/<mask>
static routers=<@IP_of_the_router>
static domain_name_servers=<@IP_of_the_router>
```

- Finally, restart the service :

```bash
 $ sudo /etc/init.d/networking restart
```

### 3.3. Disabling IPv6

- To disable IPv6 on every network interfaces, first edit the following configuration file :

```bash
$ sudo nano /etc/sysctl.conf
```

- Add the following line to the end of the file :

```nginx
net.ipv6.conf.all.disable_ipv6=1
```

- Apply changes :

```bash
$ sudo sysctl -p
```

## 4. Users

### 4.1. Create New Users

- In order to create new users, you first need to switch from current user account to the root one :

```bash
$ sudo -i
```

- Create the user and add it to few groups :

```bash
adduser <new_username>

usermod <new_username> -aG adm,dialout,cdrom,sudo,audio,\
    video,plugdev,games,users,input,netdev,spi,i2c,gpio
```

### 4.2. Remove the Default User

- From a sudoer user, run the following command :

```bash
$ sudo userdel -rf <username>
```

### 4.3. Edit ```root``` User Password

- Switch from current user account to the root one :

```bash
$ sudo -i
```

- Execute the following command and enter the new password :

```bash
passwd
```

## 5. Swap

### 5.1. Increase Swap Size

- In order to increase swap size, first edit the following configuration file :

```bash
$ sudo nano /etc/dphys-swapfile
```

- Uncomment and edit the following line as follows :

> This example illustrate a 1024 Mo swap size.

```nginx
## From :
# CONF_SWAPSIZE=100

## To :
CONF_SWAPSIZE=1024
```

- Restart the service :

```bash
$ sudo /etc/init.d/dphys-swapfile stop
$ sudo /etc/init.d/dphys-swapfile start
```

- Verify your changes :

```bash
$ free -m
```

### 5.2. Disable Swap

- To completely disable swap, execute the following commands :

```bash
$ sudo dphys-swapfile swapoff
$ sudo dphys-swapfile uninstall
$ sudo update-rc.d dphys-swapfile remove
```

## 6. Bluetooth

- To either enable, or disable the built-in Bluetooth of the Raspberry Pi, edit the following configuration file :

```bash
$ sudo nano /boot/config.txt
```

- Add the following lines to the end of the file :

```nginx
# Comment the line if you want to keep Bluetooth enabled.
dtoverlay=pi3-disable-bt
```

> Changes will be effective after reboot.

## 7. Optimization

### 7.1. Overclocking

> **Overclocking a Raspberry Pi board can cause hardware damages. Do it at your own risk.**

- To overclock the Raspberry Pi, edit the following configuration file :

```bash
$ sudo nano /boot/config.txt
```

- If you are running Raspbian without a GUI set the following configuration :

```nginx
gpu_mem=16
arm_freq=1350
over_voltage=4
sdram_freq=500
core_freq=500
disable_splash=1
```

- If you are running Raspbian with a GUI set the following configuration :

```nginx
gpu_mem=400
arm_freq=1350
over_voltage=5
sdram_freq=500
core_freq=500
gpu_freq=400
```

> Changes will be effective after reboot.

### 7.2. ZRAM Optimization

> In order to optimize _ZRAM_, you **MUST** have first disabled the swap.
> ZRAM optimization is particularly usefull when running a GUI.

- Download the script :

```bash
$ sudo curl -L https://bit.ly/2ZlWQdg \
    -o /usr/bin/zram.sh
```

- Make it executable :

```bash
$ sudo chmod +x /usr/bin/zram.sh
```

- Then, you need to make the script run automatically after boot by editing file :

```bash
$ sudo nano /etc/rc.local
```

- Insert the following command before ```exit 0``` :

```bash
/usr/bin/zram.sh &
```

## 8. Docker

### 8.1. Install Docker Engine

- To install the docker engine on Raspbian, you need to execute the installation command as follows :

```bash
$ curl -sSL get.docker.com | sh
```

- Add the current user to the docker group :

```bash
$ sudo usermod <username> -aG docker
```

- Create the ```docker```group :

```bash
$ newgrp docker
```

### 8.2. Install ```docker-compose```

- To install ```docker-compose```, you need to update possibly out of date setuptools package :

```bash
$ sudo pip install --upgrade setuptools
```

- Then, ```docker-compose``` through ```pip``` :

```bash
$ sudo pip install docker-compose
``` 