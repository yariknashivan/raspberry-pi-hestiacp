# raspberry-pi-hestiacp

Download Ubuntu 20.04 image from
https://ubuntu.com/download/raspberry-pi (arm64)

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


Go to http://192.168.0.1/ and check your Raspberry Pi ip address (DHCP Client List). It may take up to 5 minutes.


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

Install a HestiaCP

https://docs.hestiacp.com/development/panel.html


```
cd /tmp
git clone https://github.com/hestiacp/hestiacp.git
cd hestiacp/src
./hst_autocompile.sh --all --noinstall --keepbuild '~localsrc'
cd ../install
bash hst-install-ubuntu.sh --with-debs /tmp/hestiacp-src/deb/
```




Remove swap file:
```
sudo swapoff -v /swapfile
sudo nano /etc/fstab
sudo rm /swapfile
```
