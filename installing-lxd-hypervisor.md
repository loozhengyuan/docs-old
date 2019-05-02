# Installing LXD Hypervisor

## Installation

Install snapd package
```shell
sudo apt update -y && sudo apt upgrade -y
sudo apt install snapd -y
```

Install lxd package via snap
```shell
sudo snap install lxd
```

Add user to lxd group
```shell
sudo usermod -a -G lxd <user>
```

## Initial Configuration

```shell
lxd init
```

## Basic Administration

To create your first container, simple run the following command. Note that you can leave the container_name optional if you like.
```shell
lxc launch images:debian/stretch/amd64 <container_name>
```

To view a list of containers you have:
```shell
lxc list
```

To access your container:
```shell
lxc exec <container_name> -- /bin/bash
```

To stop your container:
```shell
lxc stop <container_name>
```

To delete your container:
```shell
lxc delete <container_name>
```


## Relevant sources:
https://linuxcontainers.org/lxd/getting-started-cli/
