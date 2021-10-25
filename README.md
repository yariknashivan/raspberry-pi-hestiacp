# raspberry-pi-hestiacp

Download Ubuntu 20.04 image from https://ubuntu.com/download/raspberry-pi (arm64)

Change files to access raspberry pi via ssh:

> userdata
```
power_state:
  mode: reboot
```

> network-config
```
version: 2
ethernets:
  eth0:
    dhcp4: true
    optional: true
wifis:
  wlan0:
    dhcp4: true
    optional: true
    access-points:
      YOURWIFI:
        password: "YOURWIFIPASSWORD"
```

------------------------------------------------------------------------

Go to http://192.168.0.1/ and check your Raspberry Pi ip address (DHCP Client List).  
It may take up to 5 minutes.


```
ssh ubuntu@192.168.0.104
```
password: ubuntu

Set a password and connect again
```
sudo -s
sudo apt update && sudo apt upgrade
```

Remove admin:
```
groupdel admin
```

------------------------------------------------------------------------

Make swap file:

```
sudo fallocate -l 10G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo nano /etc/fstab
--------------------
/swapfile swap swap defaults 0 0

sudo free -h
df -h
```

------------------------------------------------------------------------

Install a HestiaCP

https://docs.hestiacp.com/development/panel.html

```
cd /tmp
git clone https://github.com/hestiacp/hestiacp.git # TO INSTALL THE LATEST VERSION (ALPHA, BETA)
git clone -b release --single-branch https://github.com/hestiacp/hestiacp.git # TO INSTALL A STABLE VERSION
cd hestiacp/src
./hst_autocompile.sh --all --noinstall --keepbuild '~localsrc'
cd ../install
bash hst-install-ubuntu.sh --with-debs /tmp/hestiacp-src/deb/
```
Welcome! You have just installed HestiaCP

------------------------------------------------------------------------

Add Hestia to PATH:
> ~/.bashrc
```
if [ "${PATH#*/usr/local/hestia/bin*}" = "$PATH" ]; then
    . /etc/profile.d/hestia.sh
fi
```
Logout and login again.

------------------------------------------------------------------------

If you want to update to a stable version:
v-update-sys-hestia-git hestiacp release

------------------------------------------------------------------------

https://192.168.0.104:8083/ - admin panel  
login: admin  
To reset password:
```
passwd admin
```

Change phpMyAdmin password:

```
nano /usr/local/hestia/conf/mysql.conf
```

------------------------------------------------------------------------

Remove swap file:
```
sudo swapoff -v /swapfile
sudo nano /etc/fstab
sudo rm /swapfile
```

------------------------------------------------------------------------

For a Raspberry Pi:
```
sudo apt install libraspberrypi-bin
sudo vcgencmd measure_temp
```

```
sudo apt install net-tools
ifconfig
```

------------------------------------------------------------------------

Create an account at https://noip.com  
Create a hostname (example: nashivan.ddns.com)  
Modify a hostname - change IPv4 Address (example: 192.168.0.102). 

Create a new user in HestiaCP and add a web domain (as your hostname)
Go to your website, you will see:
![Domain](https://i.ibb.co/PgQs3Vg/image.png)

------------------------------------------------------------------------

Deploy Django project **(in testing..)**  
Add new templates:
```
cd /usr/local/hestia/data/templates/web
git clone https://github.com/realjumy/hestiacp-python-templates.git temp
mv temp/apache2/php-fpm/* apache2/php-fpm/
chmod +x apache2/php-fpm/*.sh
mv temp/nginx/* nginx/
rm -r temp
```

Create a package, create a new user, set a package for a new user and create a new website.

------------------------------------------------------------------------
