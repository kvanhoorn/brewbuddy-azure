# Brewbuddy Azure
Create a BrewBuddy (BrouwHulp) Azure Debian VM with VNC

## Infra
* Deploy Debian 10 VM with 2vcpu, 16GiB MEM and a Premium SSD of 64GB
* Enable port 5901 [Network] from any to any with TCP

## Setup X
* SSH with configured key to VM

```
sudo -i
# set timezone to Europe/Amsterdam
dpkg-reconfigure tzdata
apt-get update
apt-get upgrade
apt install aptitude tasksel
# This will take a while
aptitude install ~t^desktop$ ~t^kde-desktop$
```

## Installation (root)
```
apt-get install libfann2 libcanberra-gtk-module vnc4server
curl -LO https://github.com/BrewBuddyOrg/BrewBuddy/releases/download/5.4-ubuntu/brewbuddy-5.4_amd64.deb
dpkg -i brewbuddy-5.4_amd64.deb
```

## Installation (no root)
```
wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -
./.dropbox-dist/dropboxd &
# Visit url with your dropbox account
```

## Configuration (no root)

### vnc configuration
```
cd
mkdir .vnc
cat <<EOT >> .vnc/xstartup
!#/bin/bash
xrdb $HOME/.Xresources
startkde &
EOT
```

### vnc password
```
vncpassword
# Prompted for access password, choose no for view-only password
```

### create config folder
```
mkdir -p .config/systemd/user/
```

### create systemd loader dropbox
```
cat <<EOT >> .config/systemd/user/dropbox.service
[Unit]
Description=Dropbox as a user service
After=local-fs.target network-target

[Service]
Type=simple
ExecStart=%h/.dropbox-dist/dropboxd
Restart=on-failure
RestartSec=1

[Install]
WantedBy=default.target
EOT
```

### create systemd loader vncserver
```
cat <<EOT >> .config/systemd/user/vncserver.service
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill :1 > /dev/null 2>&1 || :'
ExecStart=/usr/bin/vncserver -httpPort 5901 -localhost no -geometry 1024x768 :1
ExecStop=/usr/bin/vncserver -kill :1

[Install]
WantedBy=default.target
EOT
```

### final configuration systemd
```
# configure and enable systemd
sudo loginctl enable-linger [username]
systemctl --user enable vncserver
systemctl --user enable dropbox
```

And reboot!

## Application
* Open VNC session to machine
* Open application from menu (Utilities)
* When prompted for 'brouwinstallatie', press cancel
* Go to settings (Instellingen) -> Databank locatie
* Browse for /home/brewer/Dropbox/BrouwHulp

## Tips
* Disable the screensaver (System Settings -> Desktop Behavior -> Screen Locking -> Disable 'Lock screen')
* Hide lower panel (Right click panel -> Panel Options -> Configure Panel -> Prefs icon -> Auto Hide)
