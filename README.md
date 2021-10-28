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

Now you need to make port forwarding:  
![Forwarding](https://i.ibb.co/D9Xt5G8/image.png)  
8083 - HestiaCP  
80 - website (default port)  
22 - ssh  
If you have an error: The port of the remote web management is conflicting with of the virtual server.  
You need to change a port for Remote Management (from 80 to 81):  
![Remote Management](https://i.ibb.co/PDyDdTt/image.png)

------------------------------------------------------------------------

## If you have a domain:

### Namecheap Configuration

1. Click the Manage button next to the domain in the Domain List view
2. In the Domain tab, scroll down and remove any entries in the Redirect Domain list
3. In the Advanced DNS tab...
4. Turn on Dynamic DNS and make a note of the password
5. Add an A Record for @ pointing to your ip (ex.: **31.133.92.161**)


ddclient Configuration:

Install ddclient (fill in dummy values for the TUI):
```
sudo apt-get install ddclient libio-socket-ssl-perl
```

Edit ```/etc/ddclient.conf``` to look like the following:  
(You can find a password at Domains > Details > Dynamic DNS tab > Dynamic DNS)
```
ssl=yes
use=web, web=dynamicdns.park-your-domain.com/getip
protocol=namecheap
server=dynamicdns.park-your-domain.com
login=YOURDOMAIN.COM
password='***'
@
```

You can test:
```
sudo ddclient -query
sudo ddclient -debug -verbose -noquiet
```

Restart:
```
sudo service ddclient restart
```
Add a domain in a HestiaCP and you will see something like this:
![nashivan](https://i.ibb.co/jrpVYdR/image.png)

## How to make a subdomain:

Add a DNS record in NameCheap (ex.: test.nashivan.com)
```
A+  test  31.133.92.161 TTL auto
```

Go to HestiaCP and create a subdomain. Add a DNS record to a zone.  
Done!

## If you don't have a domain:

Create an account at https://noip.com  
Create a hostname (example: nashivan.ddns.com)  
Modify a hostname - change IPv4 Address (example: 31.133.92.161). 

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
