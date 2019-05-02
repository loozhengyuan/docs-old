# Initial Server Setup on Debian 9

## Change root password

```shell
passwd
```

## Change timezone

```shell
dpkg-reconfigure tzdata
```

## Changing VPS sources.list to Singapore servers

```shell
sed -i -e 's=http.us=ftp.sg=g' /etc/apt/sources.list
```

## Update/upgrade

```shell
sudo apt update && sudo apt upgrade -y
```

## Installing basic packages

Some of these packages are often missing. You may install all of these or just select those you need:
```shell
sudo apt install sudo
sudo apt install curl
sudo apt install ufw
sudo apt install unattended-upgrades
sudo apt install git
sudo apt install python3-venv
```

## Setting up UFW

```shell
sudo ufw allow ssh
sudo ufw enable
```