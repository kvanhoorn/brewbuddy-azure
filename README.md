# brewbuddy-azure
Create a BrewBuddy (BrouwHulp) Azure Debian VM with VNC

## Infra
* Deploy Debian 10 VM with 2vcpu, 16GiB MEM and a Premium SSD of 64GB
* Enable port 5901 [Network] from any to any with TCP

## Setup basic
* SSH with configured key to VM

```
sudo -i
apt-get update
apt-get upgrade
apt install aptitude tasksel
aptitude install ~t^desktop$ ~t^kde-desktop$
```
