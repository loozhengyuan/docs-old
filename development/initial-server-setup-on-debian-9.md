# Initial Server Setup on Debian 9

## Change root password

```text
passwd
```

## Change timezone

```text
dpkg-reconfigure tzdata
```

## Changing VPS sources.list to Singapore servers

```text
sed -i -e 's=http.us=ftp.sg=g' /etc/apt/sources.list
```

## Update/upgrade

```text
sudo apt update && sudo apt upgrade -y
```

## Installing basic packages

Some of these packages are often missing. You may install all of these or just select those you need:

```text
sudo apt install sudo
sudo apt install curl
sudo apt install ufw
sudo apt install unattended-upgrades
sudo apt install git
sudo apt install python3-venv
```

## Setting up UFW

```text
sudo ufw allow ssh
sudo ufw enable
```

