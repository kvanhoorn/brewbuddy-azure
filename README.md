# brewbuddy-azure
Create a BrewBuddy (BrouwHulp) Azure Debian VM with VNC

## Infra
* Deploy Debian 10 VM with 2vcpu, 16GiB MEM and a Premium SSD of 64GB
* Enable port 5901 [Network] from any to any with TCP

## Setup X
* SSH with configured key to VM

```
sudo -i
apt-get update
apt-get upgrade
apt install aptitude tasksel
# This will take a while
aptitude install ~t^desktop$ ~t^kde-desktop$
```

## Installation
```
apt-get install libfann2 libcanberra-gtk-module vnc4server
curl -LO https://github.com/BrewBuddyOrg/BrewBuddy/releases/download/5.4-ubuntu/brewbuddy-5.4_amd64.deb
dpkg -i brewbuddy-5.4_amd64.deb
wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -
./.dropbox-dist/dropboxd &
# Visit url with your dropbox account
```

## Configuration
```
cd
mkdir .vnc
cat <<EOT >> .vnc/xstartup
!#/bin/bash
xrdb $HOME/.Xresources
startkde &
EOT
```

## Running
```
vncserver -httpPort 5901 -localhost no -geometry 1024x768
# Prompted for access password, choose no for view-only password
```

## Application
* Open application from menu (Utilities)
* When prompted for 'brouwinstallatie', press cancel
* Go to settings (Instellingen) -> Databank locatie
* Browse for /root/Dropbox/BrouwHulp

## Finetuning
```
cat <<EOT >> /etc/init.d/vncserver
#!/bin/bash
#/etc/init.d/vncserver
#

case "$1" in
  start)
    echo "Starting VNC"
    vncserver -httpPort 5901 -localhost no -geometry 1024x768
    ;;
  stop)
    echo "Stopping VNC"
    vncserver -kill :1
    ;;
  *)
    echo "Usage: /etc/init.d/vncserver {start|stop}"
    exit 1
    ;;
esac

exit 0
EOT
chmod 755 /etc/init.d/vncserver
```
