# Installing ZFS on Linux \(WIP\)

## Installation

WIP

#### See Also: [https://docs.oracle.com/cd/E23824\_01/html/821-1448/gavwn.html\#scrolltoc](https://docs.oracle.com/cd/E23824_01/html/821-1448/gavwn.html#scrolltoc)

## Post-Installation Configuration

### Ensure `zfs-import-scan.service` is enabled

It is not ascertained why but this service could sometimes be left un-enabled. This meant that ZFS cannot mount your zpools at boot time. To ensure that ZFS can mount your pools and datasets, ensure that the following commands are ran.

```bash
sudo systemctl start zfs-import-scan.service
sudo systemctl enable zfs-import-scan.service
```

## Best Practices

WIP

#### See Also: [http://open-zfs.org/wiki/Performance\_tuning ](http://open-zfs.org/wiki/Performance_tuning#Dataset_recordsize)[https://www.svennd.be/tuning-of-zfs-module/](https://www.svennd.be/tuning-of-zfs-module/)

#### [https://jrs-s.net/category/open-source/zfs/](https://jrs-s.net/category/open-source/zfs/)



## References:

[https://github.com/zfsonlinux/zfs/wiki](https://github.com/zfsonlinux/zfs/wiki)

