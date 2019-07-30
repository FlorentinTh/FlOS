# Setup FlOS

The latest version of the FlOS distribution can be downloaded [here](https://github.com/FlorentinTh/FlOS/releases). Such image can then be flashed on the SD card with the [Etcher](https://www.balena.io/etcher/) tool :

![Etcher UI](img/etcher.png "Etcher UI")

**To avoid possible issues, you need to tune Etcher settings accordingly :**

![Etcher Settings](img/etcher-settings.png "Etcher Settings")

---
**Credentials :**

- username : **pi**
- password : **raspberry**

---

## 1. Hostname

- In order to change the default hostname, execute the following command :

```bash
$ sudo hostnamectl set-hostname <your_hostname>
```

- Then, edit the ```hosts``` file as follows :

```bash
$ sudo nano /etc/hosts

# Replace "raspberrypi" by your hostname at this line
127.0.1.1   raspberrypi
```

## 2. Users

### 2.1. Create New Users

- In order to create new users, you first need to switch from current user account to the root one :

```bash
$ sudo -i
```

- Create the user and add it to few groups :

```bash
adduser <new_username>

usermod <new_username> -aG sudo,users,netdev,docker
```

### 2.2. Remove the Default User

- From a sudoer user, run the following command :

```bash
$ sudo userdel -rf <username>
```

### 2.3. Edit ```root``` User Password

- Switch from current user account to the root one :

```bash
$ sudo -i
```

- Execute the following command and enter the new password :

```bash
passwd
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

- Apply your changes :

```bash
$ sudo wpa_supplicant -B -i wlan0 \
    -c /etc/wpa_supplicant/wpa_supplicant.conf

```

- Obtain an IP address :

```bash
$ sudo dhcpcd wlan0
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

- Restart the service :

```bash
 $ sudo /etc/init.d/networking restart
```

- Finally reconfigure your wireless interface if needed :
  
```bash
$ sudo wpa_cli -i wlan0 reconfigure
```

## 4. Swap

### 4.1. Increase Swap Size

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

### 4.2. Disable Swap

- To completely disable swap, execute the following commands :

```bash
$ sudo dphys-swapfile swapoff
$ sudo dphys-swapfile uninstall
$ sudo update-rc.d dphys-swapfile remove
```

## 5. Bluetooth

- To either enable, or disable the built-in Bluetooth of the Raspberry Pi, edit the following configuration file :

```bash
$ sudo nano /boot/firmware/config.txt
```

- Add the following lines to the end of the file :

```nginx
# Comment the line if you want to keep Bluetooth enabled.
dtoverlay=pi3-disable-bt
