# Installing ZFS on Linux \(WIP\)

## Installation

WIP

## Post-Installation Configuration

### Ensure `zfs-import-scan.service` is enabled

It is not ascertained why but this service could sometimes be left un-enabled. This meant that ZFS cannot mount your zpools at boot time. To ensure that ZFS can mount your pools and datasets, ensure that the following commands are ran.

```bash
sudo systemctl start zfs-import-scan.service
sudo systemctl enable zfs-import-scan.service
```

## References:

[https://github.com/zfsonlinux/zfs/wiki](https://github.com/zfsonlinux/zfs/wiki)

