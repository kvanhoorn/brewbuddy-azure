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
